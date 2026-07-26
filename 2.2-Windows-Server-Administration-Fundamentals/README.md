# 2.2 Windows Server Administration Fundamentals

**Status:** Completed

**Platform:** Oracle VirtualBox

**Technology:** Microsoft Windows Server 2025 Standard Evaluation

**Domain:** Microsoft Infrastructure Administration

---

# Overview

This laboratory builds upon the validated Windows Server infrastructure established in **Lab 2.1** and introduces the core administrative capabilities required for enterprise Windows Server environments.

The focus of this laboratory was to develop practical experience using PowerShell to inspect, validate and manage key operating system components before introducing Active Directory services.

The laboratory also reinforces the relationship between Windows Server administration and enterprise security by examining processes, services, local identities, networking, event logging and firewall configuration.

These administration skills provide the operational foundation for future laboratories involving:

- Active Directory Domain Services
- Organisational Units
- User and Group administration
- Group Policy
- PowerShell automation
- Windows Server security
- Identity management

---

# Objective

The objectives of this laboratory were:

- Explore Windows Server administration using PowerShell
- Validate core operating system components
- Inspect running processes and services
- Verify network configuration and connectivity
- Review local users and security groups
- Examine Windows Event Logs
- Validate Windows Firewall configuration
- Confirm readiness for Active Directory deployment
- Document administrative evidence using PowerShell

---

# Environment

## Virtual Infrastructure

| Component | Configuration |
|------------|---------------|
| Virtualisation Platform | Oracle VirtualBox |
| Host Operating System | Windows 11 |
| Guest Operating System | Windows Server 2025 Standard Evaluation |
| Administration Method | Windows PowerShell |

## Server Configuration

| Attribute | Value |
|------------|-------|
| Computer Name | WIN-URRN4NJRE9I |
| Operating System | Windows Server 2025 Standard Evaluation |
| OS Build | 26100 |
| IPv4 Address | 10.0.2.15 |
| Default Gateway | 10.0.2.2 |
| Domain Status | WORKGROUP |

---

# Implementation

The existing Windows Server virtual machine created during Lab 2.1 was used as the foundation for this laboratory.

Rather than introducing additional infrastructure roles, the focus was placed on understanding the Windows Server operating system itself.

Administrative tasks were performed exclusively through PowerShell to reinforce command-line administration and evidence collection.

The laboratory covered:

- System information
- Process management
- Service management
- Network validation
- Remote management
- Local identity management
- Windows feature inspection
- Event log analysis
- Firewall validation

---

# PowerShell Administration

## Validate Server Identity

### Command

```powershell
Get-ComputerInfo | Select-Object CsName, WindowsProductName, OsBuildNumber, CsDomain
```

### Output

```text
CsName              : WIN-URRN4NJRE9I
WindowsProductName  : Windows Server 2025 Standard Evaluation
OsBuildNumber       : 26100
CsDomain            : WORKGROUP
```

### Interpretation

The command confirmed:

- Correct server identity
- Operating system version
- Windows build number
- Current domain membership

The server remains in a standalone **WORKGROUP** configuration, which is expected before deploying Active Directory Domain Services.

---

## Retrieve Running Processes

### Command

```powershell
Get-Process | Select-Object -First 10
```

### Output

```text
Handles  NPM(K) PM(K)   WS(K)    CPU(s)   Id  SI ProcessName
-------  ------ -----   -----    ------   --  -- -----------
131      8      2156     9928     0.30   3812 0  AggregatorHost
236      16     3772    20244     0.09   6020 1  AzureArcSysTray
138      9      1432    11072     0.03   1856 1  conhost
478      20     1996     7328     0.84    572 0  csrss
488      21     7140    30608     0.36   4212 1  ctfmon
886      63    51888   159392    28.86    816 1  dwm
2451     68    36880   374164     7.75   4280 1  explorer
```

### Interpretation

The command displayed the currently running processes.

Process inspection allows administrators to:

