---
title: "Update Dell devices with Dell Command Update using Intune"
date: 2024-04-10
categories:
  - Dell
tags:
  - Update drivers
  - Update BIOS
  - Update Firmware
  - Endpoint Security
  - Import ADMX Templates
---

**UPDATED: 12th of April 2025 w. DCU 5.5**

This topic is not sexy at all to talk about in IT, but it is nonetheless getting more important for security reasons, due to the many security updates now included in BIOS, Driver and firmware updates in newer times.

In todays day and age it's super important to ensure your drivers and BIOS is up-to-date. Not only is there always a bunch of functionality fixes but security issues is also patched. Depending on your flavor of hardware vendor, Dell, HP or Lenovo, each of them have their own tools to manage and push driver and BIOS updates. However, it's also worth mentioning, since the release of the [driver update management module in Intune](https://learn.microsoft.com/en-us/mem/intune/protect/windows-driver-updates-overview) the reasons to use the hardware vendors own tools grows less and less, as the Intune Driver Update management module gets better.

The one reason you should still consider using the hardware vendors own tools. is speed of delivery. Driver and BIOS updates are released instantly, and depending on the hardware vendor, there can be several months delay before they are released via Windows Update. That's not neccessarily the fault of Microsoft, it's mostly the hardware vendors themselves that decides when and if to release updates via Windows Update.

In this blog post, we will go through what we can do with Dell devices using Dell's own Dell Command Update.

## Getting started - Packaging Dell Command Update as a Win32 app

In case you don't have the latest version of Dell DCU in Intune,  We need to download the latest version of Dell Command update, get it packaged and uploaded to Intune as a win32 app. Usually you can find the latest version on [this site](https://www.dell.com/support/kbdoc/en-us/000177325/dell-command-update) - Package it using the [Microsoft Win32 Content Prep Tool](https://github.com/microsoft/Microsoft-Win32-Content-Prep-Tool)

You can use below install, uninstall command and detection rules for Dell DCU 5.5 which is the latest version at the time of this blog:

* **Install command:** `Dell-Command-Update-Windows-Universal-Application_P4DJW_WIN64_5.5.0_A00.EXE /s /l=C:\Windows\Logs\Dell_Command_Update_5.5_exe_installer.log`
* **Uninstall command:** `msiexec /X {3F2A9AE0-4FB2-41C7-A9DF-611E6FAC2B31} /qn`
* **Required disk space:** 500MB
* **Detection, MSI String:** `{3F2A9AE0-4FB2-41C7-A9DF-611E6FAC2B31}`

Set return code 2 as "Success" as well, to ensure it doesn't fail during ESP when deploying devices with autopilot.

![DellDCUAPP](/assets/images/2024-04-08-DellBIOSUpdates-Intune/DellCommandUpdate-App-1.png?raw=true "Dell Command Update app")

## Importing ADMX Templates

To get the ADMX templates required from managing DCU, you can extract the Dell Command | Update installer you previously downloaded.

Once you have it downloaded, open it and click extract. Extract to a folder of your choice. 

![DellDCUAPP](/assets/images/2024-04-08-DellBIOSUpdates-Intune/DellCommandUpdate-2.png?raw=true "Dell Command Update ADMX Templates")

Navigate to the extracted folder and find the "Templates" folder. That would be your admx templates we need to import into intune now. Head on over to Intune -> Devices -> Configuration. Then click "Import ADMX" in the top. 

![DellDCUAPP](/assets/images/2024-04-08-DellBIOSUpdates-Intune/ImportADMX-1.png?raw=true "Dell Command Update ADMX Templates")
![DellDCUAPP](/assets/images/2024-04-08-DellBIOSUpdates-Intune/ImportADMX-2.png?raw=true "Dell Command Update ADMX Templates")
![DellDCUAPP](/assets/images/2024-04-08-DellBIOSUpdates-Intune/ImportADMX-3.png?raw=true "Dell Command Update ADMX Templates")

Start by importing the Dell.ADMX file. For the .ADML file, you can find it under the subfolder en-US. Then hit next and create. Wait 1-2 minutes, then hit "Refresh". 

![DellDCUAPP](/assets/images/2024-04-08-DellBIOSUpdates-Intune/ImportADMX-6.png?raw=true "Dell Command Update ADMX Templates")

The Dell template should be "Available" (Sometimes it can be a bit sluggish, so be patient with this one). Once the Dell template is available, import the Dell Command Update.ADMX and ADML file similarly. Once finished, you should see "Dell.ADMX" and "Dell Command Update.ADMX":

![DellDCUAPP](/assets/images/2024-04-08-DellBIOSUpdates-Intune/ImportADMX-7.png?raw=true "Dell Command Update ADMX Templates")

## Deploying update policies

Let's go and create a new configuration profile. Select "Windows 10 and later" -> Templates -> Imported administrative templates (preview). Let's give the policy a nice name like "Dell Command Update Settings". Then hit next. Then you should be able to see the Dell folder with all the Dell Command Update settings.
![DellDCUAPP](/assets/images/2024-04-08-DellBIOSUpdates-Intune/DeployPolicy-1.png?raw=true "Dell Command Update ADMX Templates")
![DellDCUAPP](/assets/images/2024-04-08-DellBIOSUpdates-Intune/DeployPolicy-2.png?raw=true "Dell Command Update ADMX Templates")

All the settings we are looking for, is placed under the folder "Update Settings". Lets configure our Dell Command Update Policy to adjust the following:
1. **"What do when updates are found":** Set this to "Enabled" and to "Download and install updates (Notify after complete)
2. **"Update Settings":** Set the "Update Interval to "Weekly". "Time of day" to 1PM, and then "Day of the week" to "Monday" (If you set it to "Automatic" it will trigger every 3 days)
3. **"System Restart deferral":** "Enabled" and set it to 8 hours. Then assign 3 deferrals (This gives the user 8 hours to perform a reboot after updates are installed. They can defer up to 3 times)
4. **"Installation deferral":** "Enabled" and set it to 24 hours. Then assign 3 deferrals. (This gives the user 24 hours to start installing updates, and can postpone up to 3 times - Consider not setting this option, if you want to reduce the notifications/actions required for the end-user. If the end-user fails to install the updates within 24hours it will auto-install)
5. **"Maximum retry attempts":** Set this to "3"
6. **"Delay":**: Set this to "0" days. This is only for testing purpose for now, but this will delay any updates for X amount of days. This is similar to the policy we have in windows update known as "Quality update deferral period (days)"
![DellDCUAPP](/assets/images/2024-04-08-DellBIOSUpdates-Intune/DeployPolicy-4.png?raw=true "Dell Command Update ADMX Templates")


Change these settings accordingly based on your testing and your orgs needs. The settings "System restart deferral", "Installation Deferral" and "Delay" is the ones you can adjust based on your needs and deployment rings, that will have an impact to the end-user experience.

You can also check if the settings deployed by opening Dell Command Update on the device, hit the settings button in the top right corner. Then you should see a red text saying "Some settings are managed by your organization" in the top of the settings windows.
![DellDCUAPP](/assets/images/2024-04-08-DellBIOSUpdates-Intune/DellDCU-SomeSettingsManagedByYourOrg.png?raw=true "Dell Command Update ADMX Templates")

## End User Experience

Based on the settings, the user will get various notifications, based on the settings you push to the users device. It can show notifications regarding installing updates, but you can also just choose to not show them notifications regarding installing updates, and then only show them reboot notifications, if updates has been installed that requires a reboot. The notifications will be shown as toast notifications, and will look like below:

![DellDCUAPP](/assets/images/2024-04-08-DellBIOSUpdates-Intune/DCU-Reboot.png?raw=true "Dell Command Update ADMX Templates")
![DellDCUAPP](/assets/images/2024-04-08-DellBIOSUpdates-Intune/DCU-Reboot2.png?raw=true "Dell Command Update ADMX Templates")

The final restart reminder, will linger in the system tray until the user clicks "restart now" or clicks the notification away. 

> **_If the user closes the last reboot reminder, there will be no "final" reminder.. it will abruptly reboot the device, without warning, after 30 minutes, if you are using the "Reboot after updates are installed" setting - Use this setting carefully_** 

Finally, all updates deployed via Dell Command Update is logged to C:\ProgramData\Dell\UpdateService\Log - Look for the "activity.log" log to see what updates has been downloaded along with the install status, success or failed, where the "service.log" is more for the app itself, to see connectivity to update servers and whether a reboot is pending or not.

## Sample deployment rings - 1 policy pr. ring

| Ring     | Sys. Restart Hours | Sys. restart def.| Install Hours   | Install Def. | Delay   |                Assignment group                  |
|----------|--------------------|----------------- |-----------------|--------------|---------|--------------------------------------------------|
| Ring 0   | 8  hours           | 0 Def.           | 0 hours         | 0 Def.       | 0 days  | Modern Workplace Devices-Windows Autopatch-Test  |
| Ring 1   | 36 hours           | 1 Def.           | 8 hours         | 1 Def.       | 3 days  | Modern Workplace Devices-Windows Autopatch-First |
| Ring 2   | 48 hours           | 2 Def.           | 12 hours        | 2 Def.       | 7 days  | Modern Workplace Devices-Windows Autopatch-Fast  |
| Ring 3   | 72 hours           | 3 Def.           | 48 hours        | 3 Def.       | 15 days | Modern Workplace Devices-Windows Autopatch-Broad |

**Apply a Dell Device filter if you use the autopatch groups for assignment, as shown in the above example**
![DellDCUAPP](/assets/images/2024-04-08-DellBIOSUpdates-Intune/DellDeviceFilter.png?raw=true "Dell Command Update ADMX Templates")

> **_PROTIP:_** **If you don't have any deployment rings, consider reusing your autopatch groups as shown in the above sample, so you can deploy updates in a staggered approach, to avoid deploying big changes to all your devices at the same time. Autopatch automatically divides your devices in rings, so no need to do it manually. Default is 1% For Ring 1 (First), 9% for Ring 2 (Fast) and 90% for Ring 3 (Broad). Ring 0 (Test) can be reserved for members of your team, and needs to be manually assigned in autopatch. The default group names for autopatch starts with "Modern Workplace Devices-Windows Autopatch-"**
>
> ![DellDCUAPP](/assets/images/2024-04-08-DellBIOSUpdates-Intune/AutoPatch-1.png?raw=true "Dell Command Update ADMX Templates")

## Bonus: Remediation and PowerShell script for on-demand updates

It's possible to run a one-time update of all dell drivers/firmware, where we trigger dcu-cli.exe from Command Update, using a remediation or a PowerShell Script. The PowerShell script can be assigned to a group of devices, whilst the remediation the be run on-demand for troubleshooting purposes. Try this out on your Dell devices:

```
$currentdate = Get-Date -format 'ddMMyyyy_HHmmss'
$dcucli = "${env:ProgramFiles}\Dell\CommandUpdate\dcu-cli.exe"
$logsfolder = "$env:Programdata\Dell\Logs"

#Download and install Dell Command Update 5.4 if it doesn't exist
if (!(test-path $dcucli)) {
$uri = 'https://dl.dell.com/FOLDER11914128M/1/Dell-Command-Update-Windows-Universal-Application_9M35M_WIN_5.4.0_A00.EXE'
Write-Host "DCU Cli doesn't seem to be present.. Attempting to download and install now.."
Invoke-WebRequest -uri $uri -outfile 'C:\Windows\temp\dcu54.exe' 
Start-Process "C:\Windows\Temp\dcu54.exe" -ArgumentList '/s' -Wait
Start-Sleep -Seconds 10
}

#Create new folder for logs in ProgramData - Change this based on your environment
if (!(Test-path $logsfolder)) {New-item $logsfolder -ItemType Directory}

#Apply all updates if any is found - including BIOS
Start-Process $dcucli -Wait -ArgumentList "/ApplyUpdates -outputlog=$logsfolder\dcucli_applyupdates_$currentdate.log"
```

The script needs to run in 64-bit context and in SYSTEM context.

Find the docs for dcu-cli to experiment with different switches [here](https://www.dell.com/support/manuals/en-us/command-update/dellcommandupdate_rg/dell-command-update-cli-commands?guid=guid-92619086-5f7c-4a05-bce2-0d560c15e8ed&lang=en-us)

## Final words

I hope you found this walkthrough useful. There is some pros/cons to using the Dell Command Update for managing updates compared to just using the Windows Update functionality to pushing drivers/BIOS. For instance, the notifications and snooze options you get with windows updates is much better for the end-user, but on the other hand if you use Dell Command Update as it is right now, you will get updates and fixes much faster.

If you want some more inspiration for custom Dell tools in Intune, such as Prebaked Remediation scripts and custom compliance rules to check for things like Warranty, Missing Critical drivers, Battery health and other cool stuff, I would recommend you check out Sven Riebe's github repository. He's a very skilled fella working at Dell, and he likes sharing his work. You can find his repo [here](https://github.com/svenriebedell)

That's all folks :)
