---
title: "The case of the missing GPU"
date: 2025-04-14
categories:
  - Intune
tags:
  - Troubleshooting
  - GPU not detected
  - NVIDIA GPU not detected
  - GPU missing
  - Graphics card not detected
  - OpenGL issue
---

I have recently finished up a lengthy troubleshooting session with a customer that lasted around 2 months. The issue here was that this customer is using a lot of different Dell Precision Laptops with varying GPU models like the NVIDIA A3000 or similar where the NVIDIA GPU is not detected in Windows. The iGPU (intel-based) is working fine, but when trying to open any GPU-demanding application, an error would be displayed suggesting the GPU is missing. Depending on the application, the error could differ.

I reproduced the issue on several different Dell Precision laptops with the Furmark application, a simple tool for benchmarking the GPU, and the error looked like below:

![GPUError](/assets/images/2025-04-14-TheCase-OfTheMissing-GPU/OpenGL_Error.png?raw=true "Furmark OpenGL error")

So what is going on here? Is it just simple driver issue? Read on to find out or simply skip directly to the solution :)

## Debugging the issue

Prepare yourself to go on a lot of wild goose chases when troubleshooting this issue. These kind of Laptops utilizes something known as "Switchable Graphics" to conserve battery. If the laptop was always using the NVIDIA GPU, your battery life could in theory decrease to as low as 45-60 minutes if it's not connected to power. So these laptops usually comes with an internal iGPU (iGPU) from Intel or AMD that can be used to render the desktop, play youtube videos etc. for your basic graphic needs, but when any graphics demanding tasks is required like playing games or using applications like AutoCAD or Maya, that's where the discreet GPU (dGPU) comes in, usually from NVIDIA, at the expense of using a lot more battery power.

Historically there has been an abundant amount of driver issues with these kind of laptops, so when you go and google or ask copilot for these issues, you will find them going back almost 20 years. In this case, we can go and search for things like "NVIDIA GPU not detected switchable graphics" or "OpenGL error when opening application - GPU not detected" or similar. The common things to try to resolve these kind of issues are usually the following:

* Update to the latest NVIDIA GPU Driver
* Update to the latest iGPU Driver (For instance the Intel driver)
* Update the BIOS of the system
* Activating high performance mode power plan

However none of these seemed to resolve the problem. What excerbated the problem was the fact that some users reported it worked sometimes, but then after a reboot it stopped working. Also, when trying to reproduce the issue, it would seemingly work but after 1 or 2 reboots, the issue just came back.

Reinstalling Windows 11 with a Clean image and then installing all the latest drivers did not resolve the problem, which I have found normally resolves these issues where switchable graphics doesn't work. Configuring the NVIDIA GPU to always be utilized in the Control Panel also seemingly had no effect.

![Graphics](/assets/images/2025-04-14-TheCase-OfTheMissing-GPU/OpenGL_Error.png?raw=true "Swicthable Graphics options NVIDIA GPU")

## The solution

When I assist my customers on their Cloud native autopilot journey, we also go about deploying ASR Rules and the latest Security Baseline. There are common settings in the security baseline that is usually on my radar from the get go, that we know can causes issues, so we have processes and guidelines in place for how to exclude devices or groups from certain settings upfront.

This time, we found the missing GPU was caused by a feature known as [DMA Guard](https://learn.microsoft.com/en-us/windows/client-management/mdm/policy-csp-dmaguard?WT.mc_id=Portal-fx#deviceenumerationpolicy). DMA guard is also part of the security baseline.