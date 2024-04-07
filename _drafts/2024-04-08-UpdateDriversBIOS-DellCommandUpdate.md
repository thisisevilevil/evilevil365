---
title: "Update Drivers and BIOS with Dell Command | Update managed using Intune"
date: DRAFT2024-04-08
categories:
  - Dell
tags:
  - Update drivers
  - Updated BIOS
  - Updated Firmware
  - Endpoint Security
---

In todays day and age it's super important to ensure your drivers and BIOS is up-to-date. Not only is there always a bunch of functionality fixes but security issues is also patched. Depending on your flavor of hardware vendor, Dell, HP or Lenovo, each of them have their own tools to manage and push driver and BIOS updates. However, it's also worth mentioning, since the release of the [driver update management module in Intune}(https://learn.microsoft.com/en-us/mem/intune/protect/windows-driver-updates-overview) the reasons to use the hardware vendors own tools grows less and less, as the Intune Driver Update management module gets better.

The one reason you should still consider using the hardware vendors own tool is speed of delivery. Driver and BIOS updates are released instantly, and depending on the hardware vendor, there can be several months delay before they are released via Windows Update. That's not neccessarily the fault of Microsoft, it's actually the hardware vendors themselves that decides when and if to release updates via Windows Update.

## Getting started - Packaging Dell Command | Update
In case you don't have Dell Command | Update in Intune, you can steal my .intunewin file <a id="raw-url" href="https://raw.githubusercontent.com/thisisevilevil/evilevil365/master/assets/Dell-Command-Update-Windows-Universal-Application_0XNVX_WIN_5.2.0_A00.intunewin">here</a> for version 5.2. Add the app into intune as a Win32 app:
* Install command: `Dell-Command-Update-Windows-Universal-Application_0XNVX_WIN_5.2.0_A00.EXE /s /l=C:\Windows\Logs\Dell_Command_Update_5.2_exe_installer.log`
* Uninstall command: `msiexec /X {E40C2C69-CA25-454A-AB4D-C675988EC101} /qn`
* Required disk space: 500MB
* Detection, MSI String: `{E40C2C69-CA25-454A-AB4D-C675988EC101}`


### Importing ADMX Templates
To get the ADMX templates you will need to download Dell Command Update. You can always find the latest version here: https://www.dell.com/support/kbdoc/en-us/000177325/dell-command-update

Once you have it downloaded, open it and click extract. Extract to a folder of your choice. Navigate to the extracted folder and find the "Templates" folder. That would be your admx templates we need to import into intune now. Head on over to Intune -> Devices -> Configuration. Then click "Import ADMX" in the top. 

Start by importing the Dell.ADMX file. For the .ADML file, you can find it under the subfolder en-US. Then hit next and create. Wait 1-2 minutes, then hit "Refresh". The Dell template should be "Available" (Sometimes it can be a bit sluggish, so be patient with this one). Once the Dell template is available, import the Dell Command Update.ADMX and ADML file similarly. Once finished, it should look something like this:


### Deploying update policies
Let's go and create a new configuration profile. Select "Windows 10 and later" -> Templates -> Imported administratives templates (preview). Let's give the policy a nice name like "Dell Command Update Settings". Then hit next. Then you should be able to see the Dell folder with all the Dell Command | Update settings.

All the settings we are looking for, is placed under the folder "Update Settings". To configure your Dell Command | Update Policy adjust the following:
1. "What do when updates are found": Set this to "Enabled" and to "Download and install updates (Notify after complete)
2. "Update Settings": Set the "Update Interval to "Weekly". "Time of day" to 1PM, and then "Day of the week" to "Monday" (If you set it to "Automatic" it will trigger every 3 days)
3. "System Restart deferral": "Enabled" and set it to 24 hours. Then assign 3 deferrals (This gives the user 24 hours to perform a reboot after updates are installed. They can defer up to 3 times)
4. "Installation deferral": "Enabled" and set it to 48 hours. Then assign 3 deferrals. (This gives the user 48 hours to start installing updates, and can postpone up to 3 times - Consider not setting this option, if you want to reduce the notifications the end-user sees)
5. "Maximum retry attempts": Set this to "3"


### On-Demand update remediation
It's possible to a one-time update of all dell drivers/firmware using a remediation or a PowerShell Script. The PowerShell script can be assigned to a group of devices, whilst the remediation the be run on-demand for troubleshooting purposes.