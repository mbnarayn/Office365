# Connecting to PsGallery Repository via a Corporate Proxy
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


