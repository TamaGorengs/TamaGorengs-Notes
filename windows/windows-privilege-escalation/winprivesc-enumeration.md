# WinPrivEsc Enumeration

## Situational Awareness

* After compromising a machine, this the information we need to gather that can help us to Privilege Escalate

```
- Username and hostname
- Group memberships of the current user
- Existing users and groups
- Operating system, version and architecture
- Network information
- Installed applications
- Running processes
```

### Enumerate User

Using CMD

```
whoami
whoami /groups
whoami /priv
```

Using Powershell

```
Get-LocalUser
Get-LocalGroup
Get-LocalGroupMember $user
Get-LocalGroup $groupname
```

### Enumerate Hostname

```
systeminfo
```

### Enumerate Network

```
ipconfig /all
route print

netstat -ano
-a : display all active TCP Connections
-n : disable name resolution
-o : show process id
```

### Installed Application

```
Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname

Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
```

### Process

```
Get-Process

```

## Hidden in Plain View

* User might put Information in open directory that anyone can access

## PowerShell History

* We can get interesting information from PowerShell

Get list of command executed in the past

```
Get-History
```

Clear-History just clear PowerShell own history but not PSReadline. We can grab it using below command.

```
(Get-PSReadlineOption).HistorySavePath
```

## Automated Tools

* winPeas
* PowerView
* JAWS