- Verify system activity
- Troubleshoot applications
- Monitor resource utilisation
- Detect abnormal or malicious processes

---

## Validate Explorer Process

### Command

```powershell
Get-Process -Name explorer
```

### Output

```text
Handles  NPM(K) PM(K)   WS(K)    CPU(s)   Id  SI ProcessName
-------  ------ -----   -----    ------   --  -- -----------
2451     68     36956   374232     7.75   4280 1  explorer
```

### Interpretation

The Windows graphical shell (**explorer.exe**) was confirmed to be running normally.

Validating essential processes helps distinguish expected operating system behaviour from potential operational or security issues.

---

## Identify Process Owner

### Command

```powershell
Get-Process -Name explorer -IncludeUserName
```

### Output

```text
Handles   WS(K)   CPU(s)   Id   UserName                              ProcessName
-------   -----   ------   --   --------                              -----------
2510     374848    7.92   4280  WIN-URRN4NJRE9I\Administrator          explorer
```

### Interpretation

The Explorer process was running under the local **Administrator** account.

Understanding process ownership is important when:

- Investigating suspicious activity
- Analysing privilege usage
- Performing forensic analysis
- Validating administrative context

---

# Service Management

## Validate Windows Event Log Service

### Command

```powershell
Get-Service -Name EventLog
```

### Output

```text
Status   Name       DisplayName
------   ----       -----------
Running  EventLog   Windows Event Log
```

### Interpretation

The Windows Event Log service was confirmed to be running.

This service is fundamental to Windows administration because it records operating system, application and security events that support troubleshooting and security monitoring.

---

## Review Running Services

### Command

```powershell
Get-Service | Where-Object {$_.Status -eq "Running"} | Select-Object -First 10
```

### Output

```text
Status   Name               DisplayName
------   ----               -------------------------------
Running  Appinfo            Application Information
Running  AppReadiness       App Readiness
Running  AudioEndpointBuilder Windows Audio Endpoint Builder
Running  Audiosrv           Windows Audio
Running  BFE                Base Filtering Engine
Running  BrokerInfrastructure Background Tasks Infrastructure Service
Running  camsvc             Capability Access Manager Service
Running  cbdhsvc_42579      Clipboard User Service
Running  CDPSvc             Connected Devices Platform Service
Running  CDPUserSvc_42579   Connected Devices Platform User Service
```

### Interpretation

The command listed active Windows services.

Service management is essential for:

- System troubleshooting
- Performance monitoring
- Security hardening
- Detecting unnecessary or unexpected services

---

# Network Validation

## Review Network Configuration

### Command

```powershell
Get-NetIPConfiguration
```

### Output

```text
InterfaceAlias       : Ethernet
InterfaceIndex       : 4
InterfaceDescription : Intel(R) PRO/1000 MT Desktop Adapter
NetProfile.Name      : Network
IPv6Address          : fd17:625c:f037:2:40e6:cb50:71a5:3696
IPv4Address          : 10.0.2.15
IPv6DefaultGateway   : fe80::2
IPv4DefaultGateway   : 10.0.2.2
DNSServer            : 1.1.1.1
                       8.8.8.8
```

### Interpretation

The network configuration was successfully validated.

The server is configured with:

- IPv4 address: **10.0.2.15**
- Default gateway: **10.0.2.2**
- Public DNS servers:
  - 1.1.1.1
  - 8.8.8.8

The configuration confirms that the virtual machine is correctly connected to the VirtualBox NAT network.

---

## Test Internet Connectivity

### Command

```powershell
Test-NetConnection 8.8.8.8
```

### Output

```text
ComputerName           : 8.8.8.8
RemoteAddress          : 8.8.8.8
InterfaceAlias         : Ethernet
SourceAddress          : 10.0.2.15
PingSucceeded          : True
PingReplyDetails (RTT) : 16 ms
```

### Interpretation

The server successfully communicated with an external host.

This confirms:

- Network connectivity
- Correct IP configuration
- Functional routing
- Internet access

