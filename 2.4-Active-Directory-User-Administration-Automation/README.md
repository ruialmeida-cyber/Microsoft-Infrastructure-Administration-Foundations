# 2.4 Active Directory User Administration Fundamentals

**Status:** Completed

**Platform:** Oracle VirtualBox

**Technology:** Microsoft Windows Server 2025 Standard Evaluation

**Domain:** Microsoft Infrastructure Administration | Identity and Access Management

---

# Overview

This laboratory extends the Active Directory administration work from Lab 2.3 into PowerShell-based user administration and controlled automation.

The objective was to demonstrate how common Active Directory user-management tasks can be performed manually with PowerShell and then translated into a repeatable automation workflow with built-in validation.

The laboratory focuses on:

* PowerShell administration
* Active Directory user management
* Password management
* Account-state management
* Conditional logic
* Error handling
* Secure password input
* Automated validation
* Independent verification
* Controlled testing

The laboratory follows an evidence-based administration methodology:

```text
Execute
   ↓
Validate
   ↓
Interpret
   ↓
Document
```

The automation component extends this into:

```text
Precondition
   ↓
Action
   ↓
Post-condition validation
   ↓
Failure handling
   ↓
Final state verification
```

---

# Environment

| Component               | Configuration                           |
| ----------------------- | --------------------------------------- |
| Virtualisation Platform | Oracle VirtualBox                       |
| Host Operating System   | Windows 11                              |
| Guest Operating System  | Windows Server 2025 Standard Evaluation |
| Administration Method   | Windows PowerShell 5.1                  |
| Server Name             | WIN-URRN4NJRE9I                         |
| Active Directory Domain | corp.lab                                |

---

# Objectives

The objectives of this laboratory were:

* Use PowerShell to administer Active Directory
* Create and manage an Active Directory user
* Reset an Active Directory user password
* Configure password-change requirements
* Disable and enable an Active Directory account
* Validate account state through PowerShell
* Develop a PowerShell automation workflow
* Implement precondition checks
* Implement post-operation validation
* Test automation against a dedicated laboratory account
* Validate PowerShell script syntax
* Perform independent verification of the resulting Active Directory state
* Document the complete workflow as reproducible technical evidence

---

# PowerShell Environment

## PowerShell Version

The PowerShell environment was validated before administration.

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

This is the environment used for the Active Directory administration work in this laboratory.

---

# Active Directory Environment Validation

The Active Directory domain was confirmed before performing user administration.

Command:

```powershell
Get-ADDomain | Select-Object DNSRoot,DomainMode,Forest
```

Expected environment:

```text
DNSRoot     : corp.lab
DomainMode  : Windows2025Domain
Forest      : corp.lab
```

The validation confirmed that the PowerShell Active Directory module was communicating with the expected `corp.lab` domain.

---

# Manual Active Directory User Administration

Before developing the automation workflow, Active Directory user administration was performed manually.

This established the underlying administrative operations before they were automated.

The manual workflow involved:

```text
User
 ↓
Password
 ↓
Password-change requirement
 ↓
Account state
 ↓
Disable
 ↓
Validation
```

---

# User Account

The original laboratory account was:

```text
Name:
Lab User01

SamAccountName:
labuser01
```

The account was used to demonstrate individual Active Directory administration operations.

---

# Password Reset

The account password was reset using PowerShell.

The password was entered interactively rather than being written into the command or repository.

Command:

```powershell
Set-ADAccountPassword -Identity labuser01
```

An initial password attempt failed because it did not meet the domain password requirements.

PowerShell returned an error indicating that the password did not satisfy the configured domain requirements for length, complexity, or password history.

This demonstrated that the Active Directory domain password policy was being enforced.

A compliant password was subsequently accepted.

Passwords are deliberately excluded from this repository.

---

# Password State Validation

The account password state was validated using:

```powershell
Get-ADUser -Identity labuser01 -Properties PasswordLastSet,PasswordExpired,PasswordNeverExpires |
Select-Object Name,Enabled,PasswordLastSet,PasswordExpired,PasswordNeverExpires
```

