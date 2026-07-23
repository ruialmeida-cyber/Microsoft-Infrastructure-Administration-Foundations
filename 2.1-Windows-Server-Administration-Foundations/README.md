# 2.1 Windows Server Administration Foundations

## Windows Server 2025 Deployment and Baseline Validation

## Status

Completed

---

# Overview

This laboratory establishes the foundation for Microsoft infrastructure administration by deploying and validating a Windows Server 2025 environment.

Following the completion of identity-focused laboratories in Microsoft Entra ID, this phase expands into the infrastructure layer that supports modern Microsoft enterprise environments.

The objective was not simply to install Windows Server, but to create a reliable administration platform where future laboratories can explore:

- Windows Server administration
- Active Directory Domain Services
- Group Policy
- PowerShell administration
- Security configuration
- Enterprise troubleshooting

Understanding the infrastructure layer is essential because identity services operate on top of reliable systems and administrative foundations.

---

# Objective

The objectives of this laboratory were to:

- Deploy a Windows Server 2025 laboratory environment
- Validate the server installation
- Confirm operating system configuration
- Verify administrative access
- Validate network connectivity
- Establish a documented baseline configuration
- Prepare the environment for future Microsoft infrastructure laboratories

---

# Environment Overview

## Platform

Oracle VirtualBox

## Host Operating System

Windows 11

## Guest Operating System

Windows Server 2025 Standard Evaluation

## Administration Methods

- Windows Server Desktop Experience
- PowerShell

---

# Server Configuration

| Configuration | Value |
|---|---|
| Computer Name | WIN-URRN4NJRE9I |
| Operating System | Windows Server 2025 Standard Evaluation |
| OS Build | 26100 |
| IPv4 Address | 10.0.2.15 |
| Network Mode | VirtualBox NAT |

---

# Implementation

## 1. Windows Server Deployment

Windows Server 2025 Standard Evaluation was deployed within Oracle VirtualBox.

The installation was configured with:

- Desktop Experience
- Local Administrator account
- Network connectivity
- PowerShell availability

The server was prepared as a reusable Microsoft infrastructure laboratory environment.

---

# Validation

Validation was performed using PowerShell commands to confirm the server state.

---

## 1. Hostname Validation

Command:

```powershell
hostname

Result:

WIN-URRN4NJRE9I

Purpose:

Confirmed the server identity within the laboratory environment.

2. Operating System Validation

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

Confirmed the installed Windows Server version and build.

3. Network Configuration Validation

Command:

ipconfig

Validated:

IPv4 Address:
10.0.2.15

Subnet Mask:
255.255.255.0

Default Gateway:
10.0.2.2

Purpose:

Confirmed network adapter configuration and connectivity settings.

4. External Connectivity Validation

Command:

Test-NetConnection google.com

Result:

PingSucceeded : True

Purpose:

Confirmed:

DNS resolution
External network connectivity
Functional network communication
5. Administrative Identity Validation

Command:

whoami

Result:

WIN-URRN4NJRE9I\administrator

Purpose:

Confirmed the active administrative security context.

6. System Time Validation

Command:

Get-Date

Purpose:

Confirmed system date and time configuration.

Evidence

Evidence collected during this laboratory includes:

PowerShell Evidence

Location:

Evidence/PowerShell/

Files:

hostname.txt
computerinfo.txt
ipconfig.txt
test-networkconnection.txt
whoami.txt
get-date.txt
Screenshots

Location:

Evidence/Screenshots/

Includes:

Windows Server desktop
Server environment validation
Administrative tools
PowerShell validation
Troubleshooting

During the laboratory, VirtualBox display and input behaviour required adjustment.

Issues investigated:

Full-screen behaviour
Host keyboard shortcuts
VirtualBox input capture
Access between host Windows 11 and guest Windows Server

Resolution:

The laboratory continued using windowed mode, allowing:

Access to host tools
Documentation
Evidence collection
Normal workflow between environments
Operational Interpretation

This laboratory demonstrates the importance of validating infrastructure before implementing additional enterprise services.

A Microsoft environment depends on multiple connected layers:

Windows Server
        ↓
Active Directory
        ↓
Microsoft Entra ID
        ↓
Identity Security

Before deploying identity services, administrators must confirm:

The server exists
The system identity is correct
Administrative access works
Networking functions correctly
The environment is documented
Lessons Learned

This laboratory reinforced:

The importance of infrastructure validation
The role of PowerShell in administration
The value of evidence-based troubleshooting
The relationship between infrastructure and identity

Key professional lesson:

A reliable identity environment begins with reliable infrastructure.

Skills Demonstrated
Windows Server deployment
Windows administration foundations
PowerShell validation
Network troubleshooting
Administrative identity verification
Infrastructure documentation
Evidence collection methodology
Repository Context

This laboratory is part of:

Phase 2 — Microsoft Infrastructure Administration Foundations

Progression:

2.1 Windows Server Administration Foundations

        ↓

2.2 Active Directory Administration Fundamentals

        ↓

2.3 PowerShell Administration Fundamentals

        ↓

2.4 Windows Server Security Fundamentals

This phase builds the infrastructure foundation required for future work in:

Hybrid Identity
Microsoft Entra ID integration
Microsoft 365 administration
Azure administration
Identity security
