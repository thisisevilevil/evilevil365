---
title: "Custom requirements for Win32 apps"
date: 2024-07-20
categories:
  - Intune
tags:
  - Win32 apps
  - Intune
  - Custom Requirements
  - PowerShell
---

It's not uncommon for large companies to have issues with grouping, targeting and filtering when migrating from AD and SCCM into a Cloud Native setup. The number 1 complaint I usually hear, is lack of filtering options when assigning apps, which is particularly pertinent for companies with very large and old SCCM Installations with the strangest collection queries you can imagine. This really sets the premise for an entire blog post about grouping, targetting and filtering.

However for now, I will just highlight the simply functionality that's been around for a while in Intune for Win32 apps: Requirements. When you assign apps you can choose requiremente like OS, Disk Size, CPU etc. But you can also choose custom requirements. From this view you can select File and Registry as well, but the one I want to highlight is the Script setting.

With some basic PowerShell you can create any custom filtering you want for assigning your apps. Want to see some examples? Well glad you asked!

## How does it work?

When you add Custom requirement scripts to Intune, you can decide what apps should install/uninstall based on your requirement script. If the requirement is not met, the device will be put under "Not Applicable" just like we know from [Device Filters](https://learn.microsoft.com/en-us/mem/intune/fundamentals/filters). If you navigate to Device Status all devices that does not meet the requirements speciefied in the script will be designated with "PowerShell script requirement rule is not met."

![Win32Requirement](/assets/images/2024-08-03-Win32app-Requirements/Requirement-NotApplicable.png?raw=true "Win32 App Requirement Example 1")

![Win32Requirement](/assets/images/2024-08-03-Win32app-Requirements/Requirement-NotApplicable-1.png?raw=true "Win32 App Requirement Example 2")

### Constructing a simple but useful requirement script

To make the requirement script work, we need to output data to Intune so Intune, or more specifically, the Intune Management Extension should evaluate the resolved app intent or it should place it in "Not Applicable". There's a few ways to do this, using $true or $false statements or simply by writing outputting a specific integer, which is find the most simple in most cases.

Let's take this example:
```PowerShell
if (($(gwmi win32_bios).Manufacturer -like '*Dell*')) {Write-output "1"}
```

When you add the Custom Requirement Script on your Win32 app in Intune, you add the requirement script as a .ps1, and then choose the following values:

* Select output data type: Integer
* Operator: Equals
* Value: 1

Click ok, review & save and see your new Custom requirement rule at work. That's it, it's that simple. and now it's only your PowerShell skills setting the boundaries for how you want to target your Win32 app.
![Win32Requirement](/assets/images/2024-08-03-Win32app-Requirements/Requirement-Construct-1.png?raw=true "Win32 App Requirement Example 2")

Here is a few more examples for you to find as inspiration, I'm using in the field as we speak.

### Requirement for x86/AMD64 devices

```PowerShell
if ($env:Processor_Architecture -eq 'AMD64') {
    Write-output "1"
}
```

#### Requirement for ARM Devices - Very useful since we can't filter on ARM Devices (for now - Probably coming soon? :))

```PowerShell
if ($env:Processor_Architecture -eq 'ARM64') {
    Write-output "1"
}
```

#### Requirement for specific app installed - Great for "Update Only" apps, to patch only existing installations of an app.

```PowerShell
$gpversion = gwmi win32_product | Where {$_.Name -eq 'GlobalProtect'} | Select -ExpandProperty Version
if (!($gpversion -eq '6.3.0')) {Write-output "1"}
```