After successful password configuration, the account returned:

```text
Name                 : Lab User01
Enabled              : True
PasswordLastSet      : 09/08/2026 3:55:01
PasswordExpired      : False
PasswordNeverExpires : False
```

This confirmed that:

* the password had been successfully established
* the password was not expired
* the password was not configured to never expire
* the account remained enabled

---

# Force Password Change at Logon

The account was configured to require a password change at the next logon.

Command:

```powershell
Set-ADUser -Identity labuser01 -ChangePasswordAtLogon $true
```

The resulting state was validated with:

```powershell
Get-ADUser -Identity labuser01 -Properties PasswordLastSet,PasswordExpired |
Select-Object Name,Enabled,PasswordLastSet,PasswordExpired
```

Output:

```text
Name        Enabled PasswordLastSet PasswordExpired
----        ------- --------------- ---------------
Lab User01  True                    True
```

Interpretation:

The account remained enabled, while the password state indicated that a password change was required at the next logon.

---

# Account Disablement

The account was then disabled.

Command:

```powershell
Disable-ADAccount -Identity labuser01
```

The resulting state was validated:

```powershell
Get-ADUser -Identity labuser01 |
Select-Object Name,Enabled
```

Output:

```text
Name        Enabled
----        -------
Lab User01  False
```

This confirmed successful account disablement.

---

# Why Automation Was Introduced

The manual workflow demonstrated the individual administrative operations.

However, repeating the same workflow across many identities would introduce:

* repetitive work
* inconsistent execution
* increased opportunity for human error
* difficulty maintaining consistent validation
* reduced scalability

PowerShell automation provides a way to encode administrative logic into a repeatable workflow.

The objective is therefore not simply to execute commands faster.

The objective is to make the administrative process:

* repeatable
* predictable
* validated
* auditable
* easier to scale

---

# Automation Test Account

A separate test account was used for automation.

```text
Name:
Lab Automation User01

SamAccountName:
labauto01

UserPrincipalName:
labauto01@corp.lab

Description:
Lab 2.4 PowerShell automation test account
```

The dedicated account was used to avoid modifying the existing `labuser01` account during automation testing.

This provided isolation between the manual administration exercise and the automated workflow.

---

# Automation Workflow

The automation was designed as a single controlled sequence.

```text
Validate Active Directory domain
        ↓
Check whether labauto01 exists
        ↓
STOP if account already exists
        ↓
Request password securely
        ↓
Create labauto01
        ↓
Retrieve account
        ↓
Validate initial state
        ↓
Disable account
        ↓
Validate disabled state
        ↓
Enable account
        ↓
Validate enabled state
        ↓
Perform independent final validation
        ↓
Report PASS / FAIL
```

The workflow demonstrates a state-based administrative automation principle:

> Define the expected state, perform the change, and verify the resulting state.

This is configuration and administrative automation rather than a claim that the script itself constitutes a complete infrastructure-as-code implementation.

---

# PowerShell Automation Script

The complete workflow was implemented as a single PowerShell script.

