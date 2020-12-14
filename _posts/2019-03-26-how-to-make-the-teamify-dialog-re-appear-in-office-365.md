---
title: How to make the teamify dialog re-appear in Office 365
published: true
description: Quick tip for those who want to show the teamify dialog again after permanently hiding it
categories: SharePoint, Microsoft365
header:
   image: 
   overlay_filter: rgba(0, 0, 0, 0.5)
   overlay_image: /assets/img/2019-03-26/yual1r4bb6hxcfa8oi9i.png
---

Here's a quick tip for those who have permanently dissmissed the "Teamify" site dialog in an Office365 group site.

![Teamify dialog screenshot](/assets/img/2019-03-26/t6ra3p9zzs9qf101hwzf.jpg "The teamify dialog in all its glory")

## PnP PowerShell snippet

```
$siteUrl = "<full url to your site collection>"
# Show Teamify prompt again
$site = Get-PnPTenantSite -url $siteUrl 
# Temporarily disable NoScript
$site.DenyAddAndCustomizePages = [Microsoft.Online.SharePoint.TenantAdministration.DenyAddAndCustomizePagesStatus]::Disabled
$site.Update()
$site.Context.Load($site)
$site.Context.ExecuteQuery()

Connect-PnPOnline -Url $siteUrl
Remove-PnPPropertyBagValue -Key TeamifyHidden

# re-enable NoScript
$site.DenyAddAndCustomizePages = [Microsoft.Online.SharePoint.TenantAdministration.DenyAddAndCustomizePagesStatus]::Enabled
$site.Update()
$site.Context.ExecuteQuery()

```

The short version of what this script does is that it temporarily removes the NoScript limitation enforced on all group sites in Office 365. This is done so we can remove the "TeamifyHidden" propertybag value which is what controls whether or not the dialog should be visible. Finally we re-enable NoScript. 

For those who don't know, the teamify dialog allows you to create an Office 365 team attached to your existing group site. There are of course other ways of doing this, the most common being to attach your existing group site to a new Team through the Teams interface 

![create a team from an existing site](/assets/img/2019-03-26/5yt3ihwr14jsibvucyy4.jpg "create a team from an existing site")

