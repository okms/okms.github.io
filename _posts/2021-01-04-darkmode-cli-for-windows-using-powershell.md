---
layout: single
title:  "Darkmode cli for Windows using PowerShell"
date:   2021-01-04 09:00:00 +0100
excerpt: "Easily toggle darkmode from the Windows command line"
header:
  overlay_filter: rgba(0, 0, 0, 0.6)
  overlay_image: /assets/img/2021-01-04/header.jpg
  show_overlay_excerpt: true
categories: PowerShell

---

On macOS I've recently grown very fond of [dark-mode-cli](https://github.com/sindresorhus/dark-mode-cli). It's a small command-line utility which you can easily install via 

`npm install --global dark-mode-cli` 

While this specific util is macOS only, my main work computers are Windows 10 based. To have something similar when im in Windows, I made function which can be used in PowerShell (works in both Windows PowerShell and PowerShell 7.1 and newer). 

It's heavily inspired by dark-mode-cli, and can be made more PowerShell-like by e.g. having switches and boolean return values instead of status strings, for my use this is sufficient. I execute this command manually whenever i feel my eyes need some rest or I need improved contrast from a light theme. You can of course set up scheduled tasks toggling darkmode automatically at specific times. 

# Usage

```powershell
# Enable darkmode
Set-DarkMode On

# Disable darkmode
Set-DarkMode Off

# Get current status returns the current status
Set-DarkMode Status
```

# How to install

Edit your `$PROFILE` using your favourite editor and add the following. After you've done this start a new PowerShell session

```powershell
function Set-DarkMode {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [ValidateSet("On","Off","Status")]
        [string]$State        
    )

    if($State -eq "Status"){
        $props = Get-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize 
        if($props.AppsUseLightTheme -eq 1 -and $props.SystemUsesLightTheme -eq 1){
            return "Off"            
        }
        return "On"
    }     

    [int]$UsesLightTheme = 0 
    if($State -eq "Off"){        
        $UsesLightTheme = 1
    } 

    New-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize -Name SystemUsesLightTheme -Value $UsesLightTheme -Type Dword -Force | Out-Null
    New-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize -Name AppsUseLightTheme -Value $UsesLightTheme -Type Dword -Force | Out-Null
}
```

# App configuration 
Make sure you configure apps to honor system settings for light or dark theme. Here is an example from Office, it's often similar in other apps. One notable exception is Microsoft Teams where there is no setting for honoring system defaults.

![setting the office theme](/assets/img/2021-01-04/office-theme.png)