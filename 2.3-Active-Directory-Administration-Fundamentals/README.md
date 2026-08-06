# 2.3 Active Directory Administration Fundamentals

Status: Completed

Platform: Oracle VirtualBox

Technology: Microsoft Windows Server 2025 Standard Evaluation

Domain: Microsoft Infrastructure Administration | Identity and Access Management

---

# Overview

This laboratory introduces Active Directory Domain Services (AD DS), the foundation of traditional Microsoft enterprise identity infrastructure.

Building upon the Windows Server administration knowledge developed in previous laboratories, this exercise transitions from standalone server administration into enterprise identity management.

The objective was to deploy and validate an on-premises Active Directory environment using Windows Server 2025 and PowerShell-based administration.

The laboratory demonstrates the relationship between:

- Windows Server
- Active Directory Domain Services
- Domain Controllers
- DNS
- Authentication
- Authorisation
- Identity management
- Access control

Modern Microsoft environments commonly combine:

- Active Directory Domain Services (on-premises identity)
- Microsoft Entra ID (cloud identity)
- Microsoft 365
- Microsoft Azure
- Security monitoring solutions

Understanding Active Directory remains essential for identity and security professionals because many enterprise authentication and authorisation systems still depend on Active Directory infrastructure.

---

# Objectives

The objectives of this laboratory were:

- Install Active Directory Domain Services
- Promote Windows Server to a Domain Controller
- Create a new Active Directory forest
- Configure Active Directory-integrated DNS
- Validate domain creation
- Validate forest creation
- Confirm Domain Controller functionality
- Verify FSMO role ownership
- Validate Active Directory services
- Test DNS functionality
- Explore default Active Directory users and groups
- Review security-related server configuration
- Collect PowerShell evidence for documentation

---

# Environment

## Virtual Infrastructure

| Component | Configuration |
|---|---|
| Virtualisation Platform | Oracle VirtualBox |
| Host Operating System | Windows 11 |
| Guest Operating System | Windows Server 2025 Standard Evaluation |
| Administration Method | Windows PowerShell |
| Server Name | WIN-URRN4NJRE9I |

---

# Starting Configuration

Before this laboratory, the Windows Server environment was operating as a standalone server.

Initial state:

```text
Operating System:
Windows Server 2025 Standard Evaluation

Computer Name:
WIN-URRN4NJRE9I

Domain Membership:
WORKGROUP
```

The server had already completed the previous administration validation stage.

The environment was ready for the introduction of enterprise identity services.

---

# Administration Methodology

This laboratory followed an evidence-based administration workflow.

Every configuration change followed:

```text
Execute
   ↓
Validate
   ↓
Interpret
   ↓
Document
```

PowerShell was used as the primary administration and evidence collection method.

This reflects enterprise administration practices where infrastructure changes must be:

- repeatable
- auditable
- validated
- documented

---

# Environment Validation

## Confirm PowerShell Version

Before performing Active Directory administration, the PowerShell environment was validated.

Command:

```powershell
$PSVersionTable.PSVersion
```

Output:

```text
Major  Minor  Build  Revision
-----  -----  -----  --------
5      1      26100  7462
```

Interpretation:

Windows PowerShell 5.1 was confirmed.

This was important because Windows Server administration modules such as:

- ServerManager
- Active Directory PowerShell module

are designed for Windows PowerShell.

---

# Confirm Server Identity

Command:

```powershell
Get-ComputerInfo | Select-Object WindowsProductName, CsName
```

Output:

```text
WindowsProductName                      CsName
------------------                      ------
Windows Server 2025 Standard Evaluation WIN-URRN4NJRE9I
```

Interpretation:

The command confirmed that administration was being performed inside the Windows Server virtual machine.

This validation prevented a previous administrative issue where commands were accidentally executed on the Windows 11 host.

---

# Active Directory Domain Services Installation

## Verify AD DS Availability

Before installation, the availability of the Active Directory Domain Services role was confirmed.

Command:

```powershell
Get-WindowsFeature AD-Domain-Services
```

Output:

```text
Display Name                         Name                    Install State
------------                         ----                    -------------
Active Directory Domain Services     AD-Domain-Services      Available
```

Interpretation:

The AD DS role was available but not installed.

