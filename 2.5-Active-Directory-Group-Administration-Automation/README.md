# 2.5 Active Directory Group Administration Automation

**Status:** Completed

**Date:** 16 August 2026

**Platform:** Oracle VirtualBox

**Technology:** Microsoft Windows Server 2025 Standard Evaluation

**Domain:** Microsoft Infrastructure Administration | Active Directory | Identity and Access Management

---

# Overview

This laboratory extends the Active Directory administration work from Lab 2.4 into **Active Directory group administration and controlled PowerShell automation**.

The laboratory demonstrates how Active Directory security groups can be:

- inspected
- classified
- created
- populated
- validated
- modified
- enumerated
- deleted
- independently verified
- administered through PowerShell automation

The laboratory also demonstrates a controlled administrative methodology in which changes are not simply executed and assumed to have succeeded.

The workflow follows:

```text
Precondition
     ↓
Action
     ↓
Post-condition validation
     ↓
Independent verification
     ↓
Cleanup
     ↓
Final-state verification
```

The central principle is:

> An administrative command performing successfully is not, by itself, proof that the desired directory state exists.

The resulting state must be queried and validated.

---

# Environment

| Component | Configuration |
|---|---|
| Virtualisation Platform | Oracle VirtualBox |
| Host Operating System | Windows 11 |
| Guest Operating System | Windows Server 2025 Standard Evaluation |
| Administration Method | Windows PowerShell 5.1 |
| Server Name | WIN-URRN4NJRE9I |
| Active Directory Domain | corp.lab |
| Administrator Context | corp\administrator |

---

# Objectives

The objectives of this laboratory were to:

- validate the PowerShell environment
- validate the Active Directory PowerShell module
- confirm the expected Active Directory domain
- confirm the administrative security context
- inspect existing Active Directory security groups
- understand security groups versus distribution groups
- examine Active Directory group scopes
- inspect existing group membership
- inspect the `Domain Users` group
- create a controlled security group
- add an Active Directory user to the group
- validate group membership
- validate the user's group memberships
- remove a user from a group
- re-add the user
- inspect nested group membership
- enumerate security groups and their membership counts
- identify empty security groups
- create a temporary group for controlled deletion testing
- validate group deletion
- develop a controlled PowerShell group-administration workflow
- implement precondition checks
- implement error handling
- implement post-operation validation
- independently verify the resulting Active Directory state
- clean up temporary automation objects
- document the complete workflow as reproducible technical evidence

---

# Administration Methodology

The laboratory uses the evidence-based administration methodology established during the previous Active Directory laboratories.

```text
Execute
   ↓
Validate
   ↓
Interpret
   ↓
Document
```

For administrative automation, the methodology becomes:

```text
Precondition
   ↓
Action
   ↓
Post-condition validation
   ↓
Failure handling
   ↓
Independent final-state verification
```

This approach is important because administrative automation can increase both efficiency and potential impact.

A manual mistake may affect one object.

An incorrectly designed automation workflow can affect many objects.

Therefore, automation should introduce stronger controls rather than weaker ones.

---

# PowerShell Environment Validation

The PowerShell environment was validated before performing Active Directory administration.

## PowerShell Version

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

Windows PowerShell 5.1 was confirmed inside the Windows Server virtual machine.

This distinction was important because PowerShell 7.6.5 was also installed on the Windows 11 host.

The Active Directory administration work for this laboratory was performed inside the Windows Server virtual machine using Windows PowerShell 5.1.

---

# Active Directory PowerShell Module Validation

The Active Directory module was confirmed to be available.

Command:

```powershell
Get-Module -ListAvailable ActiveDirectory
```

Output:

```text
Directory: C:\WINDOWS\system32\WindowsPowerShell\v1.0\Modules

ModuleType Version    Name
---------- -------    ----
Manifest   1.0.1.0    ActiveDirectory
```

The module exposed the expected Active Directory cmdlets, including commands such as:

```text
Add-ADGroupMember
Get-ADGroup
Get-ADGroupMember
Get-ADUser
Remove-ADGroupMember
New-ADGroup
Remove-ADGroup
```

This confirmed that the Active Directory PowerShell management functionality was available.

---

# Active Directory Domain Validation

The expected Active Directory environment was validated.

Command:

```powershell
Get-ADDomain | Select-Object DNSRoot,DomainMode,Forest
```

Output:

```text
DNSRoot         DomainMode          Forest
-------         ----------          ------
corp.lab        Windows2025Domain   corp.lab
```

Interpretation:

The PowerShell Active Directory module was communicating with the expected `corp.lab` domain.

This validation was performed before modifying Active Directory objects.

---

# Administrative Context Validation

The current security context was checked.

Command:

```powershell
whoami
```

Output:

```text
corp\administrator
```

This confirmed that the session was operating under the expected domain administrator account.

The laboratory therefore had the required administrative context for controlled Active Directory group-management operations.

---

# Understanding Active Directory Groups

Active Directory groups provide a mechanism for managing collections of security principals.

Groups can be used to simplify:

- access control
- resource permissions
- administrative delegation
- application access
- policy assignment
- identity management
- security administration

Instead of assigning permissions individually to every user, permissions can be assigned to a group.

Users can then be added to or removed from that group.

This separates:

```text
Identity
   ↓
Group membership
   ↓
Authorisation
```

from directly assigning permissions to individual accounts.

---

# Security Groups and Distribution Groups

Active Directory groups have a **group category**.

The two main categories are:

```text
Security
Distribution
```

## Security Groups

Security groups are used for security and access control.

They can be used in access-control decisions, for example:

```text
User
   ↓
Security Group
   ↓
Resource Permission
```

A security group can therefore represent an administrative or access-control boundary.

Examples in the laboratory include:

```text
Domain Admins
Domain Users
Remote Desktop Users
Lab-IT-Support
```

---

## Distribution Groups

Distribution groups are intended primarily for distributing messages to groups of recipients.

They are not used as security principals for assigning permissions in the same way as security groups.

A simplified distinction is:

```text
Security Group
    ↓
Access control / authorisation

Distribution Group
    ↓
Communication / mail distribution
```

The distinction matters because the wrong group category can result in an object being unsuitable for the intended administrative purpose.

---

# Distribution Group Enumeration

The domain was queried for distribution groups.

Command:

```powershell
Get-ADGroup -Filter 'GroupCategory -eq "Distribution"' |
Select-Object Name,SamAccountName,GroupScope,GroupCategory
```