```powershell
Import-Module ActiveDirectory -ErrorAction Stop

$Username = "labauto01"
$Name = "Lab Automation User01"
$Description = "Lab 2.4 PowerShell automation test account"
$Domain = (Get-ADDomain).DNSRoot

Write-Host "=== LAB 2.4 - AD USER AUTOMATION ===" -ForegroundColor Cyan
Write-Host "Domain: $Domain"
Write-Host "Target account: $Username"
Write-Host ""

# ------------------------------------------------------------
# 1. Confirm the AD environment
# ------------------------------------------------------------

Write-Host "[1] Validating Active Directory environment..." -ForegroundColor Yellow

$DomainInfo = Get-ADDomain -ErrorAction Stop

if ($DomainInfo.DNSRoot -ne "corp.lab") {
    throw "Unexpected domain detected: $($DomainInfo.DNSRoot)"
}

Write-Host "AD domain validated: $($DomainInfo.DNSRoot)" -ForegroundColor Green

# ------------------------------------------------------------
# 2. Check whether the test account already exists
# ------------------------------------------------------------

Write-Host "[2] Checking whether $Username already exists..." -ForegroundColor Yellow

$ExistingUser = Get-ADUser -Filter "SamAccountName -eq '$Username'"

if ($ExistingUser) {
    throw "Account '$Username' already exists. Script stopped to prevent unintended modification."
}

Write-Host "Account does not exist. Safe to create." -ForegroundColor Green

# ------------------------------------------------------------
# 3. Obtain password securely
# ------------------------------------------------------------

Write-Host "[3] Enter the temporary password for $Username." -ForegroundColor Yellow
Write-Host "The password will NOT be written to the script or evidence files."

$Password = Read-Host "Password" -AsSecureString

# ------------------------------------------------------------
# 4. Create the Active Directory user
# ------------------------------------------------------------

Write-Host "[4] Creating Active Directory user..." -ForegroundColor Yellow

New-ADUser `
    -Name $Name `
    -SamAccountName $Username `
    -UserPrincipalName "$Username@$Domain" `
    -AccountPassword $Password `
    -Enabled $true `
    -ChangePasswordAtLogon $true `
    -Description $Description `
    -ErrorAction Stop

Write-Host "User created successfully." -ForegroundColor Green

# ------------------------------------------------------------
# 5. Retrieve and validate the newly created account
# ------------------------------------------------------------

Write-Host "[5] Validating created account..." -ForegroundColor Yellow

$User = Get-ADUser `
    -Identity $Username `
    -Properties Enabled,PasswordLastSet,PasswordExpired,
                PasswordNeverExpires,Description,UserPrincipalName `
    -ErrorAction Stop

$User | Select-Object `
    Name,
    SamAccountName,
    UserPrincipalName,
    Enabled,
    PasswordLastSet,
    PasswordExpired,
    PasswordNeverExpires,
    Description

# ------------------------------------------------------------
# 6. Validate expected initial state
# ------------------------------------------------------------

Write-Host "[6] Validating expected account state..." -ForegroundColor Yellow

$ValidationPassed = $true

if (-not $User.Enabled) {
    Write-Host "FAIL: Account is not enabled." -ForegroundColor Red
    $ValidationPassed = $false
}

if (-not $User.PasswordExpired) {
    Write-Host "FAIL: Password is not marked as requiring change." -ForegroundColor Red
    $ValidationPassed = $false
}

if ($User.PasswordNeverExpires) {
    Write-Host "FAIL: PasswordNeverExpires is unexpectedly enabled." -ForegroundColor Red
    $ValidationPassed = $false
}

if ($ValidationPassed) {
    Write-Host "Initial account validation PASSED." -ForegroundColor Green
}
else {
    throw "Initial validation failed."
}

# ------------------------------------------------------------
# 7. Test account disable operation
# ------------------------------------------------------------

Write-Host "[7] Testing account disable operation..." -ForegroundColor Yellow

Disable-ADAccount -Identity $Username -ErrorAction Stop

$User = Get-ADUser -Identity $Username -ErrorAction Stop

if ($User.Enabled) {
    throw "FAIL: Account should be disabled but is still enabled."
}

Write-Host "Disable operation validated successfully." -ForegroundColor Green

# ------------------------------------------------------------
# 8. Test account re-enablement
# ------------------------------------------------------------

Write-Host "[8] Testing account enable operation..." -ForegroundColor Yellow

Enable-ADAccount -Identity $Username -ErrorAction Stop

$User = Get-ADUser -Identity $Username -ErrorAction Stop

if (-not $User.Enabled) {
    throw "FAIL: Account should be enabled but is still disabled."
}

Write-Host "Enable operation validated successfully." -ForegroundColor Green

# ------------------------------------------------------------
# 9. Final independent validation
# ------------------------------------------------------------

Write-Host "[9] Performing final account validation..." -ForegroundColor Yellow

$FinalUser = Get-ADUser `
    -Identity $Username `
    -Properties Enabled,PasswordLastSet,PasswordExpired,
                PasswordNeverExpires,Description,UserPrincipalName `
    -ErrorAction Stop

