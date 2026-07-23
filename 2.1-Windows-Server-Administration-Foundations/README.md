# 2.1 Windows Server Administration Foundations

## Windows Server 2025 Deployment and Baseline Validation

**Status:** Completed

**Platform:** Oracle VirtualBox

**Technology:** Microsoft Windows Server 2025

**Domain:** Microsoft Infrastructure Administration


---

# Overview

This laboratory establishes the infrastructure foundation for the Microsoft Infrastructure Administration phase.

Following the completion of identity-focused laboratories in Microsoft Entra ID, this phase expands into the underlying systems that support modern Microsoft enterprise environments.

The objective of this laboratory was to deploy, configure, and validate a Windows Server 2025 environment that can be used for future administration exercises involving:

- Active Directory Domain Services
- Group Policy
- PowerShell administration
- Windows security
- Enterprise troubleshooting


Understanding Windows Server administration provides the foundation required for effective identity, hybrid infrastructure, and cloud administration.


---

# Objective

The objectives of this laboratory were:

- Deploy a Windows Server 2025 virtual machine
- Confirm successful operating system installation
- Validate server identity
- Verify administrative access
- Validate network configuration
- Confirm external connectivity
- Establish a documented server baseline


---

# Environment Overview

## Virtual Infrastructure

| Component | Configuration |
|---|---|
| Virtualisation Platform | Oracle VirtualBox |
| Host Operating System | Windows 11 |
| Guest Operating System | Windows Server 2025 Standard Evaluation |
| Administration Tool | PowerShell |


## Server Configuration

| Parameter | Value |
|---|---|
| Computer Name | WIN-URRN4NJRE9I |
| Operating System | Windows Server 2025 Standard Evaluation |
| Operating System Build | 26100 |
| IPv4 Address | 10.0.2.15 |
| Network Configuration | VirtualBox NAT |


---

# Implementation

## Windows Server Deployment

Windows Server 2025 Standard Evaluation was deployed within Oracle VirtualBox.

The environment was configured as a reusable Microsoft administration laboratory platform.

Initial configuration included:

- Windows Server installation
- Local administrator access
- Network configuration
- PowerShell availability


---

# Validation

Validation was performed using PowerShell commands to confirm the server baseline configuration.

The validation process focused on confirming:

- System identity
- Operating system state
- Network configuration
- Connectivity
- Administrative context


---

## Server Identity Validation

Command:

```powershell
hostname

Result:

WIN-URRN4NJRE9I

Purpose:

Confirmed the server hostname and system identity.

Operating System Validation

Command:

Get-ComputerInfo | Select-Object WindowsProductName, WindowsVersion, OsBuildNumber

Result:

WindowsProductName:
Windows Server 2025 Standard Evaluation

WindowsVersion:
2009

OsBuildNumber:
26100

Purpose:

Confirmed the installed Windows Server edition and operating system build.

Network Configuration Validation

Command:

ipconfig

Validated configuration:

IPv4 Address:
10.0.2.15

Default Gateway:
10.0.2.2

Purpose:

Confirmed network adapter configuration and VirtualBox NAT connectivity.

External Connectivity Validation

Command:

Test-NetConnection google.com

Result:

PingSucceeded : True

Purpose:

Confirmed:

Network connectivity
DNS resolution
External communication capability
Administrative Identity Validation

Command:

whoami

Result:

WIN-URRN4NJRE9I\administrator

Purpose:

Confirmed the active administrative security context.

System Date Validation

Command:

Get-Date

Purpose:

Confirmed system date and time configuration.

Evidence

All laboratory evidence was collected using PowerShell output.

No screenshot evidence was collected.

Evidence structure:

Evidence
│
└── PowerShell
    │
    ├── hostname.txt
    ├── computerinfo.txt
    ├── ipconfig.txt
    ├── test-networkconnection.txt
    ├── whoami.txt
    └── get-date.txt

The collected evidence provides a reproducible baseline of:

Server identity
Operating system information
Network configuration
Connectivity status
Administrative access
System configuration state
Troubleshooting

During laboratory operation, VirtualBox interaction required adjustment.

Investigated areas included:

Virtual machine input behaviour
Keyboard capture
Host and guest workflow
Efficient administration workflow

The final approach used a practical VirtualBox configuration that allowed continued administration, evidence collection, and documentation.

Operational Interpretation

This laboratory demonstrates the relationship between infrastructure and identity.

Modern Microsoft environments depend on a layered architecture:

Windows Server

        ↓

Active Directory

        ↓

Microsoft Entra ID

        ↓

Identity Security

Reliable identity services require reliable infrastructure foundations.

Before implementing additional enterprise services, administrators must validate:

System configuration
Administrative access
Network functionality
Operating system state
Baseline documentation
Lessons Learned

This laboratory reinforced the importance of:

Validating infrastructure before deploying additional services
Using PowerShell for efficient administration
Maintaining evidence-based troubleshooting practices
Documenting system baselines before configuration changes
Skills Demonstrated
Windows Server deployment
Windows administration fundamentals
PowerShell system validation
Network troubleshooting
Administrative identity verification
Infrastructure documentation
Evidence collection methodology
Repository Context

This laboratory is part of:

Phase 2 — Microsoft Infrastructure Administration Foundations

Learning progression:

2.1 Windows Server Administration Foundations

        ↓

2.2 Active Directory Administration Fundamentals

        ↓

2.3 PowerShell Administration Fundamentals

        ↓

2.4 Windows Server Security Fundamentals

This phase develops the infrastructure foundation required for:

Active Directory administration
Hybrid identity
Microsoft Entra ID integration
Microsoft 365 administration
Azure administration
Identity security