Output:

```text
PS C:\WINDOWS\system32>
```

No objects were returned.

Interpretation:

No distribution groups were present in the laboratory domain.

The existing groups were therefore predominantly security groups created by Active Directory and by the laboratory exercises.

---

# Security Group Enumeration

All security groups were enumerated.

Command:

```powershell
Get-ADGroup -Filter 'GroupCategory -eq "Security"' |
Select-Object Name,SamAccountName,GroupScope,GroupCategory
```

The domain contained security groups including:

```text
Domain Computers
Domain Controllers
Domain Admins
Domain Users
Domain Guests
Group Policy Creator Owners
Read-only Domain Controllers
Cloneable Domain Controllers
Protected Users
Key Admins
Forest Trust Accounts
External Trust Accounts
DnsUpdateProxy
Cert Publishers
RAS and IAS Servers
Allowed RODC Password Replication Group
Denied RODC Password Replication Group
DnsAdmins
Administrators
Users
Guests
Print Operators
Backup Operators
Replicator
Remote Desktop Users
Network Configuration Operators
Performance Monitor Users
Performance Log Users
Distributed COM Users
IIS_IUSRS
Cryptographic Operators
Event Log Readers
Certificate Service DCOM Access
RDS Remote Access Servers
RDS Endpoint Servers
RDS Management Servers
Hyper-V Administrators
Access Control Assistance Operators
Remote Management Users
Storage Replica Administrators
OpenSSH Users
Server Operators
Account Operators
Pre-Windows 2000 Compatible Access
Incoming Forest Trust Builders
Windows Authorization Access Group
Terminal Server License Servers
Schema Admins
Enterprise Admins
Enterprise Read-only Domain Controllers
Enterprise Key Admins
```

The laboratory-created group was later added to this inventory:

```text
Lab-IT-Support
```

---

# Active Directory Group Scope

Active Directory security groups can also have different scopes.

The main scopes encountered in the laboratory are:

```text
Global
DomainLocal
Universal
```

The scope determines how the group can be used within the Active Directory environment and affects where members and permissions can be applied.

Examples from the laboratory:

```text
Global
------
Domain Users
Domain Admins
Lab-IT-Support
Lab-IT-Automation
```

```text
DomainLocal
-----------
Administrators
Users
Guests
Remote Desktop Users
Event Log Readers
```

```text
Universal
---------
Schema Admins
Enterprise Admins
Enterprise Key Admins
```

The laboratory used **Global Security Groups** for the controlled groups created during testing.

---

# Inspecting Domain Users

The built-in `Domain Users` group was inspected.

Command:

```powershell
Get-ADGroupMember -Identity "Domain Users" |
Select-Object Name,SamAccountName,ObjectClass
```

Output:

```text
Name                  SamAccountName ObjectClass
----                  -------------- -----------
Administrator         Administrator  user
krbtgt                krbtgt         user
Lab User01            labuser01      user
Lab Automation User01 labauto01      user
```

This demonstrated that `Domain Users` contained the laboratory user accounts as members.

---

# Inspecting Domain Users Group Properties

The group properties were queried.

Command:

```powershell
Get-ADGroup -Identity "Domain Users" -Properties Description |
Select-Object Name,SamAccountName,GroupCategory,GroupScope,DistinguishedName,Description
```

Output:

```text
Name              : Domain Users
SamAccountName    : Domain Users
GroupCategory     : Security
GroupScope        : Global
DistinguishedName : CN=Domain Users,CN=Users,DC=corp,DC=lab
Description       : All domain users
```

Interpretation:

`Domain Users` is a:

```text
Security Group
Global Group
```

located in:

```text
CN=Users,DC=corp,DC=lab
```

The group therefore acts as a security group representing domain user accounts.

---

# Creating the Controlled Lab-IT-Support Group

Before creating the laboratory group, the expected target object was checked.

Command:

```powershell
Get-ADGroup -Identity "Lab-IT-Support" -ErrorAction SilentlyContinue
```

Output indicated that the object did not exist:

```text
Get-ADGroup : Cannot find an object with identity: 'Lab-IT-Support'
under: 'DC=corp,DC=lab'.
```

This established the precondition:

```text
Lab-IT-Support does not exist
```

The group could therefore be created without modifying an existing object.

---

# Creating Lab-IT-Support

The controlled security group was created.

Command:

```powershell
New-ADGroup `
    -Name "Lab-IT-Support" `
    -SamAccountName "Lab-IT-Support" `
    -GroupCategory Security `
    -GroupScope Global `
    -Description "Lab 2.5 controlled security group for group administration testing" `
    -ErrorAction Stop
```

No error was returned.

The group creation operation therefore completed successfully.

---

# Validating Lab-IT-Support

The newly created group was queried independently.

Command:

```powershell
Get-ADGroup -Identity "Lab-IT-Support" -Properties Description |
Select-Object Name,SamAccountName,GroupCategory,GroupScope,DistinguishedName,Description
```

Output:

```text
Name              : Lab-IT-Support
SamAccountName    : Lab-IT-Support
GroupCategory     : Security
GroupScope        : Global
DistinguishedName : CN=Lab-IT-Support,CN=Users,DC=corp,DC=lab
Description       : Lab 2.5 controlled security group for group administration testing
```

This confirmed:

```text
Name              = Lab-IT-Support
SamAccountName    = Lab-IT-Support
GroupCategory     = Security
GroupScope        = Global
DistinguishedName = CN=Lab-IT-Support,CN=Users,DC=corp,DC=lab
```

The desired group state was therefore confirmed.

---

# Validating the Target User

The account used for the group-membership test was:

```text
Lab Automation User01
labauto01
```

The account was validated before adding it to the group.

Command:

```powershell
Get-ADUser -Identity "labauto01" |
Select-Object Name,SamAccountName,Enabled
```

Output:

```text
Name                  SamAccountName Enabled
----                  -------------- -------
Lab Automation User01 labauto01         True
```

The target account existed and was enabled.

This established the precondition for the membership operation.

---

# Adding labauto01 to Lab-IT-Support

The user was added to the controlled security group.

Command:

```powershell
Add-ADGroupMember -Identity "Lab-IT-Support" -Members "labauto01" -ErrorAction Stop
```

No error was returned.

The membership operation therefore completed successfully.

---

# Validating Group Membership

The group was queried directly.

Command:

