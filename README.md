# Connecting to the PsGallery Repository via a Corporate Proxy
When running Get-PsRepository behind a corporate proxy you may get the error below  

WARNING: Unable to find module repositories.

To resolve this open PowerShell in elevated mode and run the following:
```
notepad $PROFILE
```
This will start notepad and open your PowerShell profile. If the file doesn’t exist, Notepad will prompt you to create it.

Add these lines to the profile script:
```
[system.net.webrequest]::defaultwebproxy = new-object system.net.webproxy('http://ProxyHostName:ProxyPortNumber')
[system.net.webrequest]::defaultwebproxy.credentials = [System.Net.CredentialCache]::DefaultNetworkCredentials
[system.net.webrequest]::defaultwebproxy.BypassProxyOnLocal = $true
```
Save, close and restart PowerShell.
# Versions of PowerShell for Managing Office 365
There are two versions of the PowerShell module that you use to connect to Office 365 and administer user accounts, groups, and licenses:
- Azure Active Directory PowerShell for Graph (cmdlets include AzureAD in their name)
- Microsoft Azure Active Directory Module for Windows PowerShell (cmdlets include MSol in their name)
### Install Azure Active Directory PowerShell for Graph
```PowerShell
Install-Module -Name AzureAD
```
### Install Microsoft Azure Active Directory Module for Windows PowerShell
```PowerShell
Install-Module MSOnline
```
# User Administration via PowerShell
#### Initiate a connection to Azure Active Directory
This command attempts to initiate a connection with Azure Active Directory. Since no credential is provided, the cmdlet prompts you to enter your username and password.
```
Connect-MsolService
```
### Gets users from Azure Active Directory
```
Get-MsolUser
```
### Displays all of the properties for the user from Azure Active Directory and displays in a list format
```
Get-MsolUser -UserPrincipalName user@domain.com | Format-List -Property *
```
#### Other variations of the Get-MsolUser cmdlet
```
Get-MsolUser -UnlicensedUsersOnly
```
```
Get-MsolUser | Select-Object DisplayName, Department, UsageLocation
```
### Get all the SKUs and AccountSkuIds that the company owns
```
Get-MsolAccountSku
```
### View details about the Office 365 services that are available in all of your license plans
```
Get-MsolAccountSku | Select -ExpandProperty ServiceStatus
```
### View details about the Office 365 services that are assigned to a particular user.
Replace user@domain.com with the User Principal Name.
```
(Get-MsolUser -UserPrincipalName user@domain.com).Licenses.ServiceStatus
```
### Disable specific service plans when assigning a user a license
The New-MsolLicenseOptions cmdlet creates a License Options object. This cmdlet disables specific service plans when assigning a user a license using the Set-MsolUserLicense cmdlet.

The following example creates a LicenseOptions object that disables most services in the licensing plan named reseller-account:ENTERPRISEPACK (Office 365 Enterprise E3) except Office 365 ProPlus, Office for the web, Exchange Online and SharePoint Online.
```
$LO = New-MsolLicenseOptions -AccountSkuId "reseller-account:ENTERPRISEPACK" -DisabledPlans KAIZALA_O365_P3, WHITEBOARD_PLAN2, MIP_S_CLP1, MYANALYTICS_P2, BPOS_S_TODO_2, FORMS_PLAN_E3, STREAM_O365_E3, Deskless, FLOW_O365_P2, POWERAPPS_O365_P2, TEAMS1, PROJECTWORKMANAGEMENT, SWAY, INTUNE_O365, YAMMER_ENTERPRISE, RMS_S_ENTERPRISE, MCOSTANDARD
```
### Create a new user account with license and disable services described in the previous section

The following example creates a new account for Joe Bloggs that assigns the license and disables the services described in the previous section and sets their location to United Kingdom
```
New-MsolUser -UserPrincipalName JoeBloggs@domainname.co.uk -DisplayName "Joe Bloggs" -FirstName Joe -LastName Bloggs -LicenseAssignment reseller-account:ENTERPRISEPACK -LicenseOptions $LO -UsageLocation GB
```
### Create multiple user accounts with license and disable services described in the previous section
This example creates the user accounts from the file named C:\FolderName\InputFile.csv, and logs the results in the file named C:\FolderName\OutputFile.csv  

Firstly, create a comma-separated value (CSV) file that contains the required user account information in the format below
```
UserPrincipalName,FirstName,LastName,DisplayName,UsageLocation,AccountSkuId
ClaudeL@contoso.onmicrosoft.com,Claude,Loiselle,Claude Loiselle,US,contoso:ENTERPRISEPACK
LynneB@contoso.onmicrosoft.com,Lynne,Baxter,Lynne Baxter,US,contoso:ENTERPRISEPACK
ShawnM@contoso.onmicrosoft.com,Shawn,Melendez,Shawn Melendez,US,contoso:ENTERPRISEPACK
```
Secondly, run the command below to create the user accounts. The temporary passwords will be logged to the Output file.
```
Import-Csv -Path "C:\FolderName\InputFile.csv" | foreach {New-MsolUser -DisplayName $_.DisplayName -FirstName $_.FirstName -LastName $_.LastName -UserPrincipalName $_.UserPrincipalName -UsageLocation $_.UsageLocation -LicenseAssignment $_.AccountSkuId -LicenseOptions $LO} | Export-Csv -Path "C:\FolderName\OutputFile.csv"
 ```
### Remove a user from Azure Active Directory
-Force removes a user without confirmation
```
Remove-MsolUser -UserPrincipalName JoeBloggs@domainname.co.uk -Force
```
### Remove a user from the Azure Active Directory Recycle Bin
-Force removes a user from the Recycle Bin without confirmation
```
Remove-MsolUser -UserPrincipalName JoeBloggs@domainname.co.uk -RemoveFromRecycleBin -Force
```



