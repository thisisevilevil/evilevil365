---
title: "YOLO Upgrade to Windows 11"
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

Windows 10 support is quickly coming to an end. [October 14, 2025](https://learn.microsoft.com/en-us/lifecycle/products/windows-10-home-and-pro) is the date your organization either have to have all your devices upgraded to Windows 11 or replaced with hardware that does support Windows 11. Well there is also the 3rd option that will make your companies wallet suffer, [you can choose to pay 61$ pr. device](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/when-to-use-windows-10-extended-security-updates/ba-p/4102628) to get extended patching (ESU Licenses) for Windows 10 for up to 3 years.

What if I told you there is an alternative path for old and unsupported hardware, for those with empty wallets? And just to be clear, before I continue this blog post, I highly discourage this path, it's the aboslute worst option of them all. Or well.. the worst option is to do nothing, because then you won't get any patches come October next year.. :)

There are companies out there that really needs this information, to take an informed decision about how to continue, as they are not in a financial position to replace their aging hardware. Purchasing ESU licensing is just excerbating the financial problems these companies are in. And as you all know, to take an informed decision, you need all the information required to make that informed decision regarding how to proceed.

## Windows 11 requirements
Let's examine the [Windows 11 requirements](https://www.microsoft.com/en-us/windows/windows-11-specifications).

