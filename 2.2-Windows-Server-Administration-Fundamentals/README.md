# 2.2 Windows Server Administration Fundamentals

## Windows Server 2025 Administration and PowerShell Validation

**Status:** Completed

**Platform:** Oracle VirtualBox

**Technology:** Microsoft Windows Server 2025 Standard Evaluation

**Domain:** Microsoft Infrastructure Administration

---

# Overview

This laboratory develops practical Windows Server administration skills following the successful completion of Lab 2.1 — Windows Server Administration Foundations.

The objective was to validate and explore the Windows Server operating system layer that supports enterprise identity services, administration, and security operations.

This laboratory focused on:

- PowerShell administration
- System information retrieval
- Process management
- Service management
- Event log analysis
- Network validation
- Local account and group management
- Firewall configuration review

The knowledge developed in this laboratory provides the operational foundation required for future implementation of:

- Active Directory Domain Services
- Group Policy
- Identity management
- Security monitoring
- Microsoft Entra hybrid identity

---

# Objectives

The objectives of this laboratory were:

- Develop familiarity with Windows Server administration through PowerShell
- Validate system configuration
- Analyse running processes and services
- Understand local identity management
- Review Windows event logging
- Validate network configuration
- Review Windows Firewall security profiles
- Collect reproducible administrative evidence

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

# Methodology

The laboratory followed an evidence-based administration methodology.

Each administrative task was performed using:

- PowerShell commands
- Validation of output
- Evidence collection
- Security interpretation

The objective was not only command execution but understanding how Windows Server administrators validate, monitor, and troubleshoot systems.

---

# Phase 1 — PowerShell Administration

## System Information Validation

### Command

```powershell
Get-ComputerInfo | Select-Object CsName, WindowsProductName, OsBuildNumber, CsDomain

Output
CsName          : WIN-URRN4NJRE9I
WindowsProductName : Windows Server 2025 Standard Evaluation
OsBuildNumber   : 26100
CsDomain        : WORKGROUP
Interpretation

The command confirmed:

Correct server identity
Operating system version
Build number
Current domain membership status

The server remains in a standalone WORKGROUP configuration, which is expected before Active Directory deployment.

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

The output demonstrated:

Active Windows processes
Resource utilisation visibility
Process enumeration capability

Process inspection is an important administrative and security skill for identifying abnormal activity.

Validate Explorer Process
Command
Get-Process -Name explorer
Output
ProcessName
-----------
explorer
Interpretation

The command confirmed that the Windows graphical shell process was running correctly.

Identify Process Owner
Command
Get-Process -Name explorer -IncludeUserName
Output
UserName

WIN-URRN4NJRE9I\Administrator
Interpretation

The process ownership validation confirmed the administrative context.

Understanding process ownership is relevant for:

Privilege analysis
Security investigations
Threat detection
Service Management
Validate Windows Event Log Service
Command
Get-Service -Name EventLog
Output
Status   Name      DisplayName
------   ----      -----------
Running  EventLog  Windows Event Log
Interpretation

The Windows Event Log service was confirmed operational.

This service is critical because security monitoring depends on reliable event collection.

Review Running Services
Command
Get-Service | Where-Object {$_.Status -eq "Running"} | Select-Object -First 10
Output
Status   Name
------   ----
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

System troubleshooting
Security hardening
Identifying unnecessary services
Network Validation
Review Network Configuration
Command
Get-NetIPConfiguration
Output
InterfaceAlias : Ethernet

IPv4Address : 10.0.2.15

IPv4DefaultGateway : 10.0.2.2

DNSServer :
1.1.1.1
8.8.8.8
Interpretation

The server network configuration was validated successfully.

The results confirmed:

Correct IP addressing
Gateway availability
DNS configuration
Connectivity Test
Command
Test-NetConnection 8.8.8.8
Output
ComputerName : 8.8.8.8

PingSucceeded : True

PingReplyDetails :
16 ms
Interpretation

External network connectivity was confirmed.

PowerShell Security Validation
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

This provides a balance between administrative flexibility and script security.

Remote Administration Validation
WinRM Service Check
Command
Get-Service WinRM
Output
Status   Name
------   ----
Running  WinRM
Interpretation

Windows Remote Management is operational.

WinRM enables remote administration and is commonly used in enterprise environments.

Local Identity Management
Review Local Users
Command
Get-LocalUser
Output
Name
----
Administrator
DefaultAccount
Guest
WDAGUtilityAccount
Interpretation

The server contains default local accounts.

The Administrator account is enabled and provides administrative access.

Identity management is a core security responsibility.

Review Local Groups
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

Understanding group membership is essential for:

Least privilege implementation
Access control
Identity security
Active Directory Readiness Validation
Test Active Directory Module Availability
Command
Get-ADDomain
Output
Get-ADDomain : The term 'Get-ADDomain' is not recognized
Interpretation

The Active Directory PowerShell module is not currently installed.

This confirms that:

Active Directory Domain Services have not yet been deployed
The server remains a standalone server
Installed Windows Server Roles
Command
Get-WindowsFeature
Output Summary
File and Storage Services        Installed

Microsoft Defender Antivirus     Installed

Windows PowerShell               Installed

Windows Admin Center Setup       Installed

Active Directory Domain Services Available
Interpretation

The server currently has foundational administration components installed.

Active Directory Domain Services remains available for future deployment.

Event Viewer and Monitoring
Review Recent System Events
Command
Get-EventLog -LogName System -Newest 5
Output
Information

Service Control Manager
Windows services entered running/stopped states.
Interpretation

System event logs provide visibility into operating system activity.

Review System Errors
Command
Get-EventLog -LogName System -EntryType Error -Newest 5
Output
Error

EventLog
Previous system shutdown was unexpected.

DCOM
Event ID 10005.
Interpretation

The server contained historical administrative errors.

These events demonstrate the importance of:

Log analysis
Troubleshooting
Monitoring baselines
Storage Validation
Review Disk Configuration
Command
Get-Disk
Output
Number Friendly Name
0      VBOX HARDDISK

HealthStatus
Healthy

OperationalStatus
Online

Total Size
80 GB
Interpretation

The virtual disk was confirmed operational.

Firewall Validation
Review Firewall Profiles
Command
Get-NetFirewallProfile
Output Summary
Name     Enabled

Domain   True

Private  True

Public   True
Interpretation

Windows Firewall profiles are enabled.

Firewall configuration reduces attack surface by controlling network communication.

Evidence Collection

Evidence collected during this laboratory consists of PowerShell validation outputs.

Repository structure:

Evidence/
└── Lab-2.2/
    └── PowerShell/

Collected evidence includes:

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

The Windows Server 2025 environment was successfully validated as operational.

Confirmed:

✓ Server identity
✓ Operating system configuration
✓ Network functionality
✓ Administrative access
✓ PowerShell administration capability
✓ Service operation
✓ Event logging capability
✓ Local identity management
✓ Firewall protection status

Security Relevance

This laboratory established operational knowledge required for defensive administration.

Key security concepts demonstrated:

Identity

Local users and groups define access boundaries.

Monitoring

Windows Event Logs provide telemetry for:

Security investigations
SIEM ingestion
Detection engineering
Hardening

Firewall profiles and service management reduce attack surface.

Administration

PowerShell provides scalable and auditable system management.

Key Learning Outcomes

This laboratory reinforced:

Windows Server administration fundamentals
PowerShell-based system validation
Service troubleshooting
Event analysis
Identity administration foundations
Security-focused infrastructure management
Skills Demonstrated
Windows Server administration
PowerShell administration
Process analysis
Service management
Network troubleshooting
Event log analysis
Local identity management
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