The server was ready for Active Directory deployment.

---

# Install Active Directory Domain Services

Command:

```powershell
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
```

Output:

```text
Success Restart Needed Exit Code Feature Result
------- -------------- --------- --------------
True    No             Success   Active Directory Domain Services
```

Interpretation:

Active Directory Domain Services and management tools were successfully installed.

At this stage:

- AD DS binaries were installed
- Administrative tools were available
- The server was not yet a Domain Controller

The next step was Domain Controller promotion.

---

# Domain Controller Promotion

A new Active Directory forest was created using:

```powershell
Install-ADDSForest -DomainName "corp.lab"
```

Configuration:

```text
Domain:
corp.lab

Forest:
corp.lab
```

The promotion process configured:

- Active Directory Domain Services
- Domain Controller functionality
- DNS Server integration
- Global Catalog
- SYSVOL
- Authentication services

The server restarted automatically after successful completion.

---

# Active Directory Architecture Created

The resulting architecture:

```text
Windows Server 2025

        |
        |
Domain Controller

        |
        |
Active Directory Forest

        |
        |
corp.lab Domain

        |
        |
DNS Integration

        |
        |
Identity Authentication
```

The server transitioned from:

```text
Standalone Server
(WORKGROUP)
```

to:

```text
Enterprise Identity Infrastructure
(Domain Controller)
```

---

# Initial Validation After Promotion

After reboot, the authentication environment changed.

The server login displayed:

```text
CORP\Administrator
```

This confirmed that:

- the domain was created
- the administrator account existed in Active Directory
- authentication was now domain-based

---

# Domain Validation

Command:

```powershell
Get-ADDomain
```

Output:

```text
DNSRoot        : corp.lab
DomainMode     : Windows2025Domain
Forest         : corp.lab
DomainControllersContainer :
OU=Domain Controllers,DC=corp,DC=lab
```

Interpretation:

The command confirmed:

- domain creation
- domain naming
- Active Directory database structure
- Windows Server 2025 domain functionality

The Active Directory domain was successfully created.

# Forest Validation

After validating the domain, the Active Directory forest configuration was verified.

Command:

```powershell
Get-ADForest
```

Output:

```text
ApplicationPartitions : {DC=DomainDnsZones,DC=corp,DC=lab,
                         DC=ForestDnsZones,DC=corp,DC=lab}

Domains               : {corp.lab}

ForestMode            : Windows2025Forest

GlobalCatalogs        : {WIN-URRN4NJRE9I.corp.lab}

Name                  : corp.lab

RootDomain            : corp.lab

SchemaMaster          : WIN-URRN4NJRE9I.corp.lab
```

Interpretation:

The forest was successfully created.

Validation confirmed:

- Forest name: `corp.lab`
- Forest mode: `Windows2025Forest`
- Global Catalog availability
- Schema Master ownership
- Domain naming structure

The server successfully became the first Domain Controller in a new Active Directory forest.

---

# Domain Controller Validation

The Domain Controller configuration was validated.

Command:

```powershell
Get-ADDomainController
```

Output:

```text
ComputerObjectDN :
CN=WIN-URRN4NJRE9I,OU=Domain Controllers,DC=corp,DC=lab

Domain :
corp.lab

Forest :
corp.lab

HostName :
WIN-URRN4NJRE9I.corp.lab

IPv4Address :
10.0.2.15

IsGlobalCatalog :
True

IsReadOnly :
False

OperatingSystem :
Windows Server 2025 Standard Evaluation
```

Interpretation:

The server was successfully operating as a Domain Controller.

The validation confirmed:

- Correct domain membership
- Global Catalog enabled
- Writable Domain Controller
- Correct DNS hostname
- Correct operating system

---

# FSMO Role Validation

Active Directory relies on five Flexible Single Master Operations (FSMO) roles.

These roles provide specialised responsibilities within the domain and forest.

Command:

```powershell
netdom query fsmo
```

Output:

```text
Schema master               WIN-URRN4NJRE9I.corp.lab

Domain naming master        WIN-URRN4NJRE9I.corp.lab

PDC                         WIN-URRN4NJRE9I.corp.lab

RID pool manager            WIN-URRN4NJRE9I.corp.lab

Infrastructure master       WIN-URRN4NJRE9I.corp.lab
```

