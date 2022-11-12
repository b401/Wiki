---
title: mitm6
date: M11-02-2022
author: i4
---
## The mitm6 attack

### Attack phase 1 – Primary DNS takeover


/mitm6/ starts with listening on the primary interface of the attacker machine for Windows clients requesting an IPv6 configuration via DHCPv6. By default every Windows machine since Windows Vista will request this configuration regularly. This can be seen in a packet capture from Wireshark:
![](/images/w/mitm6/dhcpv6_cropped.png)

mitm6 will reply to those DHCPv6 requests, assigning the victim an IPv6 address within the link-local range. While in an actual IPv6 network these addresses are auto-assigned by the hosts themselves and do not need to be configured by a DHCP server, this gives us the opportunity to set the attackers IP as the default IPv6 DNS server for the victims. It should be noted that mitm6 currently only targets Windows based operating systems, since other #operating systems like macOS and Linux do not use DHCPv6 for DNS server assignment#.

### Attack phase 2 – DNS spoofing

On the victim machine we see that our server is configured as DNS server. Due to the preference of Windows regarding IP protocols, the IPv6 DNS server will be preferred to the IPv4 DNS server. The IPv6 DNS server will be used to query both for A (IPv4) and AAAA (IPv6) records.

By running `ipconfig /all` you'll see that Windows added the new ipv6 dns.

![](/images/w/mitm6/ipconfig_fixed.png)


Now our next step is to get clients to connect to the attacker machine instead of the legitimate servers.
Our end goal is getting the user or browser to automatically authenticate to the attacker machine, which is why we are spoofing URLs in the internal domain testsegment.local. On the screenshot in step 1 you see the client started requesting information about wpad.testsegment.local immediately after it was assigned an IPv6 address. This is the part we will be exploiting during this attack.

### Exploiting WPAD

If **unpatched**:

1. Asks DNS for wpad.dat
2. if none ask over LLMNR/NETBIOS

Since 2016 (!(MS-16-077)[https://support.microsoft.com/en-us/help/3165191/ms16-077-security-update-for-wpad-june-14--2016 "MS-16-077"]- The location is no longer requested via broadcast protocols, only via DNS.
- Authentication does not occur automatically anymore even if this is requested.

### Exploiting WPAD post MS16-077
The first protection, where WPAD is only requested via DNS, can be easily bypassed with mitm6. As soon as the victim machine has set the attacker as IPv6 DNS server, it will start querying for the WPAD configuration of the network. Since these DNS queries are sent to the attacker, it can just reply with its own IP address (either IPv4 or IPv6 depending on what the victim’s machine asks for). This also works if the organization is already using a WPAD file (though in this case it will break any connections from reaching the internet).

To bypass the second protection, where credentials are no longer provided by default, we need to do a little more work. When the victim requests a WPAD file we won’t request authentication, but instead provide it with a valid WPAD file where the attacker’s machine is set as a proxy. When the victim now runs any application that uses the Windows API to connect to the internet or simply starts browsing the web, it will use the attackers machine as a proxy. This works in Edge, Internet Explorer, Firefox and Chrome, since they all respect the WPAD system settings by default.
Now when the victim connects to our “proxy” server, which we can identify by the use of the CONNECT HTTP verb, or the presence of a full URI after the GET verb, we reply with a HTTP 407 Proxy Authentication required. This is different from the HTTP code normally used to request authentication, HTTP 401.
IE/Edge and Chrome (which uses IEs settings) will automatically authenticate to the proxy, even on the latest Windows versions. In Firefox this setting can be configured, but it is enabled by default.



### The full attack
The previous sections described the global idea behind the attack. Running the attack itself is quite straightforward. First we start mitm6, which will start replying to DHCPv6 requests and afterwards to DNS queries requesting names in the internal network. For the second part of our attack, we use our favorite relaying tool `impacket-ntlmrelayx`.

To serve the WPAD file, all we need to add to the command prompt is the host is the -wh parameter and with it specify the host that the WPAD file resides on. Since mitm6 gives us control over the DNS, #any non-existing hostname# in the victim network will do. To make sure ntlmrelayx listens on both IPv4 and IPv6, use the -6 parameter. The screenshots below show both tools in action, mitm6 selectively spoofing DNS replies and ntlmrelayx serving the WPAD file and then relaying authentication to other servers in the network.

```
impacket-ntlmrelayx -6 -wh anyhostname.domain.com -t smb://{ip for smb relay} -l ~/tmp -socks
```


```
    -6 ipv6
    -wh WPAD_HOST, --wpad-host WPAD_HOST
                        Enable serving a WPAD file for Proxy Authentication
                        attack, setting the proxy host to the one supplied.
    -l LOOTDIR, --lootdir LOOTDIR
                        Loot directory in which gathered loot such as SAMl 
    -t TARGET, --target TARGET
                        Target to relay the credentials to, can be an IP,
                        hostname or URL like domain\username@host:port
                        (domain\username and port are optional, and don't
                        forget to escape the '\'). If unspecified, it will
                        relay back to the client')
  -socks                Launch a SOCKS proxy for the connection relayed

```
![](/images/w/mitm6/ntlmrelay_final.png)
![](/images/w/mitm6/mitm6_cropped.png)


### Defense
The only defense against this attack that we are currently aware of is disabling IPv6 if it is not used on your internal network. This will stop Windows clients querying for a DHCPv6 server and make it impossible to take over the DNS server with the above described method.
For the WPAD exploit, the best solution is to disable the Proxy Auto detection via Group Policy. If your company uses a proxy configuration file internally (PAC file) it is recommended to explicitly configure the PAC url instead of relying on WPAD to detect it automatically.
While writing this blog, Google Project Zero also discovered vulnerabilities in WPAD, and their blog post mentions that disabling the `WinHttpAutoProxySvc` is the only reliable way that in their experience disabled WPAD.


