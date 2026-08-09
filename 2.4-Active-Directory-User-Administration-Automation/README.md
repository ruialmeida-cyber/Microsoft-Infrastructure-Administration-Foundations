# 2.4 2.4-Active-Directory-User-Administration-Fundamentals

**Status:** Completed

**Platform:** Oracle VirtualBox

**Technology:** Microsoft Windows Server 2025 Standard Evaluation

**Domain:** Microsoft Infrastructure Administration | Identity and Access Management

---

# Overview

This laboratory extends the Active Directory administration work from Lab 2.3 into PowerShell-based administration and automation.

The objective was to demonstrate how common Active Directory administrative tasks can be performed manually with PowerShell, then converted into a repeatable automation workflow with built-in validation.

The laboratory focuses on:

* PowerShell administration
* Active Directory user management
* Password and account-state management
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

The account password was reset using:

```powershell
Set-ADAccountPassword -Identity labuser01 -Reset
```

The password was entered interactively.

An initial password attempt failed because it did not meet the domain password requirements.

PowerShell returned:

```text
Set-ADAccountPassword : The password does not meet the length,
complexity, or history requirement of the domain.
```

This demonstrated that Active Directory domain password policy was being enforced.

A compliant password was subsequently accepted.

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

The account was enabled but the password was marked as expired, requiring the user to change it at logon.

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
Lab User01 False
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

PowerShell automation provides a way to encode the administrative process into a repeatable workflow.

The objective is therefore not simply to execute commands faster.

The objective is to make the administrative logic:

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

This represents a basic infrastructure-as-code principle:

> Define the expected state, perform the change, and verify the resulting state.

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

The password was not written into the PowerShell script.

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
```

The automation therefore successfully demonstrated:

* domain validation
* account existence checking
* account creation
* initial state validation
* account disablement
* disabled-state validation
* account re-enablement
* enabled-state validation
* final state validation

---

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

---

# PowerShell Syntax Validation

The saved script was also checked using the PowerShell language parser.

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

This confirmed that the saved PowerShell script contained no PowerShell parsing errors.

> Note: An `else` error was encountered while manually entering an `if`/`else` block at the interactive PowerShell prompt. This occurred because the two blocks were entered as separate commands. It was not a parsing error in the saved `.ps1` file.

The saved script itself passed the parser validation.

---

# Automation Safety Controls

The automation contains several safeguards.

## Domain Validation

The script verifies that the expected domain is:

```text
corp.lab
```

If another domain is detected, the script stops.

---

## Existing Account Protection

The script checks whether:

```text
labauto01
```

already exists.

If it does, the script stops:

```powershell
if ($ExistingUser) {
    throw "Account '$Username' already exists. Script stopped to prevent unintended modification."
}
```

This prevents the script from blindly modifying an existing account.

---

## Secure Password Input

The password is collected with:

```powershell
$Password = Read-Host "Password" -AsSecureString
```

The password is therefore not hard-coded into the script.

Passwords are deliberately excluded from repository documentation.

---

## Error Handling

Active Directory operations use:

```powershell
-ErrorAction Stop
```

This prevents the script from silently continuing after an operation fails.

---

## Post-Operation Validation

The script validates the result of administrative actions rather than assuming success.

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

The automated workflow encodes the same logic into a repeatable process:

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

The main advantage is therefore not simply speed.

Automation provides a mechanism for applying the same administrative logic repeatedly and consistently.

---

# Why the Automation Was Tested Against a Separate Account

The existing account `labuser01` had already been used for manual administration.

A separate account, `labauto01`, was therefore created for automation testing.

This provided a controlled test target and prevented the automation workflow from accidentally changing the state of the previously configured account.

The approach demonstrates a basic principle used in professional infrastructure administration:

```text
Test environment
      ↓
Controlled identity
      ↓
Validate automation
      ↓
Review results
      ↓
Controlled deployment
```

Production identities should not be treated as the first test target for a new automation workflow.

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

In enterprise environments, PowerShell can be combined with:

* identity management systems
* HR-driven provisioning
* Microsoft Entra ID
* Microsoft Graph
* privileged access controls
* ticketing workflows
* approval processes
* logging
* SIEM platforms

The important security consideration is that automation increases both efficiency and potential impact.

A manual error may affect one account.

A poorly designed automation process may affect many accounts.

Therefore automation requires stronger validation rather than less validation.

---

# Security Relevance

PowerShell automation is directly relevant to identity and security operations.

The same concepts demonstrated here can be extended to:

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
2.4-PowerShell-Administration-Fundamentals
└── README.md
```

No separate evidence directory is required.

PowerShell commands, scripts, validation results and relevant outputs are contained directly within this README.

Passwords and other credentials are deliberately excluded.

---

# Technical Findings

The laboratory demonstrated that Active Directory administration performed manually can be translated into a controlled PowerShell automation workflow.

The automation successfully:

* validated the Active Directory domain
* checked for an existing target account
* prevented modification of an existing target account
* securely requested a password
* created an Active Directory user
* validated the resulting account
* disabled the account
* validated the disabled state
* re-enabled the account
* validated the enabled state
* independently verified the final state
* passed PowerShell syntax validation

The final independently verified account state was:

```text
Enabled              : True
PasswordExpired      : True
PasswordNeverExpires : False
```

---

# Conclusion

This laboratory extended the Active Directory administration work from manual PowerShell commands into controlled automation.

The key lesson was that effective automation is not simply a collection of commands executed automatically.

A robust administrative script should define:

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

This laboratory therefore provides a practical bridge between traditional Windows Server administration and modern automation-oriented identity and security engineering.

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

The phase develops infrastructure knowledge supporting:

* Microsoft Systems Administration
* Hybrid Identity
* Microsoft Entra ID
* Azure Administration
* Identity Security
* Detection Engineering
