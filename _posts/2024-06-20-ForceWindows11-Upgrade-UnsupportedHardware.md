---
title: "Upgrade unsupported hardware to Windows 11 using Intune"
date: 2024-06-20
categories:
  - Windows 11
tags:
  - Intune
  - Old Hardware
  - Windows 11
  - YOLO Windows 11
  - Force Windows 11 Upgrade
---

Are you looking to upgrade your ancient and seemingly unsupported hardware to Windows 11 using Microsoft Intune? Well look no further, you have come to the right place.

Windows 10 support is quickly coming to an end. [October 14, 2025](https://learn.microsoft.com/en-us/lifecycle/products/windows-10-home-and-pro) is the date your organization either have to have all your devices upgraded to Windows 11 or replaced with hardware that does support Windows 11. Well there is also the 3rd option that will make your wallet suffer, [you can choose to pay 61$ pr. device](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/when-to-use-windows-10-extended-security-updates/ba-p/4102628) to get extended patching (ESU Licenses) for Windows 10 for up to 3 years.

What if I told you there is an alternative path for old and unsupported hardware, for those with empty wallets? And just to be clear, before I continue this blog post, I highly discourage this path, it's the aboslute worst option of them all. Or well.. the worst option of them all is to do nothing, because then you won't get any patches come October next year.

There are companies out there that really needs this information, to take an informed decision about how to continue, as they might not be in a financial position to replace their aging hardware, or purchase ESU licenses. And as you all know, to take an informed decision, you need all the information required to make that informed decision regarding how to proceed.

This blog post will outline how you can use the consumer tool known as [Windows 11 Update Assistant](https://www.microsoft.com/software-download/windows11) along with some registry keys to override the Windows 11 hardware requirements set by Microsoft, and force an upgrade to windows 11 where possible. 
I'm also aware you can just download the ISO of Windows 11, package it as a Win32 app then run setup.exe silently in system context, but you have to package the full ISO contents in the Win32 app, which can be super clunky due to the large package size but still viable in some scenarios. The good thing about the Windows 11 Update Assistant is that it's very lightweight, the file is only around 4mb in size.

Let's get into it.. and might I say.. proceed with caution :)

![Caution](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/Caution-WatchYourstep.png?raw=true "Caution")

**Disclaimer: This solution will be presented as-is, without any warranties. Be aware that Microsoft might choose to make changes on how to bypass the Windows 11 requirements, and the workarounds I present here might also stop working. No support will be provided by the hardware vendor or by Microsoft for this scenario. The best option is to replace unsupported hardware with supported hardware, where possible. Also find the official risks as outlined by Microsoft [here](https://support.microsoft.com/en-us/windows/installing-windows-11-on-devices-that-don-t-meet-minimum-system-requirements-0b2dc4a2-5933-4ad4-9c09-ef0a331518f1).**

## Windows 11 requirements

Let's examine the [Windows 11 requirements](https://www.microsoft.com/en-us/windows/windows-11-specifications).

* 4GB of RAM
* 64GB of storage or higher
* UEFI Capable
* TPM 2.0
* 8th Generation Intel CPU or Higher (There is a few exceptions to this rule in the 7th Generation Intel's but needs to be an extreme version)

If your device is before the year of ~2017 there is very high chance your device doesn't support Windows 11, due to the CPU being unsupported. Most devices after ~2017 already have TPM 2.0 and an 8th Gen Intel CPU or higher. If the TPM doesn't appear on your device, you usually just have to enable it in the BIOS, not a biggie. This is especially relevant on any devices purchased for Gaming. Up until recently, TPM commonly came disabled by default from the Motherboard manufacturers. All you need to do is to go to the BIOS and enable it, to get Windows 11 support. On Intel boards it might be called TPM, Trusted Platform Computing or Intel PTT. On AMD Boards it will be called TPM, Trusted Platform Computing or AMD PSP.

There is otherwise a super easy way to get a list of your unsupported Windows 11 devices, through Intune. Go to intune, Reports -> Endpoint Analytics -> Work from Anywhere -> Windows -> "Add Filter" -> Windows 11 Readiness -> Select "Not Capable". From there, you can clearly see how many devices is not supported and why they are not supported, and you can also easily export this to a .csv file.

![Win11Readiness](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/UnsupportedHardware.png?raw=true "Windows 11 readiness report")

## Prerequisites

We can set a registry value to override the hardware requirements. Be aware that setting this specific value, overrides the TPM 2.0 and CPU requirement, but for some reason TPM 1.2 is still required, you can read more about this option [here](https://support.microsoft.com/en-us/windows/ways-to-install-windows-11-e0edbbfb-cfc5-4011-868b-2ce77ac7c70e). 

If there is no TPM, then this option won't work for you. You will need to reinstall Windows 11, then it's possible to override the hardware requirements. This can be done using [Rufus](https://rufus.ie/en/) when creating a bootable USB but also using tools like SCCM and MDT using an unattend.xml file to apply the correct registry values to override the hardware requirements, you can find some inspiration [here](https://github.com/thisisevilevil/IntunePublic/blob/main/Just%20stuff/BypassWin11Requirements-Unattend.xml).

### Deploying the PreReqs

1. Deploy PowerShell Script from Intune that sets the correct registry value to override hardware requirements. You can fetch the one I made from [here](https://github.com/thisisevilevil/IntunePublic/blob/main/Scripts/Win11Unsupported/DeployWin11HWOverrideKeys.ps1). Deploy it like so:
![OverrideReqs](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/DeployOverrideScript.png?raw=true "Deploy PowerShell Script")

2. Deploy PC Health Check app as a Win32 or LoB app, as the Windows Update Assistant relies on this to be present. You can download it from [here](https://aka.ms/GetPCHealthCheckApp)

Note when deploying the PC Health Check app, the app might popup on the end-users device. You can simply instruct them to close it, if it happens to you as well.

Before going any further, also be sure to test these settings first on a few test devices.

### Optional Step

Since we are already cutting the red tape here, we might as well keep going. You can override any [safeguard holds](https://learn.microsoft.com/en-us/windows/deployment/update/safeguard-holds) by applying the a policy using Settings catalog. Proceed to Disable Safeguard Holds:
![SettingsCatalog](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/DisableSafeguardsWufB.png?raw=true "Disable WufB Safeguards Settings Catalog")

This will override any safeguard holds applied from Microsoft end because of Apps, Drivers or even certain BIOS Versions that Microsoft has deemed problematic for Windows 11 23H2. Sometimes, the Safeguard hold has been applied because of a printer or just a very old app like old versions of VMware Player. We don't really care about this in this scenario. Otherwise be aware, there is obviously also an increased risk with old hardware of driver incompatibility. Attempting to force upgrade, might just result in a blue screen loop. If possible, proceed to apply all the latest driver you can for Sound, Video and Network. If your hardware vendor doesn't have any newer drivers for the last 3-4 years, you can try your luck by going directly to the vendor of the part, website like Intel or Realtek. Chances are they have newer versions available than your hardware vendor published.

There is otherwise a nice report in Intune where you can also identify these devices. Navigate to Reports -> Windows Updates -> Reports -> Windows feature update device readiness report. You can also go to the Compatibility Risks report to see the specific Drivers and apps that you have in your org that might be blocking an upgrade.

![ReadinessReport](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/FeatureUpdateReadiness-CompatRisks.png?raw=true "Readiness report")
![ReadinessReport](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/FeatureUpdateReadiness-CompatRisks-1.png?raw=true "Readiness report")

### Option #1: Forcefully applying Windows 11 from Intune on unsupported hardware

I have created a script that downloads the [Windows 11 update assistant](https://www.microsoft.com/software-download/windows11) and runs it silently in the background. I have actually used this script for the past year and a half to nudge stubborn devices over to the latest version of Windows 11. If you want to examine the code you can find it [here](https://github.com/thisisevilevil/IntunePublic/blob/main/Scripts/Win11UnsupportedHW/UpgradeToWindows11.ps1). The code is very simple as you can see.

To deploy the app using Intune perform the following steps:

1. Download the .intunewin file containing the script from <a id="raw-url" href="https://raw.githubusercontent.com/thisisevilevil/evilevil365/master/assets/UpgradeToWindows11.intunewin">here</a>
2. Upload it to intune as a win32 app
3. Specify the following when uploading the package:

* **Application name:** `Upgrade to Windows 11`
* **Install command:** `%windir%\Sysnative\WindowsPowerShell\v1.0\Powershell.exe -ExecutionPolicy Bypass -File UpgradeToWindows11.ps1`
* **Uninstall command:** `DISM /Online /Initiate-OSUninstall /Quiet`
* **Device restart hehaviour:** `Intune will force a mandatory device restart`
* **Required disk space:** 10000MB
* **Detection, Custom Script:** Use custom detection script. Download from [here](https://github.com/thisisevilevil/IntunePublic/blob/main/Scripts/Win11UnsupportedHW/Detect-Win11Installed.ps1)
* **Installation time required (mins)** 120 minutes
* **Logo**: Download <a id="raw-url" href="https://raw.githubusercontent.com/thisisevilevil/evilevil365/master/assets/Windows11Logo.png">here</a>

As you can see, we have selected "Intune will force a mandatory device restart". This will actually not come into play, if everything goes according to plan. The user will get a popup from the Windows 11 Update Assistant where they have 30 minutes to perform a reboot, where they can restart now or restart later. There is no way to alter the reboot timer or message, so keep this in mind. You will see that later in the User Experience section.
However, if there are any pending reboots or any other reasons the Windows 11 update might not correctly apply, the Intune Management Extension will attempt to restart the device. When the device has restarted, Intune will attempt to apply the update again.

**For this reason, remember to enable the "Restart Grace period" in your app assignment, to show reboot notifications/snooze options to the end-user. Note that it's disabled by default for each group where you assign the app. If you forget to set it, the user will experience an abrupt reboot without any warning whatsoever.**.

![RestartPeriod1](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/RestartGracePeriod-1.png?raw=true "Restart Grace Period")
![RestartPeriod1](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/RestartGracePeriod-2.png?raw=true "Restart Grace Period")

If you don't want this to be the case here, just go back to the package and set the device restart behvaiour to "No specific action" instead.

### Option #2: Making the Windows 11 Update Assistant available in company portal for unsupported hardware

> NOTE: Be aware for fully supported devices, you can deploy optional feature updates, to the user, so users can go to windows update instead to apply the update. This is the recommended approach in supported scenarios, see more info about this [here](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/more-flexible-windows-feature-updates/ba-p/4139230#:~:text=How%20to%20deploy%20and%20monitor,and%20select%20Create%20new%20Profile.)

Making the Windows 11 Update Assistant available in the Company portal, gives the user the option of when to start the upgrade at a convenient time.

Some of you folks probably remember ServiceUI from MDT. We can utilize ServiceUI in our Windows 11 Update Assistant package, to let users run the app from company portal and interact with the Windows 11 Update Assistant, even though it's running in System Context. I have prepared the .intunewin file for your convenience, with the ServiceUI.exe and Windows11InstallationAssistant.exe file.

1. Download the .intunewin file containing the script from <a id="raw-url" href="https://raw.githubusercontent.com/thisisevilevil/evilevil365/master/assets/ServiceUI-Win11UpdateAssistant.intunewin">here</a>
2. Upload it to intune as a win32 app - Make sure to deploy it as "available"
3. Specify the following when uploading the package:

* **Application name:** `Upgrade to Windows 11`
* **Install command:** `ServiceUI.exe -process:explorer.exe Windows11InstallationAssistant.exe`
* **Uninstall command:** `DISM /Online /Initiate-OSUninstall /Quiet`
* **Device restart hehaviour:** `No specific action`
* **Required disk space:** 10000MB
* **Detection, Custom Script:** Use custom detection script. Download from [here](https://github.com/thisisevilevil/IntunePublic/blob/main/Scripts/Win11Unsupported/Detect-Win11Installed.ps1)
* **Installation time required (mins)** 120 minutes
* **Logo**: Download <a id="raw-url" href="https://raw.githubusercontent.com/thisisevilevil/evilevil365/master/assets/Windows11Logo.png">here</a>

## User Experience

### UX Option #1: Silent upgrade in the background

If the Windows 11 upgrade applies successfully, the user will see a popup from the Windows 11 assistant, saying a reboot is required and they have 30 minutes to complete the reboot. The user can choose to reboot right away, or choose to "Restart later". If the user chooses restart later, the update assistant will go to tray and sit there until the user manually reboots the PC. Note if the user chooses to restart later, you will see the package fail in Intune with the error "The unmonitored process is in progress, however it may timeout" which is by design.
If no actions is performed, the device will automatically reboot after 30 minutes. This can be a deal-breaker for some, so be sure to check the alternative method by making the updater available in the company portal.

Otherwise note if the Windows 11 update assistant for whatever reasons fail to apply, there is no visible popup for the user, indicating it would have failed. But based on the device restart behaviour, the user would be prompted to reboot within 24 hours, unless you chose not to enable the mandatory device restart.

![Win11UpdateAssistant](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/WIn11UpdateRebootPrompt.png?raw=true "Windows 11 Update Assistant popup")

### UX Option #2: User self-service from company portal

The user can open company portal and launch the "Upgrade to Windows 11" app. This will launch the Windows 11 Update Assistant and guide the user to start the upgrade. The update assistant will download and install Windows 11. Once it's finished, the user will get 30 minutes to perform a reboot or choose to restart at a later time to finish the update.

Note, first time when launching the update, If the health check app hasn't performed an assesment on the PC yet, simply open the health check app and hit "refresh". Then head back in the Windows 11 Update Assistant and hit the "refresh" button as well, this should allow the user to continue the update.

![Win11UpdateAssistant](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/Win11_CompanyPortal_1.png?raw=true "Windows 11 Update Assistant - Company Portal")
![Win11UpdateAssistant](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/Win11_CompanyPortal_2.png?raw=true "Windows 11 Update Assistant - Company Portal")
![Win11UpdateAssistant](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/Win11_CompanyPortal_3.png?raw=true "Windows 11 Update Assistant - Company Portal")
![Win11UpdateAssistant](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/Win11_CompanyPortal_4.png?raw=true "Windows 11 Update Assistant - Company Portal")
![Win11UpdateAssistant](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/Win11_CompanyPortal_5.png?raw=true "Windows 11 Update Assistant - Company Portal")
![Win11UpdateAssistant](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/Win11_CompanyPortal_6.png?raw=true "Windows 11 Update Assistant - Company Portal")

## Rolling back to Windows 10 in case of issues

In case you need to rollback to Windows 10, there is a few options at our disposal. But remember for these rollback options to work, in all instances, you still need to have the windows.old folder available. This gets cleaned up automatically after X amount of days, dictated by your Update ring policy in Intune. Before you do this, I would recommend you set it to at least 30 days, giving you ample time to perform the rollback if any issues should occur.

![Win11Rollback](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/FeatureUpdate-Retention.png?raw=true "Feature Update Retention")

### Option #1: Perform Rollback locally from the device settings

have the user navigate to Settings -> Windows Update -> Update History -> Recovery. Then press the "Go Back" button to start the rollback.

![Win11Rollback](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/Win11-Rollback.png?raw=true "Rollback to Windows 10")

### Option #2: Perform Rollback using Intune Update Ring Policy

You can apply a seperate Update Ring Policy for the target devices in Intune, from there you can enable the "Uninstall -> Feature" button to perform the rollback. Hoever just remember, that all target devices of the policy will perform the rollback.
![Win11Rollback](/assets/images/2024-06-20-UpgradeWindows11-UnsupportedHardware/UninstallFeatureUpdate-Intune.png?raw=true "Uninstall feature update Intune")

### Option #3: Perform Rollback using on-demand remediation

Another option is to create a Remediation, with the following [DISM command](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/dism-uninstallos-command-line-options?view=windows-11): `DISM /Online /Initiate-OSUninstall /Quiet` - Don't assign the remediation to any group, it can be used on-demand using the [new remediation on-demand feature](https://learn.microsoft.com/en-us/mem/intune/fundamentals/remediations#run-a-remediation-script-on-demand-preview). Be aware that when the rollback triggers, it will trigger an abrupt reboot on the end-users device, without any warning. It can otherwise work fine for unattended / kiosk devices.

## What to expect and look for when applying and running Windows 11 on unsupported devices

* Unsupported apps will automatically be uninstalled if the upgrade is successful (Example: Snipping tool might be removed automatically if the app is severely outdated, as observed at 1 customer. Check that you didn't disable automatic updating of store apps using a policy, I have seen this multiple times now.)
* It's likely some of your devices cannot upgrade, due to driver compatibility issues. If any issues is detected during the upgrade process, I.E: BsoD upon bootup etc., the updater will automatically rollback to Windows 10. If it fails, try to update drivers, BIOS and uninstall any old and uneccessary applications on the device, then attempt the upgrade process again. If it keeps failing, then I guess you are out of luck. The last thing you can try is a clean reinstall of Windows 11 with the override reg values applied.
* Remember if you get it working on Windows 11 23H2 it's all well and good. But Microsoft always releases new features, changes GUI Elements in the operating system etc, that might make future iterations of Windows 11 less functional on older and unsupported devices. Take special note of devices with very old graphics drivers, that can easily break stuff on Windows 11.
* If the Windows 11 Upgrade app in Intune is marked as "Installed" in Intune, under device install status, but the build number is still 10.0.19045.xxx be aware that it's just the intune reporting that is a bit sluggish. The device is updated as soon as it's listed as "Installed". The build number will change to 10.0.22xxx.xxx eventually, after the device has been allowed time to sync again.

## Final words

There is many different ways to apply the reg key to override the HW requirements and also a plethora of different ways to trigger the Windows 11 update assistant or just the setup.exe from the Windows 11 ISO. GPOs, Logon Scripts, Task sequence, On-Demand remediation, you name it. This is just one way to do it. The key is to just navigate the different options and weighing the pros/cons for your organization on how to proceed.

Otherwise be aware that if the update attempts to apply but fails, you can actually fetch that from the registry. Use the following command in PowerShell to detect if an update has been applied but failed: `Get-ItemPropertValue -Path 'HKLM:\SYSTEM\Setup\MoSetup\Tracking' -Name 'FailureCount'`.
One thing that could be worth setting up is a Remediation that detect for the FailureCount and outputs it to the console, to get an overview of how many times the upgrade failed to apply, and then you could consider doing manual checks and perhaps excluding the device from getting the upgrade again automatically. When a feature update fails, you can always find the logs under `C:\$Windows.~BT` hidden folder. There are ways to analyze these logs, but I won't cover that here.

I hope you found this blog useful, and I sincerely hope you won't need it and you will find yourself in a position to replace the aging hardware instead. The hardware that doesn't support Windows 11 is slowly coming up to 8 years old now, as of this blogs date, so it's already very old. If you are in the position where you are upgrading your unsupported hardware, just be sure to consider all the risks involved before proceeding.
