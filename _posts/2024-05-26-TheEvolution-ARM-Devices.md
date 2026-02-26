---
title: "The Evolution of ARM-based devices"
date: 2024-05-26
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
  - Microsoft Intune
---

If you had asked me 10 years ago if ARM-based devices would ever go mainstream for Laptops I would probably say "Never - They are only good for tablets and mobile phones!". Fast forward a few years, and now the outlook for the future regarding hardware in end-user compute has been turned on it's head, due to the progress made in the ARM space, primarily by the people at Apple and Qualcomm. [Apple released their own CPUs back in 2020](https://www.apple.com/newsroom/2020/11/apple-unleashes-m1/) based on ARM, while Qualcomm has been on the market for a while with a large market share in the smartphone industry.

For those of you unaware, Apple and Qualcomm has actually been in competition in the ARM space for the past few years, and you'll also find that both companies have poached engineers in each others backyard, much to the opposing companies dismay. While they still partner in some capacity, you can be sure the competition is going to be stiff the next few years, much like we have seen the past 2 decades between Intel and AMD. It started way back in 2016-2017, where it's clear that [Apple already got a taste of building their own CPUs](https://fortune.com/2017/05/30/apple-qualcomm-esin-terzioglu/) rather than relying on Intel to supply CPUs to their entire product portfolio, as they recruited top-talent from Qualcomm.

Meanwhile Pat Gelsinger, the CEO of Intel, [publicly rubbed off the threat of ARM-based CPUs](https://www.techpowerup.com/315228/intel-ceo-doesnt-see-arm-based-chips-as-competition-in-the-pc-sector?cp=2) towards Intel dominance on the market, last October. If things goes to plan according to Qualcomm, I suspect Mr. Gelsinger will have to eat his words very soon, time will tell.

There is a great video from Coldfusion which I recommend you watch regarding this topic, you can find it [here](https://www.youtube.com/watch?v=V68RE0M8zhk).

As I'm writing this blogpost it's May 2024. All the big hardware suppliers (Microsoft, Dell, Lenovo etc), has announced their new ARM-based laptops based on the new Snapdragon CPUs from Qualcomm, and the test results and benchmarks so far is staggering. Not only does the ARM-based CPU outperform the counterparts at AMD and Intel, the battery life of the Laptop has also massively improved! So in other words: ARM-based Windows Laptops has entered the fray!

## Intune, Windows 10/11 with ARM-based Laptops

I will quote something from the [Microsoft docs](https://learn.microsoft.com/en-us/windows/arm/add-arm-support):
> Windows 10 enables existing unmodified x86 apps to run on Arm devices. Windows 11 adds the ability to run unmodified x64 Windows apps on Arm devices! This ability to run x86 & x64 apps on Arm devices gives end-users confidence that the majority of their existing apps & tools will run well even on new Arm-powered devices. For the best performance, responsiveness, and battery life, users will want and need Arm-native Windows apps, which means that developers will need to build or port Arm-native Windows apps

Most of your apps are going to work on ARM-based devices, but if you want the best performance you will need to go native! Large enterprises today will definitely struggle if they use a lot of in-house apps and in-house tools that relies on a driver, but if you look to the future, and you want to bet on ARM, you have the leverage to influence your preferred app-vendors to start pivoting towards the ARM Architecture. No small feat for some. The same goes for all your hardware suppliers, they need to create drivers specifically for ARM64.

### Some quickies regarding ARM vs Windows management vs Intune

1. ARM-native apps deploys to C:\Program Files (Arm)
2. 32-bit ARM apps will leave reg keys under HKLM:\SOFTWARE\WowAA32Node
3. Ensure the ARM-64 bit requirement is ticked under app requirement to make sure ARM-native apps deploys only to ARM devices.
4. Deploy ARM-native apps to your ARM devices where possible, for best performance! Look for "ARM64" or "Aarch64" instead of "64-bit" when downloading your apps. (I suspect the list of vendors starting to support ARM-native will be steadily growing during the next few years)

* In the meantime, for Win32 apps, you can write your own custom PowerShell Requirement script to make sure it only targets ARM64 devices

Based on all of the above, also think about File-based detection rules, any scripts/tools you are using that are referencing C:\Program Files etc.

* **Example**: Some scripts will have some logic based on the %PROCESSOR_ARCHITECTURE% variable. If this returns "AMD64" we know we are running in 64-bit mode, else we assume x86 (32-bit). That logic will likely break your script on an ARM-device as the %PROCESSOR_ARCHITECTURE% variable will return "ARM64" on ARM-based devices.

![ProcessorArchitecture](/assets/images/2014-05-26-TheEvolution-ARM-Devices/ProcessorArchitectureVariable.png?raw=true "Processor Architecture Variable")

### Other notable mentions

* **Drivers**: Your hardware vendors needs to have driver support for ARM64. Think Printers, Webcams, Headsets anything that needs a driver! The same goes for Antivirus solutions and VPN Products
* **Some games will not work**: Specifically if the game utilizes an anticheat functionality, those usually deploy drivers in the operating system. If that hasn't been made to support ARM, your game will most likely not work on an ARM device.
* **Antivirus**: Ensure your Antivirus vendor supports ARM! If not, it's a good time to migrate to Defender already :)
* **App-vendor support**: Microsoft is encouraging all app vendors to add support for ARM64. Vendors that already [support ARM32 should migrate to ARM64 ASAP](https://learn.microsoft.com/en-us/windows/arm/arm32-to-arm64) - Note if the app vendors doesn't migrate in time, it will still run on Windows. It will just revert to running emulated rather than ARM-native, where the app might suffer a performance impact as a result.

All of the above should be essential information for IT Admins to prepare themselves for ARM devices. Some of you probably have a lot of automation and scripts built to specifically reference C:\Program Files. Some of you will probably also be deploying ARM-based device enrolled to Intune very soon. 
If alot of your apps will not deploy on an ARM-based device, listed as "Not Applicable" in Intune, ensure the 32-bit requirement is selected, then it should deploy using the [previously mentioned emulation in the IME](https://learn.microsoft.com/en-us/windows/arm/apps-on-arm-x86-emulation).

## Finalizing words

I hope this post has been useful to you. ARM-based windows devices is now coming to market more broadly and swiftly than we have ever seen before, where the ones based on Snapdragon X Elite CPUs will seemingly be branded "Copilot+ PCs". Just look at [this blog post by Microsoft](https://blogs.microsoft.com/blog/2024/05/20/introducing-copilot-pcs/)

Personally, I think ARM-based windows devices is now here to stay, but the driver aspect is going to be problematic during the first 1-2 years for a lot of companies.

What you can already start doing today to plan for ARM-based devices, as an IT Admin:

* Prepare apps in Intune: Ensure 32-bit support is ticked but also start looking for ARM-based versions of your apps where possible.
* Check if your core apps in your organisation supports ARM CPUs, starting with your Antivirus solution
* Check all your devices and gadgets you use in your company, if the manufacturer has added ARM64 drivers for their products
* Go through any automation/scripts you have put into place where you have used logic based on %PROCESSOR_ARCHITECTURE% or similar
* Create a Dynamic Group + Filter to filter on ARM-based devices for grouping/targeting/filtering purposes.