$FinalUser | Select-Object `
    Name,
    SamAccountName,
    UserPrincipalName,
    Enabled,
    PasswordLastSet,
    PasswordExpired,
    PasswordNeverExpires,
    Description

# ------------------------------------------------------------
# 10. Final result
# ------------------------------------------------------------

if (
    $FinalUser.Enabled -eq $true -and
    $FinalUser.PasswordExpired -eq $true -and
    $FinalUser.PasswordNeverExpires -eq $false
) {
    Write-Host ""
    Write-Host "============================================" -ForegroundColor Green
    Write-Host "LAB 2.4 AUTOMATION VALIDATION: PASSED" -ForegroundColor Green
    Write-Host "============================================" -ForegroundColor Green
}
else {
    Write-Host ""
    Write-Host "============================================" -ForegroundColor Red
    Write-Host "LAB 2.4 AUTOMATION VALIDATION: FAILED" -ForegroundColor Red
    Write-Host "============================================" -ForegroundColor Red
    throw "Final validation failed."
}
```

---

# Automation Execution

The script was executed from the Windows Server virtual machine using Windows PowerShell 5.1.

Command:

```powershell
C:\Lab-2.4-User-Automation.ps1
```

The password was entered interactively when requested.

The password was not written into the PowerShell script or repository documentation.

---

# Automation Execution Evidence

The automation successfully produced the following results:

```text
=== LAB 2.4 - AD USER AUTOMATION ===
Domain: corp.lab
Target account: labauto01

[1] Validating Active Directory environment...
AD domain validated: corp.lab

[2] Checking whether labauto01 already exists...
Account does not exist. Safe to create.

[3] Enter the temporary password for labauto01.
The password will NOT be written to the script or evidence files.

[4] Creating Active Directory user...
User created successfully.

[5] Validating created account...

Name                 : Lab Automation User01
SamAccountName       : labauto01
UserPrincipalName    : labauto01@corp.lab
Enabled              : True
PasswordLastSet      :
PasswordExpired      : True
PasswordNeverExpires : False
Description          : Lab 2.4 PowerShell automation test account

[6] Validating expected account state...
Initial account validation PASSED.

[7] Testing account disable operation...
Disable operation validated successfully.

[8] Testing account enable operation...
Enable operation validated successfully.

[9] Performing final account validation...

Name                 : Lab Automation User01
SamAccountName       : labauto01
UserPrincipalName    : labauto01@corp.lab
Enabled              : True
PasswordLastSet      :
PasswordExpired      : True
PasswordNeverExpires : False
Description          : Lab 2.4 PowerShell automation test account

# Independent Account Validation

After the automation completed, the resulting account was queried independently.

Command:

```powershell
Get-ADUser -Identity labauto01 -Properties Enabled,PasswordLastSet,PasswordExpired,PasswordNeverExpires,Description |
Select-Object Name,SamAccountName,Enabled,PasswordLastSet,PasswordExpired,PasswordNeverExpires,Description
```

Output:

```text
Name                 : Lab Automation User01
SamAccountName       : labauto01
Enabled              : True
PasswordLastSet      :
PasswordExpired      : True
PasswordNeverExpires : False
Description          : Lab 2.4 PowerShell automation test account
```

Interpretation:

The independently retrieved account matched the expected final state.

The account was:

* present in Active Directory
* enabled
* configured to require a password change
* not configured with a non-expiring password
* correctly associated with the automation laboratory

This independent query provided a second validation path outside the automation script itself.

---

# PowerShell Syntax Validation

The saved automation script was checked using the PowerShell language parser.

Command:

```powershell
$errors = $null

[System.Management.Automation.Language.Parser]::ParseFile(
    "C:\Lab-2.4-User-Automation.ps1",
    [ref]$null,
    [ref]$errors
)

