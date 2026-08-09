2.4 Active Directory User Administration & Automation

Status: Completed

Platform: Oracle VirtualBox

Technology: Microsoft Windows Server 2025 Standard Evaluation

Domain: corp.lab

Administration Method: Windows PowerShell 5.1

Parent Repository: Microsoft Infrastructure Administration Foundations

Overview

This laboratory builds upon the Active Directory infrastructure established in Laboratory 2.3.

The focus of this exercise is Active Directory user administration and the transition from manual administrative operations to repeatable PowerShell automation.

The laboratory demonstrates how an administrator can:

Validate an Active Directory environment
Review domain password policy requirements
Create and manage an Active Directory user
Configure password-related account settings
Enable and disable user accounts
Validate account state using PowerShell
Automate repetitive user administration tasks
Build validation into an administrative PowerShell script
Test automation against a dedicated laboratory account
Collect reproducible evidence through PowerShell

The laboratory deliberately begins with manual administration before introducing automation.

This establishes an understanding of the underlying operations before attempting to automate them.

Objectives

The objectives of this laboratory were:

Validate the Active Directory domain
Review the default domain password policy
Create and manage an Active Directory test user
Configure password requirements
Validate password state
Configure password change requirements
Disable and re-enable an account
Validate account state after each operation
Develop a PowerShell automation script
Implement precondition checks
Implement error handling
Implement post-operation validation
Test the automation against a dedicated laboratory account
Collect evidence for reproducible documentation
Environment
Component	Configuration
Virtualisation Platform	Oracle VirtualBox
Host Operating System	Windows 11
Guest Operating System	Windows Server 2025 Standard Evaluation
Administration Method	Windows PowerShell 5.1
Active Directory Domain	corp.lab
Domain Controller	WIN-URRN4NJRE9I
Test Automation Account	labauto01
Administration Methodology

The laboratory followed an evidence-based administration workflow.

Manual operations were first performed individually to establish an understanding of the required Active Directory operations.

The process was then translated into PowerShell automation.

The automation follows the general workflow:

Validate environment
        ↓
Check preconditions
        ↓
Perform administrative operation
        ↓
Validate result
        ↓
Continue only if expected state exists
        ↓
Perform additional operation
        ↓
Validate again
        ↓
Final independent validation

This approach demonstrates an important distinction between automation and simply executing multiple commands.

A reliable administrative script should contain:

Preconditions
Controlled changes
Error handling
State validation
Clear success/failure conditions
Domain Validation

The Active Directory environment was validated before performing user administration.

Command:

Get-ADDomain

The expected domain was:

corp.lab

The automation script also performs this validation before making any account changes.

The script stops if an unexpected domain is detected.

This prevents the automation from accidentally being executed against the wrong Active Directory environment.

Password Policy Validation

The default domain password policy was reviewed before creating the test account.

Command:

Get-ADDefaultDomainPasswordPolicy |
Select-Object MinPasswordLength,ComplexityEnabled,PasswordHistoryCount,MaxPasswordAge,MinPasswordAge

Validated configuration:

MinPasswordLength    : 7
ComplexityEnabled    : True
PasswordHistoryCount : 24
MaxPasswordAge       : 42.00:00:00
MinPasswordAge       : 1.00:00:00

Interpretation:

The domain requires passwords to meet a minimum length and complexity requirement.

The domain also maintains password history and minimum/maximum password-age controls.

This validation was important because an attempted password reset initially failed because the supplied password did not satisfy the domain policy.

The failure demonstrated why administrators should understand and validate domain policy requirements rather than assuming a password will be accepted.

Manual User Administration

A dedicated Active Directory user was used to demonstrate the manual administration workflow.

User:

Name:
Lab User01

SamAccountName:
labuser01

The account was used to demonstrate:

Password reset
Password validation
Password-change-at-logon configuration
Account disablement
Account-state validation

The account was ultimately disabled after the manual administration exercise.

Password Lifecycle Validation

The user's password state was inspected using:

