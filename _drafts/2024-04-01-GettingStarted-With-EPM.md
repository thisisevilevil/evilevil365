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
- Support for Windows - Mac will come at a later stage.

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

You will notice if you elevate anything at this stage with the current policy we assigned, the user would simply be denied. Remember we set the "Default elevation response" to "Not Configured" meaning the user can only elevate apps that we create specific rules for. We will come around to crafting elevation rules for specific apps in a moment.

![EPM](/_posts/Images/2024-04-01-GettingStarted-With-EPM/EPM-NotAllowed.png?raw=true "EPM Not Allowed")


> **_NOTE:_** **Microsoft has created a new channel for delivering EPM Policies, that is super fast! EPM is the only tool to use this new channel for now as of this blogs date, but not long from now, all other sections of Intune will also migrate to this new channel to deliver policies. Depending on who you ask, they will refer to this as being "Dual Enrolled" since we now have 2 seperate channels for policies - Super nice!**

## Crafting an elevation rules policy

### Craft elevation rule to allow 7zip 24.03 beta .exe using file hash
Now we will craft our first elevation rule policy to allow 7zip. The elevation rule will use the filehash to allow the elevation. When using filehash, you achieve the storngest detection for a specific process as this is unique for every single file. That way, we ensure only the specific process we specify, will be allowed to run with elevated rights.

I will take you through every step of creating the policy.

Head on over to Intune -> Endpoint Security -> Endpoint Privilege Management. Press the "Create Policy" button, choose Windows 10 or later and choose "Elevation Rules Policy". GIve it a nice name like "Default Elevation Rules" and then press "edit instance"
![EPM](/_posts/Images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-1.png?raw=true "EPM Elevation Rules")
![EPM](/_posts/Images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-2.png?raw=true "EPM Elevation Rules 2")

**In elevation type, we have the following options:**
1. User Confirmed: User will have to confirm when elevating an app
2. Automatic: App will elevate without user confirmation. Best practice is to only use this for legacy apps that doesn't work with User Confirmed or support approved
3. Support approved: IT Support will approve elevation requests before users can elevate a process

For now, let us choose User Confirmed.

**In validation we have 2 optional boxes we can tick:**
1. Business Justification: User will have to present a business justification before being allowed to elevate a process
2. Windows Authentication: User will have to authentication using their login to Windows. This can be a password, but also works for Windows Hello and FIDO Keys

For now, let us only choose Windows Authentication:
![EPM](/_posts/Images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-3.png?raw=true "EPM Elevation Rules 3")

Lastly to finish specifying our elevation conditions, we need to select child process behaviour. Be careful with this option, as lot of installers spawn subprocesses to install dependencies. Let's us expand a bit on the options:
1. Allow all child procesesses to run elevated: All processes spawned by the elevated process, will be allowed to elevate. This is also the default in Windows when you right click a process and select "Run as administrator. You will find that a lot of app installers rely on this functionality, so if you don't choose this, be sure to test it thoroughly!
2. Require rule to elevate: The subprocess will need a specific rule to elevate, before it can be run. While this is the most secure by far, be careful choosing this one, unless for processes like cmd.exe or PowerShell.exe - Otherwise you will find yourself having to craft a lot more elevation rules that you might initially have signed up for.
3. Deny All: Most secure, but see notes in option #2.
4. Not Configured: Will revert to the default response which is "Require rule to elevate"

Let's select "Allow all child processes to run elevated" for now:
![EPM](/_posts/Images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-4.png?raw=true "EPM Elevation Rules 4")

Finally we need to provide some file information. Since we are using file hash to craft this elevation rule, we will set "Signature Source" to "Not Configured". Notice when we set it to not configured, then we only need to give it a filename and a filehash. Getting the filename is easy, but to get the filehash, we have a few different methods at our disposal. Let's open a PowerShell and use the Get-FileHash  cmdlet
![EPM](/_posts/Images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-5.png?raw=true "EPM Elevation Rules 5")
![EPM](/_posts/Images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-6.png?raw=true "EPM Elevation Rules 6")
![EPM](/_posts/Images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-7.png?raw=true "EPM Elevation Rules 7")

Once you have extracted the filehash, you should have a policy that looks like the following:
![EPM](/_posts/Images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-8.png?raw=true "EPM Elevation Rules 8")

Once you are satifised with everything, lets take our new elevation rule for a spin. Assign your scope tags where required, then assign your elevation rule to your device.

> **_NOTE:_** **If you want more control of where the process is launched from, you can also configure the "File path" option. IF users try to elevate a file outside this path, detection will fail and users will not be able to elevate**

