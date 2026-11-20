# Lab 01: Deploying a Microsoft Entra ID Developer Tenant & Scripted Provisioning

[⬅️ Back to Labs](./README.md) | [Next: Conditional Access Lab ➡️](./02_Lab_Configuring_Conditional_Access_Policies.md)

---

### Objective
Create a free Microsoft 365 Developer sandbox environment, acquire an Entra ID P2 license, and execute a PowerShell script to automate the creation of departments, groups, and users.

### Step 1: Sign Up for Free M365 Developer Sandbox
1. Navigate to `https://developer.microsoft.com/en-us/microsoft-365/dev-program`.
2. Click **Join Now** and sign in with a personal Microsoft account.
3. Choose a Configurable Sandbox and specify your domain prefix (e.g., `contosocorpdev.onmicrosoft.com`).
4. This gives you a free 90-day renewable Entra ID P2 tenant with 25 E5 licenses.

### Step 2: Install Microsoft Graph PowerShell SDK
Open an administrative PowerShell terminal:
```powershell
Install-Module Microsoft.Graph -Scope CurrentUser -Repository PSGallery -Force
```

### Step 3: Scripted User Creation
Create and execute `provision_users.ps1`:
```powershell
Connect-MgGraph -Scopes "User.ReadWrite.All", "Group.ReadWrite.All"

# Define Departments
$Departments = @("Engineering", "Finance", "SecurityOperations")

foreach ($Dept in $Departments) {
    # Create Departmental Security Group
    $Group = New-MgGroup -DisplayName "Dept-$Dept-Users" -MailEnabled:$false -MailNickname "dept-$Dept" -SecurityEnabled:$true
    Write-Host "Created Group: $($Group.DisplayName)"

    # Create 2 Users per Department
    1..2 | ForEach-Object {
        $UserParams = @{
            AccountEnabled = $true
            DisplayName = "$Dept User $_"
            MailNickname = "$Dept.User$_"
            UserPrincipalName = "$Dept.User$_@YOUR_TENANT.onmicrosoft.com"
            Department = $Dept
            PasswordProfile = @{
                ForceChangePasswordNextSignIn = $true
                Password = "P@ssword123456!"
            }
        }
        $NewUser = New-MgUser @UserParams
        New-MgGroupMember -GroupId $Group.Id -DirectoryObjectId $NewUser.Id
        Write-Host "Created User: $($NewUser.UserPrincipalName)"
    }
}
```
