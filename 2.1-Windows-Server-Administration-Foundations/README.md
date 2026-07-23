# 2.1 Windows Server Administration Foundations

## Windows Server 2025 Deployment and Baseline Validation

**Status:** Completed

**Platform:** Oracle VirtualBox

**Technology:** Microsoft Windows Server 2025

**Domain:** Microsoft Infrastructure Administration | Identity Foundations


---

# Overview

This laboratory establishes the foundation for Microsoft infrastructure administration by deploying and validating a Windows Server 2025 environment.

Following the completion of identity-focused laboratories in Microsoft Entra ID, this phase expands into the infrastructure layer that supports modern Microsoft enterprise environments.

The objective was not simply to install Windows Server, but to create a reliable administration platform for future laboratories covering:

- Windows Server administration
- Active Directory Domain Services
- PowerShell administration
- Security configuration
- Enterprise troubleshooting

Modern Microsoft environments depend on the interaction between infrastructure, identity, and security.

A strong understanding of Windows Server administration provides the foundation required for effective identity and cloud administration.


---

# Objective

The objectives of this laboratory were to:

- Deploy a Windows Server 2025 environment
- Validate the server installation
- Confirm operating system configuration
- Verify administrative access
- Validate network connectivity
- Establish a documented server baseline
- Prepare the environment for future infrastructure administration exercises


---

# Environment Overview

## Virtual Infrastructure

| Component | Configuration |
|---|---|
| Platform | Oracle VirtualBox |
| Host Operating System | Windows 11 |
| Guest Operating System | Windows Server 2025 Standard Evaluation |
| Administration Method | PowerShell and Windows Server GUI |


## Server Configuration

| Parameter | Value |
|---|---|
| Computer Name | WIN-URRN4NJRE9I |
| Operating System | Windows Server 2025 Standard Evaluation |
| OS Build | 26100 |
| IPv4 Address | 10.0.2.15 |
| Network Mode | VirtualBox NAT |


---

# Implementation

## Windows Server Deployment

Windows Server 2025 Standard Evaluation was deployed within Oracle VirtualBox.

The environment was configured with:

- Desktop Experience
- Local Administrator access
- Network connectivity
- PowerShell administration capability

The completed installation provides the foundation for future Microsoft infrastructure laboratories.


---

# Validation

Validation was performed using PowerShell commands to confirm the operational state of the server.

The purpose of validation was to establish a reliable baseline before introducing additional infrastructure services.


---

# Server Identity Validation

Command:

```powershell
hostname

Result:

WIN-URRN4NJRE9I

Validation purpose:

Confirmed the unique identity of the Windows Server instance.

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

Validation purpose:

Confirmed the expected Windows Server version and build.

Network Configuration Validation

Command:

ipconfig

Validated:

IPv4 Address:
10.0.2.15

Default Gateway:
10.0.2.2

Validation purpose:

Confirmed network adapter configuration and communication settings.

Connectivity Validation

Command:

Test-NetConnection google.com

Result:

PingSucceeded : True

Validation purpose:

Confirmed:

DNS resolution
External connectivity
Functional network communication
Administrative Identity Validation

Command:

whoami

Result:

WIN-URRN4NJRE9I\administrator

Validation purpose:

Confirmed the active administrative security context.

System Time Validation

Command:

Get-Date

Validation purpose:

Confirmed system date and time configuration.

Evidence

Evidence collected during this laboratory consists exclusively of PowerShell validation outputs.

No screenshot evidence was collected.

PowerShell Validation Evidence

Location:

Evidence/PowerShell/

Collected evidence:

hostname.txt

computerinfo.txt

ipconfig.txt

test-networkconnection.txt

whoami.txt

get-date.txt

These artefacts provide a reproducible record of:

Server identity
Operating system state
Network configuration
Connectivity status
Administrative identity
System configuration baseline
Troubleshooting

During initial laboratory operation, VirtualBox interaction required adjustment.

Areas investigated:

Virtual machine display behaviour
Keyboard capture
Host and guest workflow
Efficient evidence collection

Resolution:

The laboratory workflow continued using a practical windowed environment, allowing effective interaction between:

Windows 11 host system
Windows Server virtual machine
Documentation workflow
Operational Interpretation

This laboratory demonstrates that infrastructure validation is a prerequisite for enterprise identity services.

The Microsoft enterprise stack can be represented as:

Windows Server

        ↓

Active Directory

        ↓

Microsoft Entra ID

        ↓

Identity Security

Before implementing advanced identity services, administrators must establish:

Reliable infrastructure
Correct system identity
Valid administrative access
Functional networking
Documented configuration state
Lessons Learned

This laboratory reinforced several important administration principles:

Infrastructure must be validated before additional services are deployed
PowerShell provides efficient administrative visibility
Evidence collection improves troubleshooting accuracy
Identity services depend on reliable infrastructure foundations
Skills Demonstrated
Windows Server deployment
Windows administration fundamentals
PowerShell-based validation
Network troubleshooting
Administrative identity verification
Infrastructure documentation
Evidence-based troubleshooting
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

This phase develops the infrastructure knowledge required for:

Active Directory administration
Hybrid identity
Microsoft Entra ID integration
Microsoft 365 administration
Azure administration
Identity security
