# Leveraging Windows Services

## Service Binary Hijacking

## REMAKE

```
Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}
```

#### icacls

| Mask | Permissions             |   |
| ---- | ----------------------- | - |
| F    | Full access             |   |
| M    | Modify access           |   |
| RX   | Read and execute access |   |
| R    | Read-only access        |   |
| W    | Write-only access       |   |

```
icacls "C:\xampp\apache\bin\httpd.exe"
```

```
Get-CimInstance -ClassName win32_service | Select Name, StartMode | Where-Object {$_.Name -like 'mysql'}
```



## DLL Hijacking



## Unquoted Service Path
