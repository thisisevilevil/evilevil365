---
title: "Getting started with EPM - Endpoint Privilege Management"
date: 2024-04-01TREMOVETHIS
categories:
  - Microsoft Intune Suite
  - Getting Started
tags:
  - EPM
  - Endpoint Privilege Manager
  - PAM Solution
  - Local Administrator
  - Endpoint Security
---

# What is EPM?
EPM, short for {Endpoint Privilege Management](https://learn.microsoft.com/en-us/mem/intune/protect/epm-overview), is Microsofts own tool to control, audit and manage elevated rights on windows endpoints (mac soon to come as well!). This is also under a category of what we refer to as a [PAM Solution for Endpoints](https://www.microsoft.com/en/security/business/security-101/what-is-privileged-access-management-pam#:~:text=Privileged%20access%20management%20(PAM)%20is,privileged%20access%20to%20critical%20resources). Having a PAM Solution for endpoints is absolutely vital to control and audit the usage of elevating processes.

Some organizations have chosen to remove local admin rights altogether, but there can be certain times where users needs these admin rights to do their job, i.e: Changing system settings for development purposes, installing apps not present in the existing app catalogs or just general supportability, the list is can be endless. 

The point is: If we don't have a good and secure way to facilitate this on behalf of the end-user, guess what? They are going to log a ticket to ServiceDesk! That's where the good PAM Solution comes in. Not only will it eliminate the need to log a ticket to ServiceDesk, it will also still be able to facilitate that elevation without compromising security!

> **_NOTE:_** **LAPS is not a PAM Solution - It is highly undesirable from a security perspective to use LAPS as a general means to elevate processes on end-users devices, unless it's for emergency/break-glass purpose**


**At it's core, EPM will give you the following functionalities:**
- Allow standard users to elevate processes in a guarded enviroment
- Eliminating one-time installs of software
- Reporting/Auditing across your estate of Administrator usage!
- Supports only Windows for now, Mac will come at a later stage

# Getting Started with EPM
Getting started with EPM is very simple, and only take a few clicks. But before we do anything ensure you have the correct licensing by heading to the Intune Portal -> Tenant Administration -> Intune Add-ons. Ensure either Microsoft Intune Suite or Endpoint Privilege Management is active! When it's correctly activated, you will get an extra pane under Endpoint Security -> Endpoint Privilege Manager
![EPM](/_posts/Images/2024-04-01-GettingStarted-With-EPM/EPM-Button_Small.png?raw=true "EPM Button in Intune")

Head over to the Endpoint Privilege Management section and press the "Create Policy" button and select the "Elevation Settings Policy". This is where we can craft a policy to enable EPM! Give it a friendly name, i.e: Default EPM Elevation Policy
![EPM](/_posts/Images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules.png?raw=true "EPM Button in Intune")
![EPM](/_posts/Images/2024-04-01-GettingStarted-With-EPM/EPM-DefaultEPMElevationRules.png?raw=true "EPM Button in Intune")

At the next section, this is basically the on/off lever for EPM. We leave all of the settings on the default settings. Most of them is self-explanatory, but the most important one here is the "Default elevation response". This is super important to take note off, as this will have an important effect. If you choose "Not Configured", this means it will always default to "Deny all requests" for end-users UNLESS there is an elevation rule crafted for a given process. The other option called "Require user confirmation" is the most relaxed, as this allows the end-user to simply confirm the risks of an elevation and proceed to elevating a process without needing a specific elevation rule to be crafted in the first place and doesn't require any kind of approval from IT. 

The last option called "require support approval", allows the end-user to request elevations of files, regardless of whether there is a rule for a process crafted. Then someone from IT with the necessary access, can choose to approve/deny the elevation request.
For now, let's just choose "Not Configured" and leave everything as the default. Then assign a scope tag if required and finally assign to a test device of your choice using a group or a device filter.

![EPM](/_posts/Images/2024-04-01-GettingStarted-With-EPM/EPM-ConfigurationSettings.png?raw=true "EPM Policy")

> **_NOTE:_** **Microsoft recommends choosing "Not Configured" as the default elevation requests, for the majority of your users, to ensure full control of how administrator rights is used in your organizations endpoints!**

## The nuts and bolts
As soon as you assign the EPM policy to your test device, shortly after, the device should automatically get the EPM Client installed. It works the same way with the Intune Management Extension: IF you have any PowerShell Scripts, Remediations, Win32 or Windows Store apps assigned to the device, the Intune Management extension will also automatically install. Not need to manually maintain these binaries, Microsoft will do this for you!
Once the client installs, the most obvious things you will notice is of course when users right click, they will notice "Run with elevated access". The EPM Client will also install under the folder C:\Program Files\Microsoft EPM Agent whilst it will also install a service which will leave a process in task manager as well.

![EPM](/_posts/Images/2024-04-01-GettingStarted-With-EPM/EPM-Folder.png?raw=true "EPM Folder")
![EPM](/_posts/Images/2024-04-01-GettingStarted-With-EPM/EPM-Service.png?raw=true "EPM Service")
![EPM](/_posts/Images/2024-04-01-GettingStarted-With-EPM/EPM-RunWithElevatedAccess.png?raw=true "EPM RunWithElevatedAccess")




> **_NOTE:_** **Microsoft has created a new channel for delivering EPM Policies, that is super fast! EPM is the only tool to use this new channel for now as of this blogs date, but not long from now, all other sections of Intune will also migrate to this new channel to deliver policies. Depending on who you ask, they will refer to this as being "Dual Enrolled" since we now have 2 seperate channels for policies - Super nice!**




