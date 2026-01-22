---
title: "Use Intune and Company portal to install Windows OS Patches"
date: 2026-01-22
categories:
  - Intune
tags:
  - Windows Updates
  - January 2026-01 Out of Band patch
  - Company Portal
---

This suddenly came up out of the blue: Install or offer windows updates manually via Intune and company portal. I've previously done this for my customers to patch stubborn device, in case windows updates are broken or similar, then it can be good to have a windows update available for troubleshooting purposes.

However, in this case, this blog post was actually prompted by the January 2026-01 Out-of-Band (OOB) patch released for Windows 11 by Microsoft. It seems they have decided to not release this via normal Windows Update channels, so it will not be offered via WufB. But that OOB patch has some fixes that could annoy your users or even you as an IT Admin.

For Windows 11 23H2, the January 2026-01 OOB patch ([KB5077797](https://support.microsoft.com/en-us/topic/january-17-2026-kb5077797-os-build-22631-6494-out-of-band-3fb07d6a-0e35-4510-8518-4e333ed78edc)) contains the follwing fixes:

1. **[Remote Desktop] Fixed:** Some users experienced sign-in failures during Remote Desktop connections. This issue affected authentication steps for different Remote Desktop applications on Windows such as the Windows App.
2. **[Power & Battery] Fixed:** Some devices with Secure Launch enabled restart instead of shutting down or entering hibernation.

For Windows 11 24H2 and 25H2 that seems to only be affected by the remote desktop issue, and not the shutdown issue, so if your users suddenly face some generic errors authenticating to AVD or their Windows 365 devices, chances are that it's caused by the January 2026-01 update.

How do we offer this patch via Intune? Well not via WufB. We need to package it manually :)

## Packaging and offering the update in Company Portal

It's possible to wrap updates for Windows 11 23H2, 24H2 and 25H2 in 1 package, but I don't recommend it due to their size. So we will create 1 Win32 app for each supported Windows 11 version, where pertinent. We will use a requirement rule to makes ure the update is only offered to relevant devices. Using the win32 app option also makes sure to utilize [Delivery Optimization](https://learn.microsoft.com/en-us/windows/deployment/do/waas-delivery-optimization) if correctly configured and available

In the below example we are going to download the update for Windows 11 23H2, create a PowerShell script to install it and then package it and make it available.

1. Head over to the [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/) and do a search for the KB # of the patch you want
2. Press the download button and download all relevant files for the update
![WindowsCatalog](/assets/images/2026-01-22-Intune-CompanyPortal-OSPatches/WufB-Catalog-Download-1.png?raw=true "Microsoft Update Catalog Download Windows update")
3. Create a PowerShell script to install the update, and add the following line: `Add-WindowsPackage -Online -PackagePath "$PsScriptRoot\windows11.0-kb5077797-x64_af2b9a9ab390d2081e3e4eb52a2f8b81b5be1d7f.msu"` 

Save the PowerShell script in the same folder as the update files

3. Wrap the files using the [Intune Content Prep Tool](https://learn.microsoft.com/en-us/intune/intune-service/apps/apps-win32-prepare#convert-the-win32-app-content) as an .intunewin file
4. Upload the intunewin file to Intune as a Win32 app and give it a nice name. In this example we will offer the Windows 11 23H2 Update to Windows 11 23H2 devices
**Install command:** PowerShell.exe -ExecutionPolicy Bypass -NoProfile -File Install-WindowsUpdate-23H2_2026-01-OOB.ps1
**Uninstall command:** dism.exe /online /PackageName:Package_for_RollupFix~31bf3856ad364e35~arm64~~26100.7623.1.20.msu
>Note: To find the relevant uninstall command for the specific update you can use the following PowerShell command to list installed update and find the relevant package name: `Get-WindowsPackage -Online`

**Requirement rule:** Add a registry key as a requirement. Key path: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion - Value name: Current Build - registry key requirement: String Comparison - Operator: Equals - Value: 22631
>NOTE: For Windows 11 24H2 the value should be 26100 and 25h2 the value should be 26200

**Detection rule:** Add a registry key as detection. Key path: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion  - Value name: UBR - Detection method: Integer Comparison - Operator: Greather than or equal to - Value: 6494
>NOTE: For Windows 11 24H2 and 25H2 the value should be 7627

5. Make sure to adjust and observe the reboot behaviour accordingly. If you want to nudge the users to perform a reboot, make sure to set the following setting on the package: "Intune will force a mandatory reboot". When you set this flag, this enables the "Restart Grace Period" option, on your app assignments. This allows you to notify the user to reboot the device, and they will receive several notifications reminding them to reboot.

>NOTE: Remember to enable the restart grace period option, when using the option "Intune will force a mandatory reboot". If this is not enabled, and users install the update, this will result in an immediate and abrupt reboot for the end-user!
![RestartApp](/assets/images/2026-01-22-Intune-CompanyPortal-OSPatches/AppInstallBehaviour.png?raw=true "Intune restart on win32 app")
![RestartApp](/assets/images/2026-01-22-Intune-CompanyPortal-OSPatches/AppRestart-graceperiod-1.png?raw=true "Intune grace period on win32 app")
![RestartApp](/assets/images/2026-01-22-Intune-CompanyPortal-OSPatches/AppRestart-graceperiod-2.png?raw=true "Intune grace period on win32 app")


## Wrapping up

It's not ideal having to offer updates this way, but now I've received so many questions and concerns from customers regarding this January update, so I felt compelled to write this blog post to illustrate an ideal way to manually package and offer this update to end-users. By using the Win32 app option and making it available in company portal, this allows us to use the update as fix-on-failure, guiding users to company portal to install it, only when it's needed. There's also the option of just pushing it out to all your users, if you have a lot of users affected and a large amount of incidents, but if you do that, I would recommend to raise the restart grace period so the user has more than 24 hours to perform the mandatory reboot.

That's all for now. Have a great day ahead :)