Reliable network connectivity is essential before deploying enterprise infrastructure services.

---

# PowerShell Security Validation

## Review Execution Policy

### Command

```powershell
Get-ExecutionPolicy -List
```

### Output

```text
        Scope ExecutionPolicy
        ----- ---------------
MachinePolicy       Undefined
UserPolicy          Undefined
Process             Undefined
CurrentUser         Undefined
LocalMachine        RemoteSigned
```

### Interpretation

The LocalMachine execution policy is configured as **RemoteSigned**.

This configuration allows locally created PowerShell scripts to run while requiring downloaded scripts to be signed, providing a balance between administrative flexibility and security.

---

# Remote Administration Validation

## Verify WinRM Service

### Command

```powershell
Get-Service WinRM
```

### Output

```text
Status   Name    DisplayName
------   ----    -----------
Running  WinRM   Windows Remote Management (WS-Management)
```

### Interpretation

Windows Remote Management (WinRM) is operational.

WinRM enables secure remote administration using PowerShell and forms the foundation for many enterprise management and automation solutions.

---

# Local Identity Management

## Review Local Users

### Command

```powershell
Get-LocalUser
```

### Output

```text
Name               Enabled
----               -------
Administrator      True
DefaultAccount     False
Guest              False
WDAGUtilityAccount False
```

### Interpretation

The server contains the default local Windows accounts.

The built-in **Administrator** account is enabled and is currently being used for system administration.

Understanding local accounts is fundamental before introducing domain-based identity management through Active Directory.

---

## Review Local Groups

### Command

```powershell
Get-LocalGroup
```

### Output

```text
Name
----
Administrators
Backup Operators
Cryptographic Operators
Distributed COM Users
Event Log Readers
Guests
Hyper-V Administrators
Performance Log Users
Performance Monitor Users
Power Users
Remote Desktop Users
Remote Management Users
Users
```

### Interpretation

Windows local groups provide permission boundaries for administrative and standard users.

Understanding these groups is essential for implementing:

- Least privilege
- Role-based administration
- Secure access control
- Identity management

---

# Active Directory Readiness Validation

## Test Active Directory Module Availability

### Command

```powershell
Get-ADDomain
```

### Output

```text
Get-ADDomain : The term 'Get-ADDomain' is not recognized as the name of a cmdlet,
function, script file, or operable program.
```

### Interpretation

The Active Directory PowerShell module is not currently installed.

This confirms that:

- Active Directory Domain Services have not yet been deployed.
- The server remains in a standalone WORKGROUP configuration.
- The environment is ready for future Active Directory installation.

---

## Review Installed Windows Features

### Command

```powershell
Get-WindowsFeature
```

### Output (Summary)

```text
Installed

[X] File and Storage Services
[X] Microsoft Defender Antivirus
[X] .NET Framework 4.8 Features
[X] Windows Admin Center Setup
[X] Windows PowerShell

Available

[ ] Active Directory Domain Services
[ ] DNS Server
[ ] DHCP Server
[ ] Group Policy Management
[ ] Hyper-V
[ ] Web Server (IIS)
```

### Interpretation

The server currently contains the core components required for Windows administration.

Active Directory Domain Services is available but has not yet been installed, confirming that the environment is ready for the next stage of the infrastructure learning pathway.

---

# Event Viewer and Monitoring

## Review Recent System Events

### Command

```powershell
Get-EventLog -LogName System -Newest 5
```

### Output

```text
Information  Service Control Manager
The Microsoft Passport Container service entered the stopped state.

Information  Service Control Manager
The WaaSMedicSvc service entered the stopped state.

Information  Service Control Manager
The Network Setup Service entered the running state.

Information  Service Control Manager
The Software Protection service entered the stopped state.

Information  Service Control Manager
The AppX Deployment Service entered the running state.
```

### Interpretation

Recent system events primarily recorded normal service state changes.

Reviewing these events helps administrators understand normal operating system behaviour and establish a baseline for future troubleshooting and security monitoring.

---
