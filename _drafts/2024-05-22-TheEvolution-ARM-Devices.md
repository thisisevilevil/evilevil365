---
title: "The Evolution of ARM-based devices"
date: 2024-05-22
categories:
  - Hardware
tags:
  - ARM CPU
  - ARM Devices
  - x64-x86
  - Intel
  - AMD
  - Qualcomm
  - Snapdragon
---

If you had asked me 10 years ago if ARM-based devices would ever go mainstream for Laptops I would probably say "Never - They are only good for tablets and mobile phones!". Fast forward a few years, and now the outlook for the future regarding hardware in end-user compute has been turned on it's head, due to the progress made in the ARM space, primarily by the people at Apple and Qualcomm. [Apple released their own CPUs back in 2020](https://www.apple.com/newsroom/2020/11/apple-unleashes-m1/) based on ARM, while Qualcomm has been on the market for a while with a large market share in the smartphone industry.

For those of you unaware, Apple and Qualcomm has actually been in competition in the ARM space for the past few years, and you'll also find that both companies have poached engineers in eachothers backyard, much to the opposing companies dismay. While they still partner in some capacity, you can be sure the competition is going to be stiff the next few years. It started way back in 2016-2017, where it's clear that [Apple already got a taste of building their CPUs](https://fortune.com/2017/05/30/apple-qualcomm-esin-terzioglu/) rather than relying on Intel to supply CPUs to their entire product portfolio, as they recruited top-talent from Qualcomm.

There is a great video from Coldfusion which I recommend you watch regarding this topic, you can find it [here](https://www.youtube.com/watch?v=V68RE0M8zhk).

As I'm writing this blogpost it's May 2024. All the big hardware suppliers (Microsoft, Dell, Lenovo etc), has announced their new ARM-based laptops based on the new Snapdragon CPUs from Qualcomm, and the test results and benchmarks so far is staggering. Not only does the ARM-based CPU outperform the counterparts at AMD and Intel, the battery life of the Laptop has also massively improved! So in other words: ARM-based Laptops has entered the fray!

## Intune, Windows 10/11 with ARM-based Laptops

I will quote something from the Microsoft docs:

`
Windows 10 enables existing unmodified x86 apps to run on Arm devices. Windows 11 adds the ability to run unmodified x64 Windows apps on Arm devices! This ability to run x86 & x64 apps on Arm devices gives end-users confidence that the majority of their existing apps & tools will run well even on new Arm-powered devices.

For the best performance, responsiveness, and battery life, users will want and need Arm-native Windows apps, which means that developers will need to build or port Arm-native Windows apps`
`

All your apps are going to work on ARM-based devices, but if you want the best performance you will need to go native! I know large enterprises today, don't have this luxury, but if you look to the future, and you want to bet on ARM, you have the leverage to influence your preferred app-vendors to start pivoting towards the ARM Architecture. No small feat however, but needs to be mentioned.

"But Mads.. I enrolled an ARM-based laptop into Intune using Autopilot and now all of my apps will not deploy.. what is wrong?"
Ensure the "32 bit" requirement is also selected on your 32-bit app in Intune, as the IME is emulating 32-bit for ARM-based device.

### Some quickies regarding ARM vs Windows management vs Intune:

* ARM-native apps deploys to C:\Program Files (Arm)
* 32-bit ARM apps will leave reg keys under HKLM:\SOFTWARE\WowAA32Node
* Ensure the 32-bit requirement is selected for your Win32 apps as the IME emulates 32-bit on ARM-based devices
* Deploy ARM-native apps to your ARM devices where possible, for best performance! Look for "ARM64" instead of "64-bit" when downloading your apps. (I suspect the list of vendors starting to support ARM-native will be steadily growing during the next 1-2 years.)
* Intune win32 apps still doesn't officially natively support ARM64, but if we poke around using MS Graph, we can observe it's been added a while ago already ([Source](https://learn.microsoft.com/en-us/graph/api/resources/intune-apps-windowsarchitecture?view=graph-rest-beta)) but not visible if you use the portal - Not really sure why that is.
--> In the meantime, for Win32 apps, you can write your own custom PowerShell script to make sure it only targets ARM64 devices, and simply tick the 32bit and 64bit requirement in Intune.

### Other notable mentions:

* Driver-mania: Your hardware vendors needs to have driver support for ARM64 - Think Printers, Webcams, Headsets anything that needs a driver! The same goes for Antivirus solutions and VPN Products
* Some games will not work: Specifically if the game utilizes an anticheat, those usually deploy drivers in the operating system. If that hasn't been made to support ARM, your game will most likely cease to function.
* If you are not Using Microsoft Defender/MDE: Ensure your Antivirus vendor supports ARM! If not, it's a good time to migrate to Defender already :)
* Microsoft is encouraging all app vendors to add support for ARM64. Vendors that already [support ARM32 should migrate to ARM64 ASAP](https://learn.microsoft.com/en-us/windows/arm/arm32-to-arm64) - Note if the app vendors doesn't migrate in time, it will still run on Windows. It will just revert to running emulated rather than ARM-native.

All of the above should be essential information for IT Admins to prepare themselves for ARM devices. Some of you probably have a lot of automation and scripts built to specifically reference C:\Program Files, and some of you will probably also be deploying an ARM-based device enrolled to Intune very soon. If alot of your apps will not deploy, listed as "Not Applicable" just ensure the 32-bit requirement is selected, then it should deploy using the [previously mentioned emulation in the IME](https://learn.microsoft.com/en-us/windows/arm/apps-on-arm-x86-emulation).