```powershell
Get-ADGroupMember -Identity "Lab-IT-Support" |
Select-Object Name,SamAccountName,ObjectClass
```

Output:

```text
Name                  SamAccountName ObjectClass
----                  -------------- -----------
Lab Automation User01 labauto01      user
```

This confirmed that:

```text
Lab-IT-Support
        ↓
labauto01
```

The expected membership relationship existed.

---

# Independent Membership Validation

The user's group memberships were then queried from the opposite direction.

Command:

```powershell
Get-ADPrincipalGroupMembership -Identity "labauto01" |
Select-Object Name,SamAccountName,GroupScope,GroupCategory
```

Output:

```text
Name           SamAccountName GroupScope GroupCategory
----           -------------- ---------- -------------
Domain Users   Domain Users       Global      Security
Lab-IT-Support Lab-IT-Support      Global      Security
```

This provided an independent validation path.

The first query asked:

```text
Who belongs to Lab-IT-Support?
```

The second query asked:

```text
Which groups does labauto01 belong to?
```

Both confirmed the same relationship.

This is stronger evidence than relying on only one query.

---

# Removing a Group Member

The membership relationship was then deliberately removed to test the inverse operation.

Command:

```powershell
Remove-ADGroupMember -Identity "Lab-IT-Support" -Members "labauto01" -Confirm:$false -ErrorAction Stop
```

No error was returned.

The group was then queried:

```powershell
Get-ADGroupMember -Identity "Lab-IT-Support" |
Select-Object Name,SamAccountName,ObjectClass
```

No members were returned.

This confirmed that `labauto01` had been removed from the group.

---

# Re-Adding the Group Member

The membership relationship was restored.

Command:

```powershell
Add-ADGroupMember -Identity "Lab-IT-Support" -Members "labauto01" -ErrorAction Stop
```

The group was then queried again:

```powershell
Get-ADGroupMember -Identity "Lab-IT-Support" |
Select-Object Name,SamAccountName,ObjectClass
```

Output:

```text
Name                  SamAccountName ObjectClass
----                  -------------- -----------
Lab Automation User01 labauto01      user
```

The user was successfully restored as a member.

---

# Revalidating the User's Group Membership

The user's memberships were checked again.

Command:

```powershell
Get-ADPrincipalGroupMembership -Identity "labauto01" |
Select-Object Name,SamAccountName,GroupScope,GroupCategory
```

Output:

```text
Name           SamAccountName GroupScope GroupCategory
----           -------------- ---------- -------------
Domain Users   Domain Users       Global      Security
Lab-IT-Support Lab-IT-Support      Global      Security
```

This confirmed that the final intended membership relationship was restored.

---

# Checking for Nested Group Membership

The laboratory also tested whether `Lab-IT-Support` contained groups as members.

Command:

```powershell
Get-ADGroupMember -Identity "Lab-IT-Support" |
Where-Object ObjectClass -eq "group" |
Select-Object Name,SamAccountName,ObjectClass
```

Output:

```text
PS C:\WINDOWS\system32>
```

No objects were returned.

Interpretation:

`Lab-IT-Support` contained the user `labauto01`, but no nested groups.

The resulting structure was therefore:

```text
Lab-IT-Support
      ↓
labauto01
```

rather than:

```text
Lab-IT-Support
      ↓
Another Group
      ↓
Users
```

This distinction is important when analysing effective access in Active Directory.

---

# Enumerating Security Groups by Scope

The security groups were enumerated and sorted by scope.

Command:

```powershell
Get-ADGroup -Filter 'GroupCategory -eq "Security"' |
Select-Object Name,SamAccountName,GroupScope,GroupCategory |
Sort-Object GroupScope,Name
```

The resulting inventory included the laboratory-created group:

```text
Name                                    SamAccountName                           GroupScope    GroupCategory
----                                    --------------                           ----------    -------------
Access Control Assistance Operators     Access Control Assistance Operators     DomainLocal   Security
Account Operators                       Account Operators                       DomainLocal   Security
Administrators                          Administrators                          DomainLocal   Security
Allowed RODC Password Replication Group Allowed RODC Password Replication Group DomainLocal   Security
Backup Operators                        Backup Operators                        DomainLocal   Security
Cert Publishers                         Cert Publishers                         DomainLocal   Security
Certificate Service DCOM Access         Certificate Service DCOM Access         DomainLocal   Security
Cryptographic Operators                 Cryptographic Operators                 DomainLocal   Security
Denied RODC Password Replication Group  Denied RODC Password Replication Group  DomainLocal   Security
Distributed COM Users                   Distributed COM Users                   DomainLocal   Security
DnsAdmins                               DnsAdmins                               DomainLocal   Security
Event Log Readers                       Event Log Readers                       DomainLocal   Security
Guests                                  Guests                                  DomainLocal   Security
Hyper-V Administrators                  Hyper-V Administrators                  DomainLocal   Security
IIS_IUSRS                               IIS_IUSRS                               DomainLocal   Security
Incoming Forest Trust Builders          Incoming Forest Trust Builders          DomainLocal   Security
Network Configuration Operators         Network Configuration Operators         DomainLocal   Security
OpenSSH Users                            OpenSSH Users                           DomainLocal   Security
Performance Log Users                   Performance Log Users                   DomainLocal   Security
Performance Monitor Users               Performance Monitor Users               DomainLocal   Security
Pre-Windows 2000 Compatible Access      Pre-Windows 2000 Compatible Access      DomainLocal   Security
Print Operators                         Print Operators                         DomainLocal   Security
RAS and IAS Servers                     RAS and IAS Servers                     DomainLocal   Security
RDS Endpoint Servers                    RDS Endpoint Servers                    DomainLocal   Security
RDS Management Servers                  RDS Management Servers                  DomainLocal   Security
RDS Remote Access Servers               RDS Remote Access Servers               DomainLocal   Security
Remote Desktop Users                    Remote Desktop Users                    DomainLocal   Security
Remote Management Users                 Remote Management Users                 DomainLocal   Security
Replicator                              Replicator                              DomainLocal   Security
Server Operators                        Server Operators                        DomainLocal   Security
Storage Replica Administrators          Storage Replica Administrators          DomainLocal   Security
Terminal Server License Servers         Terminal Server License Servers         DomainLocal   Security
Users                                   Users                                   DomainLocal   Security
Windows Authorization Access Group      Windows Authorization Access Group      DomainLocal   Security

Cloneable Domain Controllers            Cloneable Domain Controllers            Global        Security
DnsUpdateProxy                          DnsUpdateProxy                          Global        Security
Domain Admins                            Domain Admins                           Global        Security
Domain Computers                         Domain Computers                        Global        Security
Domain Controllers                       Domain Controllers                      Global        Security
Domain Guests                            Domain Guests                           Global        Security
Domain Users                             Domain Users                            Global        Security
External Trust Accounts                  External Trust Accounts                 Global        Security
Forest Trust Accounts                    Forest Trust Accounts                   Global        Security
Group Policy Creator Owners              Group Policy Creator Owners             Global        Security
Key Admins                               Key Admins                              Global        Security
Lab-IT-Support                           Lab-IT-Support                          Global        Security
Protected Users                           Protected Users                         Global        Security
Read-only Domain Controllers             Read-only Domain Controllers            Global        Security

Enterprise Admins                         Enterprise Admins                     Universal     Security
Enterprise Key Admins                     Enterprise Key Admins                 Universal     Security
Enterprise Read-only Domain Controllers  Enterprise Read-only Domain Controllers Universal Security
Schema Admins                             Schema Admins                          Universal     Security
```