Get-ADUser -Identity labuser01 `
-Properties PasswordLastSet,PasswordExpired,PasswordNeverExpires |
Select-Object Name,Enabled,PasswordLastSet,PasswordExpired,PasswordNeverExpires

The account initially showed:

PasswordLastSet :
PasswordExpired : True
PasswordNeverExpires : False

After successfully setting a password, validation showed that the password had been established.

The account was subsequently configured to require a password change at logon:

Set-ADUser -Identity labuser01 -ChangePasswordAtLogon $true

Validation confirmed the resulting password-expired state.

This demonstrated how Active Directory exposes password lifecycle state through PowerShell properties.

Account Disablement

The manual account-disable operation was performed with:

Disable-ADAccount -Identity labuser01

The resulting state was validated with:

Get-ADUser -Identity labuser01 |
Select-Object Name,Enabled

Result:

Name         Enabled
----         -------
Lab User01   False

This confirmed that the account had been successfully disabled.

PowerShell Automation

After completing the manual workflow, the same administrative principles were implemented through PowerShell automation.

The automation uses a dedicated test account:

Name:
Lab Automation User01

SamAccountName:
labauto01

The account was created specifically for automation testing.

This prevents the automation test from modifying the previously used administrative test account.

Automation Design

The automation script performs the following operations:

1. Load the Active Directory module
2. Identify the target account
3. Validate the Active Directory domain
4. Check whether the target account already exists
5. Stop if the account already exists
6. Request a password securely
7. Create the Active Directory user
8. Retrieve the newly created account
9. Validate the expected initial state
10. Disable the account
11. Validate the disabled state
12. Re-enable the account
13. Validate the enabled state
14. Perform final independent validation
15. Report PASS or FAIL

The password is requested using:

Read-Host "Password" -AsSecureString

The password is therefore not hard-coded into the script.

Precondition Checking

Before creating the user, the script checks whether the account already exists.

The logic is intentionally fail-safe:

$ExistingUser = Get-ADUser -Filter "SamAccountName -eq '$Username'"

if ($ExistingUser) {
    throw "Account '$Username' already exists. Script stopped to prevent unintended modification."
}

This prevents the automation from unintentionally modifying an existing account.

The script therefore treats an unexpected existing account as a condition requiring administrator review.

Account Creation

The user is created with:

New-ADUser `
    -Name $Name `
    -SamAccountName $Username `
    -UserPrincipalName "$Username@$Domain" `
    -AccountPassword $Password `
    -Enabled $true `
    -ChangePasswordAtLogon $true `
    -Description $Description `
    -ErrorAction Stop

The account is created with:

Enabled:
True

PasswordNeverExpires:
False

Change password at next logon:
Required

Description:
Lab 2.4 PowerShell automation test account
Automated Validation

The script does not assume that a successful command means the desired state exists.

After account creation, the script retrieves the account and validates its properties.

The initial validation checks:

Enabled = True

PasswordExpired = True

PasswordNeverExpires = False

If any expected condition is not met, the script reports failure and stops.

This is an important administrative principle:

Successful execution of a command is not the same as successful achievement of the intended state.

Disable and Re-enable Testing

The automation also tests account lifecycle operations.

The account is disabled:

Disable-ADAccount -Identity $Username -ErrorAction Stop

The script then independently verifies:

Enabled = False

The account is subsequently re-enabled:

Enable-ADAccount -Identity $Username -ErrorAction Stop

The script again validates the resulting state:

Enabled = True

This provides evidence that the automation is capable of performing and validating both account-disable and account-enable operations.

Final Validation

The script performs a final independent query of the account.

The final expected state is:

Enabled:
True

PasswordExpired:
True

PasswordNeverExpires:
False

The script reports:

LAB 2.4 AUTOMATION VALIDATION: PASSED

only when all required conditions are satisfied.

Automation Test Result

The automation script was executed against the dedicated test account:

labauto01

The execution successfully demonstrated:

AD domain validation                 PASSED
Account precondition check           PASSED
User creation                        PASSED
Initial account validation            PASSED
Disable operation                    PASSED
Disable-state validation             PASSED
Enable operation                     PASSED
Enable-state validation              PASSED
Final account validation             PASSED

The test account was left enabled with the expected password-change requirement.

Script Syntax Validation

Before relying on the automation, the PowerShell parser was used to inspect the script.

Validation was performed with:

$errors = $null

[System.Management.Automation.Language.Parser]::ParseFile(
    "C:\Lab-2.4-User-Automation.ps1",
    [ref]$null,
    [ref]$errors
)

if ($errors.Count -eq 0) {
    Write-Host "SYNTAX TEST PASSED: No PowerShell parsing errors detected." -ForegroundColor Green
}

The parser returned:

SYNTAX TEST PASSED: No PowerShell parsing errors detected.

This confirms that the final saved script contains no PowerShell parsing errors.

The subsequent execution test provides a separate level of validation because syntactic correctness alone does not prove that an automation script performs the intended administrative operations.

Validation Layers

The laboratory therefore used multiple validation layers:

Layer 1
PowerShell parser
        ↓
Syntax validation

Layer 2
Active Directory environment
        ↓
Domain validation

Layer 3
Precondition checks
        ↓
Existing-account protection

Layer 4
Administrative operation
        ↓
User creation / disable / enable

Layer 5
State validation
        ↓
Expected AD properties

Layer 6
Final independent validation
        ↓
Automation result

This approach provides stronger assurance than simply executing a sequence of commands.

Evidence

Evidence is maintained primarily through PowerShell output rather than screenshots.

The evidence demonstrates:

Domain configuration
Password policy
Manual user administration
Account state
Automation execution
Script validation
Final account state

The repository intentionally avoids storing passwords or other authentication secrets.

Security Considerations

Although this laboratory focuses on administration rather than security monitoring, the operations have direct security implications.

Identity Lifecycle

User accounts represent identities that must be:

created
maintained
modified
disabled
eventually removed

Automation can reduce inconsistent manual administration when implemented with appropriate safeguards.

Least Privilege

Administrative automation should run using an account with only the permissions required for the operation.

The laboratory uses a dedicated test account to demonstrate the principle of separating automation testing from existing administrative identities.

Fail-Safe Behaviour

The script stops when an unexpected existing account is detected.

This is preferable to blindly modifying an object because the administrator may need to investigate the existing state first.

Credential Handling

Passwords are entered interactively as secure strings.

Passwords are not embedded in the script or committed to GitHub.

Automation and Enterprise Administration

Manually performing every user-administration operation does not scale effectively.

In larger environments, administrators commonly use automation to make repetitive operations:

repeatable
consistent
auditable
faster
less dependent on manual intervention

PowerShell is particularly relevant in Microsoft environments because it provides direct administrative interfaces to Windows Server and Active Directory.

However, automation does not remove the requirement for validation.

A poorly designed script can perform an incorrect operation much faster than a human administrator.

Therefore:

Automation
+
Preconditions
+
Error handling
+
Validation
=
Reliable administration
Skills Demonstrated

This laboratory demonstrates:

Active Directory user administration
PowerShell 5.1
Active Directory PowerShell module
Password-policy analysis
User lifecycle management
Account enable/disable operations
Secure password input
PowerShell scripting
Conditional logic
Error handling
Precondition validation
Post-operation validation
Script syntax validation
Evidence collection
Repeatable administrative workflows
Repository Structure
2.4-Active-Directory-User-Administration-Automation/
│
├── README.md
│
├── Scripts/
│   └── Lab-2.4-User-Automation.ps1
│
└── Evidence/
    └── PowerShell/
        ├── domain-validation.txt
        ├── password-policy-validation.txt
        ├── user-validation.txt
        ├── automation-validation.txt
        └── syntax-validation.txt
Repository Context

This laboratory forms part of:

Phase 2 — Microsoft Infrastructure Administration Foundations

Learning progression:

2.1 Windows Server Administration Foundations
        ↓
2.2 Windows Server Administration Fundamentals
        ↓
2.3 Active Directory Administration Fundamentals
        ↓
2.4 Active Directory User Administration & Automation
        ↓
2.5 Windows Server Security Fundamentals

The progression moves from infrastructure administration towards identity administration and automation.

This provides a foundation for future work involving:

Active Directory security
Group Policy
Microsoft Entra ID
Hybrid Identity
Microsoft Sentinel
Identity monitoring
Detection Engineering
Conclusion

This laboratory demonstrated the transition from manual Active Directory user administration to validated PowerShell automation.

The manual workflow established an understanding of the underlying operations.

The automation phase then reproduced those operations using:

precondition checks
secure credential input
controlled account creation
error handling
state validation
account lifecycle testing
final independent verification

The resulting workflow demonstrates an important principle of infrastructure administration:

Understand manually
        ↓
Automate deliberately
        ↓
Validate automatically
        ↓
Document evidence

This approach provides a practical foundation for more advanced Microsoft identity administration, hybrid identity management and security-focused automation.

# PowerShell Automation

The laboratory was extended from manual Active Directory administration into PowerShell-based automation.

The purpose was to demonstrate that the administrative workflow performed manually can be converted into a repeatable script while retaining validation and safety controls.

The automation was deliberately tested against a dedicated laboratory account rather than an existing administrative or production identity.

---

# Automation Design

The automation follows a controlled sequence:

```text
Validate environment
        ↓