if ($errors.Count -eq 0) {
    Write-Host "SYNTAX TEST PASSED: No PowerShell parsing errors detected." -ForegroundColor Green
}
else {
    Write-Host "SYNTAX TEST FAILED: PowerShell parsing errors detected." -ForegroundColor Red
    $errors | Select-Object Message,Extent
}
```

Result:

```text
SYNTAX TEST PASSED: No PowerShell parsing errors detected.
```

This confirmed that the saved `.ps1` file contained no PowerShell parsing errors.

## Interactive Prompt Test

During validation, an `else` error was encountered while manually entering an `if`/`else` construct at the interactive PowerShell prompt.

The command was entered as separate interactive submissions, resulting in:

```text
else : The term 'else' is not recognized as the name of a cmdlet,
function, script file, or operable program.
```

This was an interactive command-entry issue and **not a parsing error in the saved PowerShell script**.

The saved script itself passed the PowerShell parser validation shown above.

---

# Automation Safety Controls

The automation contains several safeguards designed to reduce unintended changes.

## Domain Validation

The script verifies that the expected Active Directory domain is:

```text
corp.lab
```

If another domain is detected, the script stops execution.

```powershell
if ($DomainInfo.DNSRoot -ne "corp.lab") {
    throw "Unexpected domain detected: $($DomainInfo.DNSRoot)"
}
```

This prevents the script from continuing against an unexpected Active Directory environment.

---

## Existing Account Protection

The script checks whether the target account already exists.

```powershell
$ExistingUser = Get-ADUser -Filter "SamAccountName -eq '$Username'"

if ($ExistingUser) {
    throw "Account '$Username' already exists. Script stopped to prevent unintended modification."
}
```

If `labauto01` already exists, the script stops instead of modifying the existing identity.

This is an important safety control when developing administrative automation.

---

## Secure Password Input

The password is collected interactively using:

```powershell
$Password = Read-Host "Password" -AsSecureString
```

The password is therefore not hard-coded into the script.

Passwords are deliberately excluded from repository documentation and evidence.

---

## Error Handling

Active Directory operations use:

```powershell
-ErrorAction Stop
```

This ensures that terminating errors stop the workflow rather than allowing subsequent operations to continue against an unexpected state.

For example:

```powershell
New-ADUser `
    -Name $Name `
    -SamAccountName $Username `
    -UserPrincipalName "$Username@$Domain" `
    -AccountPassword $Password `
    -Enabled $true `
    -ChangePasswordAtLogon $true `
    -Description $Description `
    -ErrorAction Stop
```

This creates a clear failure boundary around the account-creation operation.

---

## Post-Operation Validation

The automation validates the result of administrative actions rather than assuming that successful command execution automatically means the desired state was achieved.

For example:

```text
Disable Account
      ↓
Retrieve Account
      ↓
Check Enabled = False
```

and:

```text
Enable Account
      ↓
Retrieve Account
      ↓
Check Enabled = True
```

This distinction between **action success** and **state validation** is important in administrative automation.

---

# Manual Administration Versus Automation

The manual workflow required individual commands and validation steps:

```text
Administrator
     ↓
Command
     ↓
Active Directory
     ↓
Validation
     ↓
Next command
```

The automated workflow encoded the same administrative logic into a repeatable process:

```text
PowerShell Script
       ↓
Precondition
       ↓
Action
       ↓
Validation
       ↓
Next action
       ↓
Final validation
```

The primary advantage is therefore not simply speed.

Automation provides a mechanism for applying the same administrative logic repeatedly and consistently.

However, automation also increases potential impact.

A manual mistake may affect one identity.

A poorly designed automation process may affect many identities.

Therefore, increased automation should be accompanied by stronger validation, controlled testing and clear failure conditions.

---

# Why the Automation Was Tested Against a Separate Account

The existing account `labuser01` had already been used for manual administration.

A separate account, `labauto01`, was therefore used for automation testing.

This provided a controlled test target and prevented the automation workflow from unnecessarily changing the state of the previously configured account.

The approach demonstrates a basic controlled-testing methodology:

```text
Dedicated test identity
        ↓
Execute automation
        ↓
Validate results
        ↓
Review final state
        ↓
