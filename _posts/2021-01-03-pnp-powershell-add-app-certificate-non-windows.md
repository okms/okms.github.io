---
layout: single
title:  "PnP.PowerShell how to import app certificates in a non-windows environment"
date:   2021-01-03 22:00:00 +0100
# excerpt: "Valid fields causes REST voes in Microsoft 365 and SharePoint"
header:
  overlay_filter: rgba(0, 0, 0, 0.6)
  overlay_image: /assets/img/2021-01-03/pnp.powershell.png
  show_overlay_excerpt: false
categories: SharePoint, Microsoft365, PowerShell

---

This post is relevant for users of the new [PnP.PowerShell](https://github.com/pnp/powershell) module, built for PowerShell 7.1, especially those of you running PnP PowerShell on a non-windows machine or in WSL.

If you're using PnP PowerShell though e.g. Azure Automation, you should look into authenticating against your Microsoft 365 tenant using an Azure app. Setting this up manually can be a bit of a hassle, so especially for test and dev sites Im a big fan using `Initialize-PnPPowerShellAuthentication` ([documentation](https://pnp.github.io/powershell/cmdlets/released/Initialize-PnPPowerShellAuthentication.html)). This cmdlet walks you through setting up a new Azure AD App with a self signed certificate. I will not go throgh how this setup process works as there are other [good posts on the topic](https://blog.sperre.org/2020/04/17/azure-ad-app-authentication.html). However, if you're doing this in a non-windows environment you will still have to import your certificate into the local certificate store.

The following will let you do this. Copy, add the path to your `.pfx` file and paste into a terminal


```powershell
$PathToCertificate = "<path to your pfx file>" # e.g. /home/okms/PnP PowerShell.pfx
$StoreName = [System.Security.Cryptography.X509Certificates.StoreName]::My 
$StoreLocation = [System.Security.Cryptography.X509Certificates.StoreLocation]::CurrentUser 
$Store = [System.Security.Cryptography.X509Certificates.X509Store]::new($StoreName, $StoreLocation) 
$Flag = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable 
$Certificate = [System.Security.Cryptography.X509Certificates.X509Certificate2]::new($PathToCertificate,"",$Flag) 
$Store.Open([System.Security.Cryptography.X509Certificates.OpenFlags]::ReadWrite) 
$Store.Add($Certificate) 
$Store.Close() 
```

Here's the same lines of script wrapped in a function which can be convenient if you want to add this to your `$PROFILE` or need it as part of a script.

```powershell
function() {
  [CmdletBinding()]
  Param(
    [Parameter(Mandatory = $true)]
    [string]$PathToCertificate,
  )
  Write-Verbose ("Adding certificate '{0}' to the local store" -f $PathToCertificate)
  $StoreName = [System.Security.Cryptography.X509Certificates.StoreName]::My 
  $StoreLocation = [System.Security.Cryptography.X509Certificates.StoreLocation]::CurrentUser 
  $Store = [System.Security.Cryptography.X509Certificates.X509Store]::new($StoreName, $StoreLocation) 
  $Flag = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable 
  $Certificate = [System.Security.Cryptography.X509Certificates.X509Certificate2]::new($PathToCertificate,"",$Flag) 
  $Store.Open([System.Security.Cryptography.X509Certificates.OpenFlags]::ReadWrite) 
  $Store.Add($Certificate) 
  $Store.Close()
  Write-Verbose ("Certificate successfully added")
}
```

