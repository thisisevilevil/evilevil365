---
title: "Quality Updates During Autopilot Enrollment is back"
date: 2025-08-26
categories:
  - Intune
tags:
  - Autopilot
  - Quality updates during ESP
  - Cumulative Updates during ESP
  - Updates during ESP
---

UPDATE 10th of September 2025: Looks like it's being rolled back (again).
> **Editor’s note 9.8.2025:**  
> This capability has been delayed by a couple of months to help ensure delivery of the best possible experience. You can start configuring the new setting on the Enrollment Status Page (ESP), but you won’t see the new user interface yet. We’ll update this post with a revised timeline as soon as it’s available.

[Source](https://techcommunity.microsoft.com/blog/windows-itpro-blog/get-ready-for-windows-quality-updates-out-of-the-box/4434498)

In the Intune service release for August 2025, we’re getting some great new additions, which you can read about [here](https://techcommunity.microsoft.com/blog/microsoftintuneblog/what%E2%80%99s-new-in-microsoft-intune-august-2025/4445612). 

The big change in this update is the return of Windows quality updates (not feature updates!) during Autopilot enrollment—and they will be enabled by default. You can read the full announcement [here](https://techcommunity.microsoft.com/blog/windows-itpro-blog/get-ready-for-windows-quality-updates-out-of-the-box/4434498).
Some of you might remember the previous attempt to roll this out didn’t go so well, as it was enabled by default without an option to turn it off. This time, we finally have the ability to disable it if needed.

It all sounds great—updates during ESP. We all want our devices to be updated, right? Well, not always. It depends. The main problem with Windows updates being delivered during Autopilot enrollment has always been the extended enrollment time. Depending on the device’s patch level, and the network speed, you might add another 20–30 minutes to the process, which can be significant if you already have several apps blocking your ESP.

Be sure to consider the impact before enabling this feature, and test it first on a few devices to gauge the average increase in enrollment time.

## Disabling Updates in the ESP

If you don’t want this feature, you’ll need to go to your ESP (Enrollment Status Page) settings and disable it. By default, it will be turned on.  

![ESP](/assets/images/2025-26-08-Updates-ESP/Toggle-ESP.png?raw=true "ESP Windows Update Toggle")

> **Note:** If you don’t see this option in your ESP settings yet, it’s because the feature is still rolling out globally.

## Final Thoughts

A great addition in the future would be a conditional rule that allows IT admins to enable updates during ESP only if the OS patch level is below a certain build number. The current alternative is to use enrollment restrictions, which block devices from enrolling when they’re below a required patch level. However, this forces the user (or IT) to manually update the device—usually by pressing **Shift+F10** → `control update` or similar—before it can be enrolled. Not exactly the best user experience.

For now, though, this is still a very welcome change that has been highly requested for a long time. It’s nice to see it finally here. 🙂
