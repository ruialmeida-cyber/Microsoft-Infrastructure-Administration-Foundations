# 2.2 Windows Server Administration Fundamentals

## Windows Server 2025 Administration and PowerShell Validation

**Status:** Completed

**Platform:** Oracle VirtualBox

**Technology:** Microsoft Windows Server 2025 Standard Evaluation

**Domain:** Microsoft Infrastructure Administration

---

# Overview

This laboratory develops practical Windows Server administration skills following the completion of Lab 2.1 — Windows Server Administration Foundations.

The objective was to validate and explore the Windows Server operating system layer that supports enterprise identity, administration, and security operations.

The laboratory focused on:

- PowerShell administration
- System information retrieval
- Process management
- Service management
- Event log analysis
- Network validation
- Local user and group management
- Firewall configuration review

This laboratory establishes the operational foundation required before introducing:

- Active Directory Domain Services
- Group Policy
- Enterprise identity management
- Security monitoring
- Microsoft Entra hybrid identity

---

# Objectives

The objectives of this laboratory were:

- Develop practical Windows Server administration skills using PowerShell
- Validate Windows Server configuration
- Understand running processes and services
- Review local identity management
- Analyse Windows event logs
- Validate network configuration
- Review firewall security settings
- Collect evidence for technical documentation

---

# Environment and Configuration

## Virtual Infrastructure

| Component | Configuration |
|---|---|
| Virtualisation Platform | Oracle VirtualBox |
| Host Operating System | Windows 11 |
| Guest Operating System | Windows Server 2025 Standard Evaluation |
| Administration Method | PowerShell |

---

## Server Configuration

| Attribute | Value |
|---|---|
| Computer Name | WIN-URRN4NJRE9I |
| Operating System | Windows Server 2025 Standard Evaluation |
| OS Build | 26100 |
| IPv4 Address | 10.0.2.15 |
| Default Gateway | 10.0.2.2 |
| Network Mode | VirtualBox NAT |
| Domain Status | WORKGROUP |

---

# Implementation

The Windows Server 2025 environment created during Lab 2.1 was used as the foundation for administration exercises.

The laboratory continued with PowerShell-based validation and administration tasks.

Activities performed:

- Retrieved system information
- Validated running processes
- Identified process ownership
- Checked Windows services
- Analysed event logs
- Reviewed local users and groups
- Confirmed network connectivity
- Reviewed firewall configuration

---

# Validation Methodology

PowerShell was used as the primary administration and validation interface.

The objective was to understand how Windows Server administrators retrieve system information, troubleshoot issues, and verify operational status.

Validation focused on:

- Server identity
- Operating system state
- Administrative context
- Service availability
- Network connectivity
- Local identity configuration
- Security controls

---

# PowerShell Administration Validation

## System Information Validation

### Command