Check whether target account already exists
        ↓
Stop if account already exists
        ↓
Request password securely
        ↓
Create test account
        ↓
Retrieve account
        ↓
Validate expected state
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

This demonstrates an important principle of infrastructure automation:

> Automation should not simply execute commands. It should also verify that the expected state was actually achieved.

---

# Automated Test Account

A dedicated Active Directory test account was used:

```text
Name:
Lab Automation User01

SamAccountName:
labauto01

Domain:
corp.lab

Description:
Lab 2.4 PowerShell automation test account
```

The account was created specifically to validate the automation workflow.

This avoided modifying the previously created `Lab User01` account.

---

# PowerShell Automation Script

The complete automation workflow was implemented in a single PowerShell script.

The script:

* imports the Active Directory module
* validates the expected domain
* checks whether the target account already exists
* stops if the account already exists
* requests the password as a `SecureString`
* creates the account
* validates the resulting account
* tests disabling the account
* validates the disabled state
* tests re-enabling the account
* validates the enabled state
* performs a final independent validation
* reports an explicit PASS or FAIL result

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
Write-Host "The password will NOT be written to the script or evidence."

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

The script requested the password interactively.

The password was supplied as a secure string and was not stored in the script.

---

# Automation Execution Result

The automation successfully completed the following operations:

```text
[1] Validating Active Directory environment...
AD domain validated: corp.lab

[2] Checking whether labauto01 already exists...
Account does not exist. Safe to create.

[3] Enter the temporary password for labauto01.

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

The script therefore demonstrated successful automated creation, validation, disabling, re-enabling and final state verification.

---

# Independent Validation

The account was independently queried after the automation completed.

Command:

```powershell
Get-ADUser -Identity labauto01 `
    -Properties Enabled,PasswordLastSet,PasswordExpired,
                PasswordNeverExpires,Description |
Select-Object Name,SamAccountName,Enabled,PasswordLastSet,
              PasswordExpired,PasswordNeverExpires,Description
```

Observed result:

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

The independently queried Active Directory object matched the expected final state.

The account was:

* present
* enabled
* configured to require a password change
* not configured with a non-expiring password
* correctly described as the laboratory automation account

---

# Script Syntax Validation

Before relying on the automation script, its PowerShell syntax was independently checked using the PowerShell parser.

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

The subsequent `else` error observed while entering the validation manually was caused by entering the `if` and `else` blocks as separate PowerShell commands at the interactive prompt.

It was not a parsing error in the saved `.ps1` script.

The parser result itself confirmed:

```text
No PowerShell parsing errors detected.
```

---

# Automation Safety Controls

The script contains several controls intended to prevent unintended modification.

## Domain validation

The script checks that the expected Active Directory domain is:

```text
corp.lab
```

If a different domain is detected, execution stops.

## Existing-account protection

The script checks whether:

```text
labauto01
```

already exists.

If it exists, the script stops rather than modifying the existing account.

This is particularly important because automation can amplify mistakes.

## Secure password input

The password is requested using:

```powershell
Read-Host "Password" -AsSecureString
```

The password is therefore not embedded in the script.

Passwords are deliberately excluded from repository documentation.

## Explicit error handling

Active Directory operations use:

```powershell
-ErrorAction Stop
```

This allows the script to stop rather than silently continuing after an operational failure.

## State validation

The script does not assume that an operation succeeded.

For example:

```text
Disable account
      ↓
Retrieve account
      ↓
Confirm Enabled = False
```

and:

```text
Enable account
      ↓
Retrieve account
      ↓
Confirm Enabled = True
```

This creates an execute → validate workflow.

---

# Manual Administration vs Automation

The laboratory demonstrates the difference between individual administration and repeatable automation.

Manual administration:

```text
Administrator
     ↓
Command
     ↓
User
     ↓
Validation
```

Automation:

```text
PowerShell Script
       ↓
Environment Validation
       ↓
Pre-condition Check
       ↓
Action
       ↓
Post-condition Validation
       ↓
Result
```

The objective of automation is therefore not simply to reduce typing.

The greater benefit is repeatability and consistent enforcement of administrative logic.

---

# Why a Dedicated Test Account Was Used

The automation was deliberately tested against:

```text
labauto01
```

rather than the previously configured:

```text
labuser01
```

This provided isolation between the manual administration exercise and the automation exercise.