* 4GB of RAM
* 64GB of storage or higher
* UEFI Capable
* TPM 2.0
* 8th Generation Intel CPU or Higher (There is a few exceptions to this rule in the 7th Generation Intel's but needs to be an extreme version)

So in other words just to translate all of this for the non-techies: If your device is before the year of ~2017 there is very high chance your device doesn't support Windows 11, due to the CPU being unsupported. Most devices after ~2017 already have TPM 2.0 and an 8th Gen Intel CPU or higher. If the TPM doesn't appear on your device, you usually just have to enable it in the BIOS, not a biggie. This is especeially relevant on any devices purchased for Gaming.. a few years ago the TPM commonly came disabled by default from the Motherboard manufacturers. All you need to do is to go to the BIOS and enable it, to get Windows 11 support.

There is otherwise a super easy way to get a list of your unsupported Windows 11 devices, through Intune. Go to intune, Reports -> Endpoint Analytics -> Work from Anywhere -> Windows -> "Add Filter" -> Windows 11 Readiness -> Select "Not Capable". From there, you can clearly see how many devices is not supported and why they are not supported, and you can also easily export this view to a .csv file.

<Insert screenshot>

## Forcefully applying Windows 11 from Intune on unsupported hardware

Disclaimer: This solution will also be presented as-is. Also be aware that Microsoft might choose to make changes on how to bypass the Windows 11 requirements, and the workaround I present here might also stop working. The best option is to replace unsupported hardware.

I have created a script that downloads the [Windows 11 update assistant](https://www.microsoft.com/software-download/windows11) and runs it silently in the background. I have actually used this script for the past year and a half to nudge stubborn devices over to the latest version of Windows 11. If you want to examine the code you can find it [here](https://github.com/thisisevilevil/IntunePublic/blob/main/Scripts/Win11Unsupported/UpgradeToWindows11.ps1) The oode is very simple.
The key here is to set the correct registry value to override the hardware requirements. Be aware that setting this specific value, overrides the TPM 2.0 and CPU requirement, but for some reason, TPM 1.2 is still required, you can read more about this option [here](https://support.microsoft.com/en-us/windows/ways-to-install-windows-11-e0edbbfb-cfc5-4011-868b-2ce77ac7c70e). So if there is no TPM, then this option won't work for you. You will need to reinstall Windows 11, then it's possible to override the hardware requirements. This can be done using Rufus when creating a bootable USB but also using tools like SCCM and MDT using the unattend.xml file to apply the correct registry values to override the hardware requirements.

Let's move on.

I have found that for this to work, we need to have the PC Health Check app installed. The Windows 11 update assistant relies on this, to apply the upgrade, and during testing I have found that even though we override hardware requirements, it's still looking for that app to be present on the device, before it can proceed to the upgrade. You can deploy it from Intune as a Win32 app or a LoB app. You can download it from [here](https://aka.ms/GetPCHealthCheckApp) - Be aware that this health check app might popup on the screen when installed for the current user. Test it and communicate to the organization where pertinent.

To deploy the app using Intune perform the following steps:
1. Download the .intunewin file containing the script from [here]()
-> Note that this script also deploys the correct reg value to override the hardware requirements, automatically.
2. Upload it to intune as a win32 app
3. Specify the following when uploading the package:
* **Install command:** `%windir%\Sysnative\WindowsPowerShell\v1.0\Powershell.exe -ExecutionPolicy Bypass -File UpgradeToWindows11.ps1`
* **Uninstall command:** `not required`
* **Device restart hehaviour:** `Intune will force a mandatory device restart`
* **Required disk space:** 10000MB
* **Detection, Custom Script:** `Use custom detection script. Download from [here](https://github.com/thisisevilevil/IntunePublic/blob/main/Scripts/Win11Unsupported/Detect-Win11Installed.ps1)`
* **Installation time required (mins)** 120 minutes

As you can see, we have selected "Intune will force a mandatory device restart". This will actually not come into play, if everything goes according to plan. The user will get a popup where they have 30 minutes to perform a reboot. There is no way to alter the reboot timer or message, so keep this in mind. You will see that later in the User Experience section.
However, if there are any pending reboots or any other reasons the Windows 11 update might not correctly apply, the Intune Management Extension will attempt to restart the device. **For this reason, remember to enable the "Restart Grace" period in your app assignment, to show reboot notifications/snooze options to the end-user. Note that it's disabled by default for each group where you assign the app. If you forget to set it, the user will experience an abrupt reboot without any warning whatsoever.**.
If you don't want this to be the case here, just set the device restart behvaiour to "No specific action".

Since we are already cutting the red tape here, we might as well keep going. You can override any [safeguard holds](https://learn.microsoft.com/en-us/windows/deployment/update/safeguard-holds) by applying the following policy using Settings catalog:
<Insert Screenshot>

This will override any safeguard holds applied from Microsoft end because of Apps, Drivers or even certain BIOS Versions that Microsoft has deemed problematic for Windows 11 23H2. There is otherwise a nice report in Intune where you can also identify these devices. Navigate to Reports -> Windows Updates -> Reports -> Windows feature update device readiness report. You can also go to the Compatibility Risks report to see the specific Drivers and apps that you have in your org that might be blocking an upgrade.

## User Experience~
If the Windows 11 upgrade applies successfully, they will see a popup from the Windows 11 assistant, saying a reboot is required and they have 30 minutes to complete the reboot. The user can choose to reboot right away, or wait the full 30 minutes to perform the reboot. If no actions is performed, the device will automatically reboot after 30 minutes. This can be a deal-breaker for some, so see the "Alternative" section below.

<Insert Screenshot of Windows 11 Update Assistant Reboot prompt>

## Alternative: Making the Windows 11 Update Assistant available in company portal for unsupported hardware
There is a way you can make the Windows 11 Update assistant available in the company portal. I'm aware you can just download the ISO of Windows 11, package it as a Win32 app and let them run setup.exe silently in system context, but you have to package the full ISO contents in the Win32 app, which can be super clunky due to the large package size but still viable in some scenarios. The good thing about the Windows 11 Update Assistant is that it's very lightweight, the file is only around 4mb in size.

Making the Windows 11 Update Assistant available in the Company portal, gives the user the option of when to start the upgrade at a convenient time.

NOTE: Be aware for fully supported devices, you can deploy optional feature updates, to the user, so users can go to windows update instead to apply the update. This is the recommended approach in supported scenarios, see more info about this [here](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/more-flexible-windows-feature-updates/ba-p/4139230#:~:text=How%20to%20deploy%20and%20monitor,and%20select%20Create%20new%20Profile.)

Some of you folks probably remember ServiceUI from MDT. We can utilize ServiceUI in our Windows 11 Update Assistant package, to let users run the app from company portal and interact with the Windows 11 Update Assistant, even though it's running in System Context. I have prepared the .intunewin file for your convenience, but you can also see the contents of the package [here]()


* **Install command:** `ServiceUI.exe -process:explorer.exe Windows11InstallationAssistant.exe`
* **Uninstall command:** `not required`
* **Device restart hehaviour:** `No specific action`
* **Required disk space:** 10000MB
* **Detection, Custom Script:** `Use custom detection script. Download from [here](https://github.com/thisisevilevil/IntunePublic/blob/main/Scripts/Win11Unsupported/Detect-Win11Installed.ps1)`
* **Installation time required (mins)** 120 minutes
* **Logo**: Download [here]()

We also need to deploy the registry value that overrides

## What to expect when running Windows 11 on unsupported devices

* Unsupported apps will automatically be uninstalled (Example: Snipping tool, outdated)
* Best effort: If things stop breaking, don't expect any support from the hardware vendor or from Microsoft
* It's likely some of your devices cannot upgrade, due to driver compatibility issues. If any issues is detected during the upgrade process, I.E: BsoD upon bootup etc., the updater will automatically rollback to Windows 10. If it fails, try to update drivers, BIOS and uninstall any old and uneccessary applications on the device, then attempt the upgrade process again. If it keeps failing, then I guess you are out of luck. The last thing you can try is a clean reinstall of Windows 11 with the override reg values applied.
* Remember if you get it working on Windows 11 23H2 it's all well and good. But Microsoft always releases new features, changes GUI Elements in the operating system etc, that might make future iterations of Windows 11 less functional on older and unsupported devices. Take special note of devices with very old graphics drivers, that can easily break stuff on Windows 11.



## Rolling back to Windows 10 in case of issues



