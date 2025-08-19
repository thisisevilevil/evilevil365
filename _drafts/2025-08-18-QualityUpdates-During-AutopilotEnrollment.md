---
title: "Quality Updates during Autopilot enrollment"
date: 2025-08-18
categories:
  - Intune
tags:
  - Autopilot
  - Quality updates during ESP
  - Cumulative Updates during ESP
  - Updates during ESP
---

In the not so distant future we will see the feature that updates Windows 11 during the enrollment process, be rolled out again to enterprise customers. In fact, its due to be released during the next Intune service release in August 2025. But this time, IT Admins actually gets a switch where we will be able to turn it off. When Microsoft previously released this feature, they enable it by default for everyone, without an option to turn it off.

It all sounds great, updates during ESP, we all want our devices to be updated, right? Well, not always. It depends. The problem with windows updates being delivered during Autopilot enrollment has always been the prolonged enrollment time. Depending on the patch level of your device, you might add another 20-30 minutes to the enrollment time, which can be pretty steep if you already have a couple of apps blocking your ESP.

Consider the impact before enabling it, and perhaps test it first on a few devices to gauge the average increase in enrollment time.

## Disabling updates in the ESP

If you don't want this feature, you will need to go to your ESP (Enrollment Status Page) and disable it. It will be enabled by default. It's a shame there isn't a condition-based enablement of this feature, so only devices that's running a patch level older than what is deemed acceptable, is routed through the updating via ESP. The alternative is enrollment restrictions, but then the user (or IT), will have to manually update the device usually by hitting SHIFT+F10 -> control update or similar, before the device can be enrolled.. not the best user experience.