This provided an inventory view of the domain's security-group structure.

---

# Security Group Membership Count Analysis

A member-count analysis was performed to identify groups with members and groups with no current direct members.

Command:

```powershell
Get-ADGroup -Filter 'GroupCategory -eq "Security"' |
ForEach-Object {
    $Members = @(Get-ADGroupMember -Identity $_.DistinguishedName -ErrorAction SilentlyContinue)

    [PSCustomObject]@{
        Name        = $_.Name
        GroupScope  = $_.GroupScope
        GroupType   = $_.GroupCategory
        MemberCount = $Members.Count
    }
} |
Sort-Object MemberCount -Descending
```

Output:

```text
Name                                     GroupScope GroupType MemberCount
----                                     ---------- --------- -----------
Denied RODC Password Replication Group  DomainLocal Security           8
Domain Users                             Global      Security           4
Users                                    DomainLocal Security           3
Administrators                           DomainLocal Security           3
Guests                                   DomainLocal Security           2
Windows Authorization Access Group       DomainLocal Security           1
Lab-IT-Support                            Global      Security           1
Pre-Windows 2000 Compatible Access       DomainLocal Security           1
IIS_IUSRS                                 DomainLocal Security           1
Domain Guests                             Global      Security           1
Domain Admins                             Global      Security           1
Domain Controllers                        Global      Security           1
Group Policy Creator Owners               Global      Security           1
Schema Admins                             Universal   Security           1
Enterprise Admins                         Universal   Security           1
Access Control Assistance Operators      DomainLocal Security           0
Hyper-V Administrators                   DomainLocal Security           0
Certificate Service DCOM Access          DomainLocal Security           0
Event Log Readers                        DomainLocal Security           0
Enterprise Key Admins                    Universal   Security           0
RDS Management Servers                   DomainLocal Security           0
RDS Endpoint Servers                     DomainLocal Security           0
RDS Remote Access Servers                DomainLocal Security           0
Incoming Forest Trust Builders           DomainLocal Security           0
Account Operators                        DomainLocal Security           0
Enterprise Read-only Domain Controllers Universal   Security           0
Terminal Server License Servers          DomainLocal Security           0
Storage Replica Administrators           DomainLocal Security           0
Remote Management Users                  DomainLocal Security           0
Server Operators                         DomainLocal Security           0
OpenSSH Users                            DomainLocal Security           0
Cryptographic Operators                  DomainLocal Security           0
External Trust Accounts                  Global      Security           0
Forest Trust Accounts                    Global      Security           0
DnsUpdateProxy                           Global      Security           0
RAS and IAS Servers                      DomainLocal Security           0
Cert Publishers                          DomainLocal Security           0
Read-only Domain Controllers             Global      Security           0
Domain Computers                         Global      Security           0
Cloneable Domain Controllers             Global      Security           0
Key Admins                               Global      Security           0
Protected Users                          Global      Security           0
Network Configuration Operators           DomainLocal Security           0
Remote Desktop Users                     DomainLocal Security           0
Performance Monitor Users                DomainLocal Security           0
Distributed COM Users                    DomainLocal Security           0
Performance Log Users                    DomainLocal Security           0
DnsAdmins                                DomainLocal Security           0
Allowed RODC Password Replication Group  DomainLocal Security           0
Print Operators                          DomainLocal Security           0
Replicator                               DomainLocal Security           0
Backup Operators                         DomainLocal Security           0
```

This analysis demonstrated that group membership can be queried programmatically rather than manually inspecting every group.

It also demonstrated that an empty group is not automatically evidence of an error.

Some built-in groups legitimately have zero direct members in a particular environment.

---

# Why a Temporary Group Was Created

A separate temporary group was used to test group deletion safely.

The purpose was not to create another permanent administrative group.

The purpose was to create a controlled object whose lifecycle could be tested:

```text
Create
   ↓
Validate
   ↓
Delete
   ↓
Validate deletion
```

This is safer than testing deletion against an important built-in group.

The group was named:

```text
Lab-Temp-Delete
```

---

# Precondition Check for Lab-Temp-Delete

The expected group was checked before creation.

Command:

```powershell
Get-ADGroup -Identity "Lab-Temp-Delete" -ErrorAction SilentlyContinue
```

Output indicated that the group did not exist:

```text
Get-ADGroup : Cannot find an object with identity:
'Lab-Temp-Delete' under: 'DC=corp,DC=lab'.
```

The precondition was therefore:

```text
Lab-Temp-Delete does not exist
```

---

# Creating Lab-Temp-Delete

The temporary group was created.

Command:

```powershell
New-ADGroup `
    -Name "Lab-Temp-Delete" `
    -SamAccountName "Lab-Temp-Delete" `
    -GroupCategory Security `
    -GroupScope Global `
    -Description "Temporary Lab 2.5 group for controlled deletion testing" `
    -ErrorAction Stop
```

The command completed without error.

---

# Validating Lab-Temp-Delete

The new object was independently queried.

Command:

```powershell
Get-ADGroup -Identity "Lab-Temp-Delete" -Properties Description |
Select-Object Name,SamAccountName,GroupCategory,GroupScope,DistinguishedName,Description
```

Output:

```text
Name              : Lab-Temp-Delete
SamAccountName    : Lab-Temp-Delete
GroupCategory     : Security
GroupScope        : Global
DistinguishedName : CN=Lab-Temp-Delete,CN=Users,DC=corp,DC=lab
Description       : Temporary Lab 2.5 group for controlled deletion testing
```

The group therefore existed in the expected state.

---

# Deleting Lab-Temp-Delete

The controlled deletion operation was performed.

Command:

```powershell
Remove-ADGroup -Identity "Lab-Temp-Delete" -Confirm:$false -ErrorAction Stop
```

No error was returned.

---

# Independent Validation of Group Deletion

The deleted group was queried again.

Command:

```powershell
Get-ADGroup -Identity "Lab-Temp-Delete" -ErrorAction SilentlyContinue
```

The object could no longer be found:

```text
Get-ADGroup : Cannot find an object with identity:
'Lab-Temp-Delete' under: 'DC=corp,DC=lab'.
```

This demonstrated that the deletion had produced the expected final state:

```text
Lab-Temp-Delete
      ↓
Does not exist
```

The deletion operation was therefore validated independently.

---

# Preparing the Automation Test

The automation phase used another dedicated group:

```text
Lab-IT-Automation
```

The existing laboratory user remained:

```text
labauto01
```

The target group was deliberately chosen so that the automation workflow could safely create, populate, validate and delete the object without modifying a built-in Active Directory group.

---

# Automation Preconditions

The target group was checked before automation.

Command:

```powershell
Get-ADGroup -Identity "Lab-IT-Automation" -ErrorAction SilentlyContinue
```

Output indicated that the group did not exist:

```text
Get-ADGroup : Cannot find an object with identity:
'Lab-IT-Automation' under: 'DC=corp,DC=lab'.
```

The target user was also checked.

Command:

```powershell
Get-ADUser -Identity "labauto01" |
Select-Object Name,SamAccountName,Enabled
```

Output:

```text
Name                  SamAccountName Enabled
----                  -------------- -------
Lab Automation User01 labauto01         True
```

The automation preconditions were therefore:

```text
Expected domain = corp.lab
Target user exists = True
Target user enabled = True
Target group exists = False
```

---

# PowerShell Group Automation

The complete controlled group-administration workflow was implemented as a PowerShell script.

The script performs:

```text
Validate domain
        ↓
Validate target user
        ↓
Check target group does not exist
        ↓
Create security group
        ↓
Add target user
        ↓
Validate membership
        ↓
Validate final group state
        ↓
Report PASS
```

The script was:

```powershell
Import-Module ActiveDirectory -ErrorAction Stop

$ExpectedDomain = "corp.lab"
$GroupName = "Lab-IT-Automation"
$Username = "labauto01"

Write-Host "=== LAB 2.5 - GROUP ADMINISTRATION AUTOMATION ===" -ForegroundColor Cyan
Write-Host "Domain: $ExpectedDomain"
Write-Host "Target group: $GroupName"
Write-Host "Target user: $Username"
Write-Host ""

# 1. Validate domain

$Domain = Get-ADDomain -ErrorAction Stop

if ($Domain.DNSRoot -ne $ExpectedDomain) {
    throw "Unexpected domain detected: $($Domain.DNSRoot)"
}

Write-Host "[1] Domain validation PASSED." -ForegroundColor Green

# 2. Validate user exists

$User = Get-ADUser -Identity $Username -ErrorAction Stop

if (-not $User.Enabled) {
    throw "Target user '$Username' exists but is disabled."
}

Write-Host "[2] Target user validation PASSED." -ForegroundColor Green

# 3. Confirm target group does not already exist

$ExistingGroup = Get-ADGroup -Identity $GroupName -ErrorAction SilentlyContinue

if ($ExistingGroup) {
    throw "Target group '$GroupName' already exists. Script stopped."
}

Write-Host "[3] Target group precondition PASSED." -ForegroundColor Green

# 4. Create controlled security group

New-ADGroup `
    -Name $GroupName `
    -SamAccountName $GroupName `
    -GroupCategory Security `
    -GroupScope Global `
    -Description "Lab 2.5 PowerShell automation test group" `
    -ErrorAction Stop

Write-Host "[4] Security group created." -ForegroundColor Green

# 5. Add controlled user

Add-ADGroupMember `
    -Identity $GroupName `
    -Members $Username `
    -ErrorAction Stop

Write-Host "[5] User added to group." -ForegroundColor Green

# 6. Validate membership

$Membership = Get-ADGroupMember `
    -Identity $GroupName `
    -ErrorAction Stop

$MemberFound = $Membership |
    Where-Object { $_.SamAccountName -eq $Username }

if (-not $MemberFound) {
    throw "Validation failed: '$Username' is not a member of '$GroupName'."
}

Write-Host "[6] Membership validation PASSED." -ForegroundColor Green

# 7. Final state verification

$FinalGroup = Get-ADGroup `
    -Identity $GroupName `
    -Properties Description `
    -ErrorAction Stop

$FinalGroup |
    Select-Object Name,SamAccountName,GroupCategory,GroupScope,Description

$Membership |
    Select-Object Name,SamAccountName,ObjectClass

Write-Host ""
Write-Host "============================================" -ForegroundColor Green
Write-Host "LAB 2.5 GROUP AUTOMATION: PASSED" -ForegroundColor Green
Write-Host "============================================" -ForegroundColor Green
```

---

# Automation Execution

The script was executed from the Windows Server virtual machine using Windows PowerShell 5.1.

The script produced:

```text
=== LAB 2.5 - GROUP ADMINISTRATION AUTOMATION ===
Domain: corp.lab
Target group: Lab-IT-Automation
Target user: labauto01

[1] Domain validation PASSED.
[2] Target user validation PASSED.
[3] Target group precondition PASSED.
[4] Security group created.
[5] User added to group.
[6] Membership validation PASSED.

============================================
LAB 2.5 GROUP AUTOMATION: PASSED
============================================
```

During the target-group existence check, PowerShell displayed a non-fatal object-not-found message because `Lab-IT-Automation` did not yet exist:

```text
Get-ADGroup : Cannot find an object with identity:
'Lab-IT-Automation' under: 'DC=corp,DC=lab'.
```

The command used:

```powershell
-ErrorAction SilentlyContinue
```

and the resulting `$null` state was correctly interpreted by the script as:

```text
Target group does not exist
```