Only then consider broader use
```

New automation should be validated against a dedicated test identity or controlled test environment before being applied to production identities.

---

# Automation and Scale

Manual administration becomes increasingly inefficient as the number of identities increases.

For example:

```text
1 user
   ↓
Manual administration may be practical

100 users
   ↓
Repetition becomes expensive

1,000+ users
   ↓
Automation becomes increasingly important
```

In enterprise environments, PowerShell automation can be combined with:

* identity management systems
* HR-driven provisioning
* Microsoft Entra ID
* Microsoft Graph
* privileged access controls
* ticketing workflows
* approval processes
* logging
* SIEM platforms

The security consideration is that automation increases both efficiency and potential impact.

The larger the scope of an automated operation, the more important it becomes to establish:

* clear preconditions
* restricted scope
* validation
* error handling
* auditability
* controlled testing

---

# Security Relevance

PowerShell automation is directly relevant to identity and security operations.

The same administrative concepts demonstrated here can be extended to:

* user provisioning
* user deprovisioning
* account disablement
* group membership management
* privileged access management
* access reviews
* password management
* identity lifecycle workflows
* security remediation

The underlying security principle is:

```text
Automation
+
Preconditions
+
Controlled testing
+
Validation
+
Error handling
=
Safer administration
```

This methodology is particularly relevant to identity-focused security work because identity changes can directly affect authentication and authorisation.

---

# Skills Demonstrated

This laboratory demonstrated:

* Windows Server administration
* Windows PowerShell 5.1
* Active Directory administration
* Active Directory user management
* Password policy interaction
* Account state management
* Secure password input
* PowerShell conditional logic
* PowerShell error handling
* PowerShell syntax validation
* Automated identity administration
* State-based validation
* Independent verification
* Controlled testing methodology
* Evidence-based technical documentation

---

# Repository Structure

The laboratory is intentionally kept minimal.

```text
2.4-Active-Directory-User-Administration-Fundamentals
└── README.md
```

No separate evidence directory is required.

PowerShell commands, automation code, validation results and relevant outputs are contained directly within this README.

Passwords and other credentials are deliberately excluded.

---

# Technical Findings

The laboratory demonstrated that Active Directory user administration performed manually can be translated into a controlled PowerShell automation workflow.

The manual administration phase successfully demonstrated:

* Active Directory user password administration
* Domain password-policy enforcement
* Password-change requirements
* Account disablement
* Account-state validation

The automation phase successfully demonstrated:

* Active Directory domain validation
* Target-account existence checking
* Protection against modification of an existing target account
* Secure password input
* Active Directory user creation
* Initial account-state validation
* Account disablement
* Disabled-state validation
* Account re-enablement
* Enabled-state validation
* Independent final-state verification
* PowerShell syntax validation

The final independently verified account state was:

```text
Enabled              : True
PasswordExpired      : True
PasswordNeverExpires : False
```

The laboratory therefore demonstrated both **manual identity administration** and **controlled administrative automation**.

---

# Conclusion

This laboratory extended the Active Directory administration work from Lab 2.3 into Active Directory user administration and controlled PowerShell automation.

The key lesson was that effective automation is not simply a collection of commands executed automatically.

A robust administrative workflow should define:

```text
What must be true before execution
        ↓
What action should be performed
        ↓
What should happen if something is wrong
        ↓
What state should exist afterwards
        ↓
How that state will be verified
```

This methodology provides a foundation for more advanced Microsoft infrastructure and identity automation.

The concepts demonstrated here can later be extended to:

* Active Directory organisational units
* Group Policy administration
* Active Directory group management
* Microsoft Entra ID automation
* Microsoft Graph
* Azure administration
* identity lifecycle automation
* security remediation
* detection and response workflows

This laboratory provides a practical bridge between traditional Windows Server administration and modern automation-oriented identity and security engineering.

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

2.4 Active Directory User Administration Fundamentals

        ↓

2.5 Windows Server Security Fundamentals
```

This phase develops infrastructure knowledge supporting:

* Microsoft Systems Administration
* Hybrid Identity
* Microsoft Entra ID
* Azure Administration
* Identity Security
* Detection Engineering



