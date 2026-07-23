2.1 Windows Server Administration Foundations
Windows Server 2025 Infrastructure Baseline Validation

Status: Completed

Platform: Oracle VirtualBox

Technology: Microsoft Windows Server 2025 Standard Evaluation

Domain: Microsoft Infrastructure Administration | Windows Server Administration

Overview

This laboratory establishes the infrastructure foundation for Phase 2 — Microsoft Infrastructure Administration Foundations.

Following the completion of identity-focused laboratories in Microsoft Entra ID, the portfolio expands into the underlying Microsoft infrastructure that supports enterprise identity, administration, and security operations.

The objective of this laboratory was to deploy and validate a Windows Server 2025 environment and establish a reliable baseline before introducing additional Microsoft infrastructure services.

Future laboratories will build upon this environment by introducing:

Active Directory Domain Services
Organisational Units
Users and Groups
Group Policy
PowerShell administration
Windows Server security controls
Objective

The objectives of this laboratory were:

Deploy a Windows Server 2025 virtual environment
Validate successful installation
Confirm server identity
Verify operating system configuration
Validate network functionality
Confirm administrative access
Establish a documented infrastructure baseline
Environment and Configuration
Virtual Infrastructure
Component	Configuration
Virtualisation Platform	Oracle VirtualBox
Host Operating System	Windows 11
Guest Operating System	Windows Server 2025 Standard Evaluation
Administration Method	PowerShell
Server Baseline
Attribute	Value
Computer Name	WIN-URRN4NJRE9I
Operating System	Windows Server 2025 Standard Evaluation
OS Build	26100
IPv4 Address	10.0.2.15
Default Gateway	10.0.2.2
Network Mode	VirtualBox NAT
Implementation

Windows Server 2025 Standard Evaluation was deployed within Oracle VirtualBox.

The environment was configured as a reusable Microsoft administration laboratory platform.

Initial configuration included:

Windows Server installation
Local Administrator access
Network configuration
PowerShell administration capability

The completed environment provides the foundation for future laboratories involving:

Active Directory
Group Policy
PowerShell automation
Windows Server security
Validation Methodology

PowerShell was used as the primary validation method.

The purpose of validation was to confirm that the server was correctly deployed and operational before introducing additional infrastructure services.

Validation areas:

System identity
Operating system configuration
Network state
Connectivity
Administrative context

Commands executed:

hostname

Get-ComputerInfo | Select-Object WindowsProductName, WindowsVersion, OsBuildNumber

ipconfig

Test-NetConnection google.com

whoami

Get-Date
Evidence Collection

All evidence collected during this laboratory consists exclusively of PowerShell outputs.

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

The collected artefacts provide evidence of:

Server identity
Operating system state
Network configuration
Connectivity validation
Administrative identity
Infrastructure baseline state
Technical Findings
Server Identity

The hostname validation confirmed the deployed Windows Server identity:

WIN-URRN4NJRE9I

The server identity was successfully established.

Operating System Validation

The environment was confirmed as:

Windows Server 2025 Standard Evaluation

Build:
26100

The operating system deployment matched the expected laboratory configuration.

Network Validation

Network configuration validation confirmed:

IPv4 Address:
10.0.2.15

Default Gateway:
10.0.2.2

The server successfully obtained network configuration through the VirtualBox NAT environment.

Connectivity Validation

Connectivity testing confirmed successful communication:

PingSucceeded : True

This validated:

Network availability
DNS resolution
External connectivity
Administrative Validation

The active administrative context was confirmed:

WIN-URRN4NJRE9I\administrator

Administrative access was successfully validated.

Operational Interpretation

This laboratory demonstrates that infrastructure validation is a prerequisite for enterprise identity services.

Modern Microsoft environments follow a layered architecture:

Windows Server

        ↓

Active Directory

        ↓

Microsoft Entra ID

        ↓

Identity Security

Reliable identity services depend on reliable infrastructure foundations.

Before deploying additional services, administrators must establish:

Correct system identity
Reliable infrastructure
Functional networking
Valid administrative access
Documented configuration state
Key Learning Outcome

This laboratory reinforced the importance of establishing a validated infrastructure baseline before introducing additional Microsoft services.

Key concepts developed:

Windows Server administration fundamentals
PowerShell-based validation
Infrastructure troubleshooting
Evidence-based administration
Professional technical documentation
Skills Demonstrated
Windows Server deployment
Windows administration fundamentals
PowerShell validation
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

This phase develops the infrastructure knowledge required for:

Active Directory administration
Hybrid identity
Microsoft Entra ID integration
Microsoft 365 administration
Azure administration
Identity security
Portfolio Direction

This laboratory represents the transition from identity foundations into Microsoft infrastructure administration.

The progression follows the relationship between:

Infrastructure → Identity → Cloud → Security

Understanding the infrastructure layer creates the foundation required for future work in hybrid identity and identity-focused cloud security.
