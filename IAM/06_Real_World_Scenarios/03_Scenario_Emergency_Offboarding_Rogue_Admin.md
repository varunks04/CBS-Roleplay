# Case Study 03: Emergency Offboarding of a Rogue Administrator

[⬅️ Prev: MFA Fatigue](./02_Scenario_MFA_Fatigue_and_Session_Hijacking.md) | [Back to Scenarios](./README.md) | [Next: B2B Federation ➡️](./04_Scenario_Third_Party_Vendor_B2B_Federation.md)

---

### 1. Incident Context
A Senior Systems Administrator with Domain Admin and Azure Global Admin privileges is informed by HR that they are being terminated immediately for gross misconduct. The administrator is working remotely and has active SSH/RDP sessions into production infrastructure.

### 2. The 5-Minute Emergency Protocol Execution

```powershell
# ==============================================================================
# EMERGENCY ISOLATION SCRIPT - ROGUE ADMIN PROTOCOL
# ==============================================================================
$TargetUPN = "rogue.admin@contoso.com"
$TargetSam = "radmin"

# STEP 1: Revoke all active cloud sessions instantly (Continuous Access Evaluation)
Connect-MgGraph -Scopes "User.ReadWrite.All"
Revoke-MgUserSignInsSession -UserId $TargetUPN

# STEP 2: Scramble and disable cloud account
Update-MgUser -UserId $TargetUPN -AccountEnabled:$false -PasswordProfile @{
    ForceChangePasswordNextSignIn = $true
    Password = ([-char[]](33..126) | Get-Random -Count 64) -join ''
}

# STEP 3: Disable Active Directory on-premises account & break Kerberos sessions
Disable-ADAccount -Identity $TargetSam
Set-ADUser -Identity $TargetSam -SmartcardLogonRequired $true # Scrambles password hash

# STEP 4: Terminate active CyberArk PSM jump-host sessions
Invoke-RestMethod -Uri "https://cyberark.corp/api/LiveSessions/TerminateByUser" -Method POST -Body @{UserName = $TargetSam}

# STEP 5: Force remote wipe of enrolled corporate laptops via Intune
$Device = Get-MgDeviceManagementManagedDevice -Filter "userPrincipalName eq '$TargetUPN'"
Invoke-MgWipeDeviceManagementManagedDevice -ManagedDeviceId $Device.Id

Write-Host "Emergency offboarding executed. Blast radius neutralized in 4 minutes."
```