The script therefore continued to the creation stage.

This was the intended precondition.

---

# Automation Final Group State

After the automation completed, the resulting group was independently retrieved.

Commands:

```powershell
$FinalGroup = Get-ADGroup -Identity "Lab-IT-Automation" -Properties Description
$FinalMembers = Get-ADGroupMember -Identity "Lab-IT-Automation"
```

The group properties were then displayed:

```powershell
$FinalGroup |
    Select-Object Name,SamAccountName,GroupCategory,GroupScope,DistinguishedName,Description
```

Output:

```text
Name              : Lab-IT-Automation
SamAccountName    : Lab-IT-Automation
GroupCategory     : Security
GroupScope        : Global
DistinguishedName : CN=Lab-IT-Automation,CN=Users,DC=corp,DC=lab
Description       : Lab 2.5 PowerShell automation test group
```

The membership was displayed:

```powershell
$FinalMembers |
    Select-Object Name,SamAccountName,ObjectClass
```

Output:

```text
Name                  SamAccountName ObjectClass
----                  -------------- -----------
Lab Automation User01 labauto01      user
```

This independently confirmed the intended automated state:

```text
Lab-IT-Automation
        ↓
labauto01
```

---

# Automation Cleanup

The automation test group was deliberately removed after successful validation.

Command:

```powershell
Remove-ADGroup -Identity "Lab-IT-Automation" -Confirm:$false -ErrorAction Stop
```

The command completed without error.

The group was then independently queried.

Command:

```powershell
Get-ADGroup -Identity "Lab-IT-Automation" -ErrorAction SilentlyContinue
```

The object could no longer be found:

```text
Get-ADGroup : Cannot find an object with identity:
'Lab-IT-Automation' under: 'DC=corp,DC=lab'.
```

This confirmed that the temporary automation group had been removed.

---

# Final Independent Laboratory State Validation

The final Active Directory state was checked independently after cleanup.

Commands:

```powershell
$User = Get-ADUser -Identity "labauto01" -Properties Enabled
$SupportGroup = Get-ADGroup -Identity "Lab-IT-Support"
$SupportMembers = Get-ADGroupMember -Identity "Lab-IT-Support"

$AutomationGroup = Get-ADGroup -Filter "SamAccountName -eq 'Lab-IT-Automation'" -ErrorAction SilentlyContinue
```

The resulting state was evaluated:

```powershell
[PSCustomObject]@{
    UserExists            = ($null -ne $User)
    UserEnabled           = $User.Enabled
    SupportGroupExists    = ($null -ne $SupportGroup)
    LabAuto01Member       = ($SupportMembers.SamAccountName -contains "labauto01")
    AutomationGroupExists = ($null -ne $AutomationGroup)
}
```

Output:

```text
UserExists            : True
UserEnabled           : True
SupportGroupExists    : True
LabAuto01Member       : True
AutomationGroupExists : False
```

This was the final independently verified state.

It confirmed:

```text
labauto01 exists              = True
labauto01 enabled             = True
Lab-IT-Support exists         = True
labauto01 is member           = True
Lab-IT-Automation exists      = False
```

The intended permanent laboratory group remained:

```text
Lab-IT-Support
      ↓
labauto01
```

The temporary automation group had been removed:

```text
Lab-IT-Automation
      ↓
Deleted
```

---

# Final Laboratory State

The laboratory therefore ended in the following controlled state:

```text
Active Directory
└── corp.lab
    │
    ├── Domain Users
    │     ├── Administrator
    │     ├── krbtgt
    │     ├── labuser01
    │     └── labauto01
    │
    └── Lab-IT-Support
          └── labauto01
```

Temporary test objects were removed.

```text
Lab-Temp-Delete       → Deleted
Lab-IT-Automation     → Deleted
```

The controlled laboratory group remained:

```text
Lab-IT-Support
```

with:

```text
labauto01
```

as its direct member.

---

# Why the Temporary Groups Were Useful

The temporary groups were not created simply to increase the number of objects in the laboratory.

They allowed individual administrative operations to be tested safely.

For example:

```text
Lab-Temp-Delete
       ↓
Create
       ↓
Validate
       ↓
Delete
       ↓
Validate deletion
```

and:

```text
Lab-IT-Automation
       ↓
Check precondition
       ↓
Create
       ↓
Add member
       ↓
Validate
       ↓
Independent verification
       ↓
Delete
       ↓
Validate deletion
```

This approach isolates potentially destructive operations from important built-in Active Directory objects.

---

# Why Group Membership Matters for Security

Group membership is an important part of identity and access management.

A user's effective access may depend on:

```text
Direct group membership
        +
Nested group membership
        +
Group scope
        +
Resource permissions
        +
Policy
```

For example:

```text
labauto01
    ↓
Lab-IT-Support
    ↓
Permission
    ↓
Resource
```

Changing group membership can therefore change what an identity is authorised to access.

This means that group administration is not merely directory housekeeping.

It is an access-control operation.

---

# Direct Membership Versus Nested Membership

The laboratory explicitly checked whether `Lab-IT-Support` contained groups.

Command:

```powershell
Get-ADGroupMember -Identity "Lab-IT-Support" |
Where-Object ObjectClass -eq "group" |
Select-Object Name,SamAccountName,ObjectClass
```

No groups were returned.

Therefore:

```text
Lab-IT-Support
      ↓
labauto01
```

was a direct membership relationship.

In a larger environment, group nesting can produce more complex effective-access relationships.

For example:

```text
User
 ↓
Group A
 ↓
Group B
 ↓
Resource Permission
```

This is why identity investigations often need to consider group membership recursively rather than looking only at direct membership.

---

# Automation Safety Controls

The automation workflow contains several controls.

## Domain Validation

The script verifies:

```powershell
$ExpectedDomain = "corp.lab"
```

and compares this against the actual domain:

```powershell
$Domain = Get-ADDomain -ErrorAction Stop

if ($Domain.DNSRoot -ne $ExpectedDomain) {
    throw "Unexpected domain detected: $($Domain.DNSRoot)"
}
```

If the script is executed against an unexpected domain, it stops.

This prevents accidental execution against the wrong Active Directory environment.

---

## User Validation

The target user is retrieved before modifying group membership:

```powershell
$User = Get-ADUser -Identity $Username -ErrorAction Stop
```

The script also verifies that the account is enabled:

```powershell
if (-not $User.Enabled) {
    throw "Target user '$Username' exists but is disabled."
}
```

This creates a defined precondition for the membership operation.

---

## Existing Group Protection

The automation checks whether the target group already exists:

```powershell
$ExistingGroup = Get-ADGroup -Identity $GroupName -ErrorAction SilentlyContinue

if ($ExistingGroup) {
    throw "Target group '$GroupName' already exists. Script stopped."
}
```

This prevents the automation from silently modifying an existing group.

The intended behaviour is:

```text
Group exists
    ↓
STOP
```

rather than:

```text
Group exists
    ↓
Modify existing object
```

This is an important safety distinction.

---

## Error Handling

Active Directory modification commands use:

```powershell
-ErrorAction Stop
```

For example:

```powershell
New-ADGroup `
    -Name $GroupName `
    -SamAccountName $GroupName `
    -GroupCategory Security `
    -GroupScope Global `
    -Description "Lab 2.5 PowerShell automation test group" `
    -ErrorAction Stop
```

and:

```powershell
Add-ADGroupMember `
    -Identity $GroupName `
    -Members $Username `
    -ErrorAction Stop
```

This creates a clear failure boundary.

If an operation encounters a terminating error, the script does not simply continue to the next administrative operation.

---

## Post-Operation Validation

After creating the group, the script retrieves it again.

After adding the user, the script retrieves the membership again.

The script does not treat:

```text
Command completed
```

as equivalent to:

```text
Desired state exists
```

Instead:

```text
Action
 ↓
Retrieve resulting state
 ↓
Compare with expected state
```

This is a core administrative automation principle.

---

# Independent Verification

Independent verification was performed after the automation workflow.

The automation itself validated the membership:

```powershell
$Membership = Get-ADGroupMember `
    -Identity $GroupName `
    -ErrorAction Stop
```

The final state was subsequently queried independently:

```powershell
$FinalGroup = Get-ADGroup `
    -Identity $GroupName `
    -Properties Description `
    -ErrorAction Stop

$FinalMembers = Get-ADGroupMember `
    -Identity $GroupName
```

After cleanup, the directory was independently queried again.

This provides multiple validation paths:

```text
Automation validation
        ↓
Independent object query
        ↓
Independent membership query
        ↓
Final post-cleanup query
```

This reduces the risk of accepting an incorrect state merely because the automation reported success.

---

# PowerShell Evidence Strategy

The laboratory deliberately uses PowerShell output as the primary evidence mechanism.

No separate CSV evidence files were required.

The repository therefore does not need a collection of:

```text
screenshots
CSV exports
log attachments
```

for every individual operation.

Instead, the README contains:

- the command used
- the relevant PowerShell output
- the interpretation of that output
- the validation performed
- the resulting state

This keeps the repository reproducible while avoiding unnecessary evidence artefacts.

The PowerShell output is the evidence.

The written interpretation explains why the evidence matters.

---

# Administrative Methodology Demonstrated

The laboratory repeatedly applied the following pattern:

```text
1. Identify the object
        ↓
2. Establish the expected precondition
        ↓
3. Perform the administrative action
        ↓
4. Query the resulting state
        ↓
5. Compare the result with the expected state
        ↓
6. Perform independent verification where appropriate
        ↓
7. Clean up temporary objects
        ↓
8. Verify the final environment
```

Examples included:

```text
Create Lab-IT-Support
        ↓
Query group
        ↓
Validate properties
```

```text
Add labauto01
        ↓
Query group membership
        ↓
Query user's group memberships
```

```text
Remove labauto01
        ↓
Query membership
        ↓
Confirm removal
```

```text
Create Lab-Temp-Delete
        ↓
Validate
        ↓
Delete
        ↓
Confirm object no longer exists
```

```text
Create Lab-IT-Automation
        ↓
Add labauto01
        ↓
Validate
        ↓
Independently verify
        ↓
Delete
        ↓
Confirm deletion
```

---

# Manual Administration Versus Automation

The manual group-management workflow involved individual operations:

```text
Administrator
     ↓
Identify group
     ↓
Validate group
     ↓
Modify membership
     ↓
Validate membership
     ↓
Modify again
     ↓
Validate again
```

The automation workflow encoded the same logic:

```text
PowerShell Script
       ↓
Precondition
       ↓
Create
       ↓
Membership change
       ↓
Validation
       ↓
Final state
```

The purpose of automation is not merely to execute commands faster.

The larger benefit is the ability to encode administrative logic consistently.

A well-designed administrative script can make the expected state explicit.

---

# Automation Risk

Automation increases efficiency, but it also increases potential impact.

For example:

```text
Manual command
     ↓
Potentially affects one object
```

whereas:

```text
Automation
     ↓
Potentially affects hundreds or thousands of objects
```

Therefore:

```text
More automation
       ↓
More potential impact
       ↓
Stronger validation required
```

This is particularly important in identity administration because group membership can directly influence authorisation.

A poorly designed group-management script could unintentionally grant or remove access from many identities.

The laboratory therefore deliberately uses:

- dedicated test objects
- precondition checks
- explicit scope
- error handling
- post-operation validation
- independent verification
- cleanup

---

# Security Relevance

Active Directory group administration is directly relevant to identity security.

Group membership can determine whether an identity receives access to:

- files
- applications
- servers
- administrative functions
- remote access
- security-sensitive resources

For example:

```text
User
 ↓
Security Group
 ↓
Access Control Entry
 ↓
Resource
```

An unexpected group-membership change can therefore represent a security event.

From a security-monitoring perspective, changes such as:

```text
User added to privileged group
User removed from security group
New security group created
Security group deleted
Group membership modified
```

can be important administrative or security events.

This provides a direct connection between Active Directory administration and future identity-focused detection engineering.

---

# Identity Security Perspective

The laboratory demonstrates a fundamental identity-security concept:

> Authorisation is often determined indirectly through group membership.

A security analyst investigating unexpected access may therefore need to determine:

```text
Who is the user?
        ↓
Which groups is the user a member of?
        ↓
Are any groups privileged?
        ↓
Are there nested groups?
        ↓
What permissions do those groups provide?
        ↓
