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

For those of you unaware, Apple and Qualcomm has actually been in competition in the ARM space for the past few years, and you'll also find that both companies have poached engineers in eachothers backyard. While they still partner in some capacity, you can be sure the competition is going to be stiff the next few years. It started way back in 2016-2017, where it's clear that [Apple already got a taste of building their CPUs](https://fortune.com/2017/05/30/apple-qualcomm-esin-terzioglu/) rather than relying on Intel to supply CPUs to their entire product portfolio, as they recruited top-talent from Qualcomm. 

There is a great video from Coldfusion which I recommend you watch regarding this topic, you can find it [here](https://www.youtube.com/watch?v=V68RE0M8zhk).

As I'm writing this blogpost it's May 2024. All the big hardware suppliers (Microsoft, Dell, Lenovo etc), has announced their new ARM-based laptops, and the test results and benchmarks is staggering. Not only does the ARM-based CPU outperform the counterparts at AMD and Intel, the battery life of the Laptop has also massively improved! So in other words: ARM-based Laptops has entered the fray!

## Intune, Windows 10/11 with ARM-based Laptops

I will quote something from the Microsoft docs:

`
Windows 10 enables existing unmodified x86 apps to run on Arm devices. Windows 11 adds the ability to run unmodified x64 Windows apps on Arm devices! This ability to run x86 & x64 apps on Arm devices gives end-users confidence that the majority of their existing apps & tools will run well even on new Arm-powered devices.

For the best performance, responsiveness, and battery life, users will want and need Arm-native Windows apps, which means that developers will need to build or port Arm-native Windows apps`
`

All your apps are going to work on ARM-based devices, but if you want the best performance you will need to go native! I know large enterprises today, don't have this luxury, but if you look to the future, and you want to bet on ARM, you have the leverage to influence your preferred app-vendors to start pivoting towards the ARM Architecture. No small feat however, but needs to be mentioned.

"But Mads.. I enrolled an ARM-based laptop into Intune using Autopilot and now all of my apps will not deploy.. what is wrong?"
Ensure the "32 bit" requirement is also selected on your 32-bit app in Intune, as the IME is emulating 32-bit for ARM-based device.

Some quickies regarding ARM vs Windows:
* ARM-native apps dpeloys to C:\Program Files (Arm)
* 32-bit ARM apps will leave reg keys under HKLM:\SOFTWARE\WowAA32Node