The existing account had already been used to demonstrate:

* password reset
* password expiration
* password change at logon
* account disabling

The new account provided a clean target for testing the complete automation workflow.

This is analogous to the use of a test identity in a controlled administrative environment before applying automation to live identities.

---

# Administrative Automation Principles Demonstrated

The laboratory demonstrates several principles relevant to enterprise PowerShell administration.

### 1. Preconditions

Before changing anything, the script checks the environment.

### 2. Idempotency awareness

The script does not blindly recreate or modify an existing account.

If the target already exists, execution stops.

### 3. Secure input

Credentials are not hard-coded into the script.

### 4. Explicit failure

Unexpected conditions cause the script to stop rather than continue silently.

### 5. Post-condition validation

The script verifies the resulting Active Directory state.

### 6. Independent validation

The resulting account was also queried outside the automation workflow.

### 7. Evidence-based administration

The process produces PowerShell output that can be documented and reviewed.

---

# Security Relevance

PowerShell automation is directly relevant to identity and security operations.

The same principles can be applied to larger workflows involving:

* account provisioning
* account deprovisioning
* group membership management
* access reviews
* privileged account administration
* password policy enforcement
* identity lifecycle management
* security remediation

However, automation also increases the potential impact of an error.

A manual mistake may affect one object.

A poorly designed automation script can affect hundreds or thousands.

Therefore:

```text
Automation
+
Preconditions
+
Validation
+
Controlled testing
+
Logging
=
Safer administration
```

This principle is particularly relevant to identity-focused security engineering.

---

# Laboratory Outcome

The laboratory successfully demonstrated a progression from manual Active Directory administration to controlled PowerShell automation.

The final workflow covered:

```text
Active Directory environment validation
        ↓
Account existence validation
        ↓
Secure credential collection
        ↓
Automated account creation
        ↓
Automated state validation
        ↓
Automated account disabling
        ↓
Automated disabled-state validation
        ↓
Automated account re-enablement
        ↓
Automated enabled-state validation
        ↓
Independent final validation
```

The automation was successfully executed against the dedicated laboratory account:

```text
labauto01
```

The final independently verified state was:

```text
Enabled              : True
PasswordExpired      : True
PasswordNeverExpires : False
```

---

# Final Technical Findings

The laboratory established that PowerShell can be used to automate Active Directory administration while retaining explicit validation controls.

Key findings:

* Active Directory can be administered programmatically through the PowerShell Active Directory module.
* Administrative workflows can be represented as conditional sequences.
* Preconditions can prevent unintended modifications.
* Secure password handling prevents credentials from being embedded in automation code.
* Post-operation validation can confirm that the intended state was achieved.
* Dedicated test identities provide a safer environment for validating automation.
* Independent validation provides additional confidence in automation results.
* Automation increases both efficiency and the potential blast radius of mistakes.

---

# Skills Demonstrated

This laboratory demonstrated:

* Windows Server administration
* Windows PowerShell 5.1
* Active Directory PowerShell administration
* Active Directory user management
* Secure credential handling
* Conditional logic
* Error handling
* State validation
* PowerShell parsing and syntax validation
* Automation design
* Identity lifecycle concepts
* Evidence-based administration
* Controlled testing methodology

---

# Repository Structure

The completed laboratory intentionally keeps the implementation minimal.

No separate evidence files are required.

The repository structure is:

```text
2.4-PowerShell-Administration-Fundamentals
│
└── README.md
```

All relevant:

* PowerShell commands
* automation code
* validation commands
* observed outputs
* technical findings
* security interpretation

are contained within this README.

This keeps the laboratory self-contained and makes the complete administration workflow readable from a single document.

---

# Conclusion

This laboratory extended the Windows Server and Active Directory administration work from manual command execution into controlled PowerShell automation.

The important transition was not simply learning how to write a script.

It was learning how to design automation around:

```text
Precondition
     ↓
Action
     ↓
Validation
     ↓
Failure handling
     ↓
Final state
```

This approach provides a foundation for more advanced infrastructure and identity automation.

The same methodology can later be extended to:

* Active Directory group administration
* organisational unit management
* Group Policy administration
* Microsoft Entra ID automation
* Azure resource administration
* identity lifecycle workflows
* security remediation
* Microsoft Sentinel response automation

The laboratory therefore establishes a practical bridge between traditional Windows administration and the automation-oriented workflows used in modern cloud, identity and security engineering.