Was the membership change expected?
```

The administrative skills demonstrated here therefore provide a foundation for later work involving:

- identity monitoring
- access reviews
- privileged access analysis
- authentication investigations
- Microsoft Entra ID
- Microsoft Sentinel
- identity-based detection engineering

---

# Controlled Testing Methodology

The laboratory intentionally avoided modifying important built-in groups for destructive testing.

Instead, dedicated objects were used:

```text
Lab-IT-Support
Lab-Temp-Delete
Lab-IT-Automation
```

The workflow was:

```text
Dedicated test object
        ↓
Perform operation
        ↓
Validate result
        ↓
Review state
        ↓
Delete temporary object where appropriate
```

This is a safer approach than experimenting directly against:

```text
Domain Admins
Administrators
Domain Users
Enterprise Admins
Schema Admins
```

or other important built-in groups.

---

# Technical Findings

The laboratory demonstrated that Active Directory group administration can be performed and validated through PowerShell.

The following were successfully demonstrated.

## Environment Validation

- Windows PowerShell 5.1 confirmed
- Active Directory PowerShell module confirmed
- `corp.lab` domain confirmed
- `corp\administrator` security context confirmed

## Group Classification

- Security groups enumerated
- Distribution groups queried
- No distribution groups were present
- Group scopes examined
- Global, DomainLocal and Universal groups identified

## Group Administration

- `Domain Users` membership inspected
- `Domain Users` properties inspected
- `Lab-IT-Support` created
- `Lab-IT-Support` validated
- `labauto01` validated
- `labauto01` added to `Lab-IT-Support`
- Membership validated directly
- Membership validated from the user's perspective
- `labauto01` removed
- Removal validated
- `labauto01` re-added
- Re-addition validated
- Nested group membership checked

## Group Analysis

- Security groups enumerated by scope
- Group membership counts calculated
- Empty groups identified
- Existing directory structure examined programmatically

## Controlled Deletion

- `Lab-Temp-Delete` created
- Group properties validated
- Group deleted
- Deletion independently verified

## Automation

- Domain precondition implemented
- User precondition implemented
- Existing-group protection implemented
- Security group creation automated
- Membership addition automated
- Membership validation automated
- Final state validation implemented
- Error handling implemented
- Independent verification performed
- Temporary automation group removed
- Final environment independently validated

---

# Final Verified State

The final independent validation produced:

```text
UserExists            : True
UserEnabled           : True
SupportGroupExists    : True
LabAuto01Member       : True
AutomationGroupExists : False
```

The final state can therefore be represented as:

```text
corp.lab
   │
   ├── labauto01
   │      │
   │      └── Member of Lab-IT-Support
   │
   ├── Lab-IT-Support
   │      └── labauto01
   │
   └── Lab-IT-Automation
          └── Does not exist
```

The temporary deletion-test group was also removed:

```text
Lab-Temp-Delete
      ↓
Does not exist
```

---

# Skills Demonstrated

This laboratory demonstrated:

- Windows Server administration
- Windows PowerShell 5.1
- Active Directory administration
- Active Directory security groups
- Active Directory distribution groups
- Group scopes
- Group membership administration
- Security-group analysis
- Direct membership analysis
- Nested-group analysis
- PowerShell object filtering
- PowerShell pipelines
- PowerShell conditional logic
- PowerShell error handling
- PowerShell automation
- Precondition validation
- Post-operation validation
- Independent verification
- Controlled destructive testing
- Administrative cleanup
- Evidence-based administration
- Identity and access-control concepts
- Security-oriented administrative methodology

---

# Repository Structure

The laboratory is intentionally kept minimal.

```text
2.5-Active-Directory-Group-Administration-Automation
└── README.md
```

PowerShell commands, automation code, validation results and relevant outputs are contained directly within this README.

No separate CSV evidence files are required.

No passwords or credentials are included.

---

# Relationship to Lab 2.4

Lab 2.4 demonstrated:

```text
Active Directory User Administration
             ↓
PowerShell User Automation
```

Lab 2.5 extends the same methodology into:

```text
Active Directory Group Administration
             ↓
PowerShell Group Automation
```

The progression is therefore:

```text
2.3
Windows Server
     ↓
Active Directory Administration

2.4
Active Directory Users
     ↓
User Administration
     ↓
User Automation

2.5
Active Directory Groups
     ↓
Group Administration
     ↓
Group Automation
```

This builds a stronger infrastructure foundation for later identity and security work.

---

# Conclusion

This laboratory extended the Active Directory administration work from Lab 2.4 into group administration and controlled PowerShell automation.

The laboratory demonstrated that Active Directory groups can be administered through a repeatable, evidence-based workflow rather than through unverified command execution.

The key methodology was:

```text
Define expected state
        ↓
Validate preconditions
        ↓
Perform controlled action
        ↓
Validate resulting state
        ↓
Independently verify
        ↓
Clean up temporary objects
        ↓
Verify final environment
```

The laboratory successfully demonstrated:

- security-group identification
- distribution-group identification
- group-scope analysis
- group creation
- group membership administration
- membership removal
- membership restoration
- nested-group inspection
- group inventory analysis
- member-count analysis
- controlled group deletion
- PowerShell group automation
- precondition protection
- error handling
- post-operation validation
- independent verification
- controlled cleanup

The final environment was independently confirmed to contain:

```text
labauto01
    ↓
Lab-IT-Support
```

while the temporary automation and deletion-test groups were removed.

The most important technical lesson is that **identity administration is state management**.

The administrator should not merely ask:

```text
Did the command run?
```

The administrator should ask:

```text
What state did I expect?

What state existed before the operation?

What action did I perform?

What state exists now?

Can I independently verify it?
```

This approach provides a foundation for more advanced Microsoft infrastructure and identity-security work.

The same concepts can later be extended to:

- Active Directory organisational units
- Group Policy
- delegated administration
- privileged access management
- Microsoft Entra ID
- Microsoft Graph
- Azure administration
- identity lifecycle automation
- access reviews
- security monitoring
- Microsoft Sentinel
- identity-focused detection engineering

---

# Repository Context

This laboratory forms part of the Microsoft infrastructure and identity administration progression:

```text
2.1 Windows Server Administration Foundations

        ↓

2.2 Windows Server Administration Fundamentals

        ↓

2.3 Active Directory Administration Fundamentals

        ↓

2.4 Active Directory User Administration Fundamentals

        ↓

2.5 Active Directory Group Administration Automation
```

The progression develops practical foundations for:

- Microsoft Systems Administration
- Active Directory
- Hybrid Identity
- Microsoft Entra ID
- Azure Administration
- Identity Security
- Security Operations
- Detection Engineering
