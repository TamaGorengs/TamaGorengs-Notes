# WMI and WinRM

Create [PSCredential](https://learn.microsoft.com/en-us/powershell/scripting/learn/deep-dives/add-credentials-to-powershell-functions?view=powershell-7.4\&viewFallbackFrom=powershell-7.2) to store Username and Password

```
$username = 'jen';
$password = 'Nexus123!';
$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $secureString;
```

Create _Common Information Model_ (CIM) via the [**New-CimSession**](https://docs.microsoft.com/en-us/powershell/module/cimcmdlets/new-cimsession?view=powershell-7.2)

```
$options = New-CimSessionOption -Protocol DCOM
$session = New-Cimsession -ComputerName 192.168.50.73 -Credential $credential -SessionOption $Options 
$command = 'calc';
```

```
Invoke-CimMethod -CimSession $Session -ClassName Win32_Process -MethodName Create -Arguments @{CommandLine =$Command};
```