Interpretation:

All FSMO roles were assigned to the first Domain Controller.

This is expected in a single-domain laboratory environment.

Validated roles:

- Schema Master
- Domain Naming Master
- PDC Emulator
- RID Master
- Infrastructure Master

---

# Active Directory Service Validation

Critical Active Directory services were validated.

Command:

```powershell
Get-Service NTDS,DNS,Netlogon,KDC
```

Output:

```text
Status   Name      DisplayName

Running  DNS       DNS Server

Running  KDC       Kerberos Key Distribution Center

Running  Netlogon  Netlogon

Running  NTDS      Active Directory Domain Services
```

Interpretation:

Core identity infrastructure services were running successfully.

Validated components:

## NTDS

Active Directory database service.

Provides:

- directory storage
- object management
- authentication support

## DNS

Provides:

- name resolution
- Active Directory service discovery
- Domain Controller location

## Kerberos KDC

Provides:

- ticket-based authentication
- secure identity verification

## Netlogon

Provides:

- domain authentication communication
- secure channel management

---

# Active Directory Replication Validation

Replication health was checked.

Command:

```powershell
repadmin /replsummary
```

Output:

```text
Source DSA          largest delta    fails/total %

Destination DSA     largest delta    fails/total %
```

Interpretation:

No replication failures were reported.

Because this laboratory contains a single Domain Controller, there are no additional replication partners.

The validation confirms the Active Directory environment is healthy for the current topology.

---

# DNS Validation

DNS is a critical dependency for Active Directory.

Active Directory does not rely only on IP addresses.

It uses DNS records to locate:

- Domain Controllers
- LDAP services
- Kerberos services
- Global Catalog servers

---

# Hostname Resolution Validation

Command:

```powershell
nslookup WIN-URRN4NJRE9I.corp.lab
```

Output:

```text
Name:
WIN-URRN4NJRE9I.corp.lab

Addresses:

10.0.2.15

fd17:625c:f037:2:40e6:cb50:71a5:3696
```

Interpretation:

The Domain Controller hostname successfully resolved through DNS.

This confirmed:

- DNS registration
- Correct hostname resolution
- Domain Controller discoverability

---

# Active Directory Service Record Validation

Command:

```powershell
nslookup -type=SRV _ldap._tcp.dc._msdcs.corp.lab
```

Output:

```text
_ldap._tcp.dc._msdcs.corp.lab

SRV service location:

port = 389

svr hostname =
win-urrn4njre9i.corp.lab

internet address =
10.0.2.15
```

Interpretation:

The LDAP service record was successfully registered.

This record allows domain members to locate Domain Controllers automatically.

DNS is therefore a fundamental component of Active Directory functionality.

---

# Active Directory Users Validation

Default Active Directory users were reviewed.

Command:

```powershell
Get-ADUser -Filter * | Select-Object Name,SamAccountName
```

Output:

```text
Name          SamAccountName

Administrator Administrator

Guest         Guest

krbtgt        krbtgt
```

Interpretation:

The default Active Directory user objects were successfully created.

Important accounts:

## Administrator

Domain administrative account.

## Guest

Default limited account.

## krbtgt

Kerberos service account used for ticket generation.

---

# Active Directory Groups Validation

Default security groups were reviewed.

Command:

```powershell
Get-ADGroup -Filter * | Select-Object Name
```

Output summary:

```text
Domain Admins

Domain Users

Domain Computers

Domain Controllers

Enterprise Admins

Schema Admins

Administrators

DnsAdmins

Protected Users
```

Interpretation:

Active Directory security groups provide permission boundaries.

They support:

- role-based access control
- delegated administration
- least privilege principles

Understanding these groups is fundamental for identity security.

---

# Windows Firewall Validation

Firewall profiles were reviewed after Domain Controller deployment.

Command:

```powershell
Get-NetFirewallProfile
```

Output summary:

```text
Domain    Enabled : True

Private   Enabled : True

Public    Enabled : True
```

Interpretation:

Windows Firewall remained enabled across all profiles.

Maintaining firewall protection reduces unnecessary exposure and supports secure server operation.

---

# Security Event Validation

Windows Security events were reviewed.

Command:

