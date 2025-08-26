---
title: "Quality Updates during Autopilot enrollment"
date: 2025-08-26
categories:
  - Intune
tags:
  - Autopilot
  - Quality updates during ESP
  - Cumulative Updates during ESP
  - Updates during ESP
---

In the latest intune service release for August 2025, we are getting some nice new additions, you can read about that [here](https://techcommunity.microsoft.com/blog/microsoftintuneblog/what%E2%80%99s-new-in-microsoft-intune-august-2025/4445612).

The big change in this update is the return of updates during Autopilot enrollments. And it will be turned on by default. Some of you might remember the previous attempt to roll this out, didn't go so well, since it was enabled by default without the option to turn it off. Now we have the option to turn it off!

It all sounds great, updates during ESP, we all want our devices to be updated, right? Well, not always. It depends. The problem with windows updates being delivered during Autopilot enrollment has always been the prolonged enrollment time. Depending on the patch level of your device, you might add another 20-30 minutes to the enrollment time, which can be pretty steep if you already have a couple of apps blocking your ESP.

Consider the impact before enabling it, and perhaps test it first on a few devices to gauge the average increase in enrollment time.

## Disabling updates in the ESP

If you don't want this feature, you will need to go to your ESP (Enrollment Status Page) and disable it. It will be enabled by default. 

![ESP](/assets/images/2025-26-08-Updates-ESP/Toggle-ESP.png?raw=true "ESP Windows Update Toggle")

>NOTE: If you don't see this in your ESP Settings yet, it's because the feature is still rolling out globally

## Final thoughts

What could be a nice addition here, would be a conditional-based rule for it to trigger, so IT Admins could choose to enact the updates during ESP but only if the OS Patch level was below a certain build number. The alternative is enrollment restrictions, to block devices from enrolling when they are below a certain OS Patch level, but then the user (or IT), will have to manually update the device usually by hitting SHIFT+F10 -> control update or similar, before the device can be enrolled.. not the best user experience.

For now, this is still a very welcome change, that has been highly requested for a long time - Nice it's finally here :)