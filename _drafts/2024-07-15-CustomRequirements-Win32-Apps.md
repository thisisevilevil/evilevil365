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

It's not uncommon for large companies to have issues with grouping, targeting and filtering when migrating from AD and SCCM into a Cloud Native setup. The number 1 complaint I usually hear, is lack of filtering options when assigning apps, which is particularly pertinent for companies with very large and old SCCM Installations with the strangest collection queries you can imagine. This really sets the premise for an entire blog post about grouping, targetting and filtering. However for now, I will just highlight the simply functionality that's been around for a while in Intune for Win32 apps: Requirements. When you assign apps you can choose requiremente like OS, Disk Size, CPU etc. But you can also choose custom requirements. From this view you can select File and Registry as well, but the one I want to highlight is the Script setting.

With some basic PowerShell you can create any custom filtering you want for assigning your apps. Want to see some examples? Well glad you asked!

## How does it work?

When you add Custom requirement scripts to Intune, you can decide what apps should install/uninstall based on your requirement script. If the requirement is not met, the device will be put under "Not Applicable" just like we know from [Device Filters](https://learn.microsoft.com/en-us/mem/intune/fundamentals/filters).

![Win32Requirement](/assets/images/2024-08-03-Win32app-Requirements/Requirement-NotApplicable.png?raw=true "Win32 App Requirement Example 1")

![Win32Requirement](/assets/images/2024-08-03-Win32app-Requirements/Requirement-NotApplicable-1.png?raw=true "Win32 App Requirement Example 2")