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

Windows 10 support is quickly coming to an end. [October 14, 2025](https://learn.microsoft.com/en-us/lifecycle/products/windows-10-home-and-pro) is the date your organization either have to have all your devices upgraded to Windows 11 or replaced with hardware that does support Windows 11. Well there is also the 3rd option that will make your companies wallet suffer, [you can choose to pay 61$ pr. device](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/when-to-use-windows-10-extended-security-updates/ba-p/4102628) to get extended patching for Windows 10 for up to 3 years.

What if I told you there is an alternative path for those organizations that are stricken by indecision or maybe empty wallets to buy new hardware? And just to be clear, before I continue this blog post, I highly discourage this path, it's the aboslute worst option of them all. But believe me, there are companies out there that really needs this information, to take an informed decision about how to continue, as they are not in a financial position to replace their aging hardware, and potentially purchasing ESU licensing is just excerbating this problem. And as you all know, to take an informed decision, you need all the information required to make that informed decision regarding how to proceed.

## Windows 11 requirements
Let's examine the [Windows 11 requirements](https://www.microsoft.com/en-us/windows/windows-11-specifications).

* 4GB of RAM
* 64GB of storage or higher
* UEFI Capable
* TPM 2.0
* 8th Generation Intel CPU or Higher (There is a few exceptions to this rule in the 7th Generation Intel's but needs to be an extreme version)

So in other words just to translate all of this for the non-techies: If your device is before the year of 2017 there is very high chance your device doesn't support Windows 11, due to the CPU being unsupported. Most devices after 2017 already have TPM 2.0 and an 8th Gen Intel CPU or higher. If the TPM doesn't appear on your device, you usually just have to enable it in the BIOS, not a biggie.

There is a super easy way to get a list of your unsupported Windows 11 devices, through Intune. Go to intune, Reports -> Endpoint Analytics -> Work from Anywhere -> Windows -> "Add Filter" -> Windows 11 Readiness -> Select "Not Capable". From there, you can clearly see how many devices is not supported, and you can also easily export this view to a .csv file.

<Insert screenshot>

## Forcefully applying Windows 11 from Intune on unsupported hardware

Before we continue, just underlining what I mentioned before, I highly discourage this path, but I recognize some companies is in a rough spot and need to be presented with all available alternatives. This solution will also be presented as-is, be aware that Microsoft might choose to make changes on how to bypass the Windows 11 requirements, and the workaround I present here might stop working.

I have created a script that downloads the [Windows 11 update assistant](https://www.microsoft.com/software-download/windows11) and runs it silently in the background. I have actually used this script for the past year and a half to nudge stubborn devices over to the latest version of Windows 11. If you want to examine the code you can find it [here](https://github.com/thisisevilevil/IntunePublic/blob/main/Scripts/Win11Unsupported/UpgradeToWindows11.ps1)

To deploy the app using Intune perform the following steps:
1. Download the .intunewin file containing the script from [here]()
2. Upload it to intune as a win32 app
3. Specify the following:

* **Install command:** `%windir%\Sysnative\WindowsPowerShell\v1.0\Powershell.exe -ExecutionPolicy Bypass -File UpgradeToWindows11.ps1`
* **Uninstall command:** `not available`
* **Required disk space:** 10000MB
* **Detection, Custom Script:** `Use custom detection script. Download from [here](https://github.com/thisisevilevil/IntunePublic/blob/main/Scripts/Win11Unsupported/Detect-Win11Installed.ps1)`
* **Installation time required (mins)** 120 minutes

Since we are already cutting the red tape here, we might as well keep going. You can override any safeguard holds by applying the following policy using Settings catalog:
<Insert Screenshot>

Essentially this will override any safeguard holds applied from Microsoft end because of Apps or Drivers that might be blocking.

## What to expect when running Windows 11 on unsupported devices




## Rolling back to Windows 10 in case of issues