```powershell
Get-WinEvent -LogName Security -MaxEvents 5 |
Select-Object TimeCreated,Id,LevelDisplayName,Message
```

Output:

```text
Event ID 4672
Special privileges assigned to new logon

Event ID 4624
An account was successfully logged on

Event ID 4634
An account was logged off
```

Interpretation:

Security auditing was functioning correctly.

The events demonstrate:

- authentication activity
- privileged logons
- security telemetry generation

These logs provide the foundation for future SIEM and detection engineering workflows.

---

# Final Server Validation

Command:

```powershell
Get-ComputerInfo |
Select-Object WindowsProductName,CsName,WindowsVersion
```

Output:

```text
WindowsProductName                      CsName

Windows Server 2025 Standard Evaluation WIN-URRN4NJRE9I
```

Interpretation:

The server remained correctly configured after Active Directory deployment.

---

# Evidence Collection

Evidence collected during this laboratory consists primarily of PowerShell outputs.

The recommended repository structure:

```text
2.3-Active-Directory-Administration-Fundamentals

│
├── README.md
│
└── Evidence
    │
    └── PowerShell
        │
        ├── powershell-version.txt
        ├── server-validation.txt
        ├── ad-ds-installation.txt
        ├── domain-validation.txt
        ├── forest-validation.txt
        ├── domain-controller-validation.txt
        ├── fsmo-validation.txt
        ├── services-validation.txt
        ├── replication-validation.txt
        ├── dns-validation.txt
        ├── users-validation.txt
        ├── groups-validation.txt
        ├── firewall-validation.txt
        └── security-events.txt
```

The evidence demonstrates a reproducible administration process.

---

# Technical Findings

The Windows Server 2025 environment was successfully transformed from a standalone server into an Active Directory Domain Controller.

The laboratory confirmed:

- Successful AD DS installation
- Successful forest creation
- Successful domain creation
- DNS integration
- Domain Controller operation
- FSMO role assignment
- Active Directory service health
- LDAP service discovery
- Default identity objects
- Security event generation
- Firewall protection

---

# Security Relevance

Although this laboratory focused on infrastructure administration, each component has direct security relevance.

## Identity

Active Directory provides:

- authentication
- authorisation
- identity lifecycle management

## Authentication Security

Kerberos and domain authentication create the foundation for secure enterprise access.

## Monitoring

Windows Security Events provide telemetry used by:

- Security Operations Centres
- SIEM platforms
- Detection Engineering workflows

## Access Control

Groups and permissions support:

- least privilege
- administrative separation
- role-based access control

## Hybrid Identity

Understanding Active Directory is essential for environments integrating with:

- Microsoft Entra ID
- Microsoft 365
- Azure services

---

# Skills Demonstrated

This laboratory demonstrated:

- Windows Server administration
- Active Directory deployment
- Domain Controller configuration
- DNS validation
- PowerShell administration
- Identity management fundamentals
- Authentication concepts
- FSMO role validation
- Enterprise troubleshooting
- Security event analysis
- Evidence-based documentation

---

# Repository Context

This laboratory is part of:

## Phase 2 — Microsoft Infrastructure Administration Foundations

Learning progression:

```text
2.1 Windows Server Administration Foundations

        ↓

2.2 Windows Server Administration Fundamentals

        ↓

2.3 Active Directory Administration Fundamentals

        ↓

2.4 PowerShell Administration Fundamentals

        ↓

2.5 Windows Server Security Fundamentals
```

This phase develops the infrastructure knowledge required for:

- Microsoft Systems Administration
- Hybrid Identity
- Microsoft Entra ID integration
- Cloud Administration
- Identity Security
- Detection Engineering

---

# Conclusion

This laboratory successfully introduced Active Directory Domain Services into the Windows Server environment.

The server was transformed from a standalone WORKGROUP machine into an enterprise identity infrastructure component.

Through PowerShell-based validation, the laboratory demonstrated how Windows Server, Active Directory and DNS operate together to provide authentication and identity management capabilities.

This foundation supports future work involving:

- Organisational Units
- Group Policy
- User administration
- Hybrid Identity
- Microsoft Entra ID integration
- Identity security monitoring

The laboratory establishes the core infrastructure knowledge required for modern Microsoft identity and security-focused roles.