```powershell
Get-ComputerInfo | Select-Object CsName, WindowsProductName, OsBuildNumber, CsDomain
Output
CsName              : WIN-URRN4NJRE9I
WindowsProductName  : Windows Server 2025 Standard Evaluation
OsBuildNumber       : 26100
CsDomain            : WORKGROUP
Interpretation

The server identity and operating system configuration were successfully validated.

The server remains a standalone WORKGROUP machine, which is expected before Active Directory deployment.

Process Management
Retrieve Running Processes
Command
Get-Process | Select-Object -First 10
Output
ProcessName
-----------
AggregatorHost
AzureArcSysTray
conhost
csrss
ctfmon
dwm
explorer
Interpretation

The command demonstrated process enumeration and provided visibility into active system processes.

Process analysis is important for:

Troubleshooting
Performance analysis
Security investigations
Process Ownership Validation
Command
Get-Process -Name explorer -IncludeUserName
Output
UserName

WIN-URRN4NJRE9I\Administrator
Interpretation

The process owner was confirmed as the local Administrator account.

Understanding process ownership supports privilege analysis and incident investigation.

Service Management
Windows Event Log Service Validation
Command
Get-Service -Name EventLog
Output
Status   Name      DisplayName
------   ----      -----------
Running  EventLog  Windows Event Log
Interpretation

The Windows Event Log service was operational.

Reliable event logging is essential for:

Security monitoring
SIEM ingestion
Detection engineering
Running Services Review
Command
Get-Service | Where-Object {$_.Status -eq "Running"} | Select-Object -First 10
Output
Status   Name

Running  Appinfo
Running  AppReadiness
Running  AudioEndpointBuilder
Running  Audiosrv
Running  BFE
Running  BrokerInfrastructure
Running  camsvc
Running  CDPSvc
Interpretation

The command provided visibility into active Windows services.

Service management is important for:

System reliability
Troubleshooting
Attack surface reduction
Network Validation
Network Configuration
Command
Get-NetIPConfiguration
Output
InterfaceAlias : Ethernet

IPv4Address : 10.0.2.15

IPv4DefaultGateway : 10.0.2.2

DNS Servers:
1.1.1.1
8.8.8.8
Interpretation

Network configuration was successfully validated.

The server has:

Correct IP addressing
Valid gateway configuration
Working DNS configuration
Connectivity Test
Command
Test-NetConnection 8.8.8.8
Output
ComputerName : 8.8.8.8

PingSucceeded : True

PingReplyDetails : 16 ms
Interpretation

External network connectivity was confirmed.

PowerShell Security Configuration
Execution Policy Review
Command
Get-ExecutionPolicy -List
Output
MachinePolicy Undefined
UserPolicy Undefined
Process Undefined
CurrentUser Undefined
LocalMachine RemoteSigned
Interpretation

The server uses the RemoteSigned execution policy.

This allows administrative scripting while providing protection against unsigned remote scripts.

Remote Administration Validation
WinRM Service
Command
Get-Service WinRM
Output
Status   Name
------   ----
Running  WinRM
Interpretation

Windows Remote Management is operational.

WinRM enables remote administration in enterprise environments.

Local Users and Groups
Local Users
Command
Get-LocalUser
Output
Administrator
DefaultAccount
Guest
WDAGUtilityAccount
Interpretation

The server contains default local accounts.

Local account management is a foundation for identity security.

Local Groups
Command
Get-LocalGroup
Output
Administrators
Users
Remote Desktop Users
Event Log Readers
Remote Management Users
Interpretation

Local groups define permission boundaries.

Understanding group membership supports:

Least privilege
Access control
Identity governance
Active Directory Readiness Validation
Active Directory Command Test
Command
Get-ADDomain
Output
Get-ADDomain : The term 'Get-ADDomain' is not recognized
Interpretation

The Active Directory PowerShell module is not installed.

This confirms:

Active Directory Domain Services are not deployed
The server remains a standalone server
Windows Server Roles Review
Command
Get-WindowsFeature
Output Summary
File and Storage Services        Installed

Microsoft Defender Antivirus     Installed

Windows PowerShell               Installed

Windows Admin Center Setup       Installed

Active Directory Domain Services Available
Interpretation

The server contains core administration components.

Active Directory remains available for future deployment.

Event Viewer and Monitoring
System Events
Command
Get-EventLog -LogName System -Newest 5
Output Summary
Service Control Manager events

Services entered running and stopped states.
Interpretation

Windows system logs provide visibility into operating system activity.

System Errors
Command
Get-EventLog -LogName System -EntryType Error -Newest 5
Output Summary
EventLog:
Previous system shutdown was unexpected.

DCOM:
Event ID 10005.
Interpretation

Historical errors were identified.

Event analysis is an essential skill for:

Troubleshooting
Monitoring
Security investigation
Storage Validation
Disk Configuration
Command
Get-Disk
Output
Number   Friendly Name

0        VBOX HARDDISK

HealthStatus:
Healthy

OperationalStatus:
Online

Size:
80 GB
Interpretation

The virtual disk was validated as healthy and operational.

Windows Firewall Validation
Firewall Profiles
Command
Get-NetFirewallProfile
Output Summary
Name       Enabled

Domain     True

Private    True

Public     True
Interpretation

Windows Firewall profiles are enabled.

Firewall controls reduce unnecessary network exposure and support system hardening.

Evidence Collection

Evidence collected during this laboratory consists of PowerShell validation outputs.

Repository structure:

Evidence/
└── PowerShell/

Collected evidence:

computerinfo.txt

processes.txt

explorer.txt

explorer-user.txt

services.txt

eventlog-service.txt

network-config.txt

network-test.txt

execution-policy.txt

winrm.txt

local-users.txt

local-groups.txt

windows-features.txt

event-system.txt

event-errors.txt

disk.txt

firewall-profile.txt
Technical Findings

The Windows Server 2025 environment was successfully validated.

Confirmed:

✓ Server identity
✓ Operating system configuration
✓ Network connectivity
✓ PowerShell administration capability
✓ Service operation
✓ Event logging capability
✓ Local identity configuration
✓ Firewall protection status

Operational Interpretation

This laboratory demonstrated that Windows Server administration is a critical foundation for enterprise identity and security operations.

The relationship between infrastructure and identity follows:

Windows Server

        ↓

Active Directory

        ↓

Microsoft Entra ID

        ↓

Identity Security

Reliable identity services require:

Correct infrastructure configuration
Effective administration
Monitoring capability
Security controls
Key Learning Outcome

This laboratory reinforced the importance of understanding the Windows operating system layer that supports enterprise identity platforms.

Skills developed:

Windows Server administration
PowerShell validation
Service management
Event analysis
Local identity management
Security-focused administration
Skills Demonstrated
Windows Server administration
PowerShell administration
Process analysis
Service troubleshooting
Network validation
Event log analysis
Local user and group management
Firewall validation
Evidence-based documentation
Repository Context

This laboratory is part of:

Phase 2 — Microsoft Infrastructure Administration Foundations

Learning progression:

2.1 Windows Server Administration Foundations

        ↓

2.2 Windows Server Administration Fundamentals

        ↓

2.3 Active Directory Administration Fundamentals

        ↓

2.4 PowerShell Administration

        ↓

2.5 Windows Server Security Fundamentals

This phase develops the infrastructure knowledge required for:

Active Directory administration
Hybrid identity
Microsoft Entra ID integration
Microsoft 365 administration
Cloud security operations
