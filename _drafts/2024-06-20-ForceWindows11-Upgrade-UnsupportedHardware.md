---
title: "The Evolution of ARM-based devices"
date: 2024-06-20
categories:
  - Windows 11
tags:
  - Intune
  - Old Hardware
  - Windows 11
  - Force Windows 11 Upgrade
---

Are you looking to upgrade your ancient and seemingly unsupported hardware to Windows 11? Well look no further, you have come to the right place. 

Windows 10 support is quickly coming to an end. [October 14, 2025](https://learn.microsoft.com/en-us/lifecycle/products/windows-10-home-and-pro) is the date your organization either have to have all your devices upgraded to Windows 11 or replaced with hardware that does support Windows 11. Well there is also the 3rd option that will make your companies wallet suffer, [you can choose to pay 61$ pr. device](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/when-to-use-windows-10-extended-security-updates/ba-p/4102628) to get extended patching for Windows 10 for up to 3 years.

What if I told you there is an alternative path for those organizations that are stricken by indecision or maybe empty wallets to buy new hardware? And just to be clear, before I continue this blog post, I highly discourage this path, it's the aboslute worst option of them all. But believe me, there are companies out there that really needs this information, to take an informed decision about how to continue, as they are not in a financial position to replace their aging hardware, and potentially purchasing ESU licensing is just excerbating this problem. And as you all know, to take an informed decision, you need all the information required to make that informed decision regarding how to proceed.

## Windows 11 requirements
Let's examine the [Windows 11 requirements](https://www.microsoft.com/en-us/windows/windows-11-specifications).

* 4GB of RAM
* 64GB of storage or higher
* UEFI Capable
* TPM 2.0
* 8th Generation Intel CPU or Higher (There is a few exceptions to this rule in the 7th Generation Intel's but needs to be an extreme)

So in other words just to translate all of this for the non-techies: If your device is before the year of 2017 there is very high chance your device doesn't support Windows 11, due to the CPU being unsupported. Most devices after 2017 already have TPM 2.0, if it doesn't appear on your device, you usually just have to enable it in the BIOS, not a biggie.

There is a super easy way to get a list of your unsupported Windows 11 devices, through Intune. Go to intune, Reports -> Endpoint Analytics -> Work from Anywhere -> Windows -> "Add Filter" -> Windows 11 Readiness -> Select "Not Capable". From there, you can clearly see how many devices is not supported, and you can also easily export this view to a .csv file.

<Insert screenshot>



