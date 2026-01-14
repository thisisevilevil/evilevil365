---
title: "Windows Updates in 2026"
date: 2026-01-14
categories:
  - Intune
tags:
  - Windows Updates
  - Intune
  - Updates during ESP
---

It's early 2026, our hangovers have barely passed, but life must go on. Microsoft is off to an early and great start in 2026 with some great new changes.

![WindowsUpdates](/assets/images/2026-14-01-WindowsUpdates-Intune-Early2026/Thumbnail.png?raw=true "Windows Update during OOBE")

## Updates during ESP

Windows Updates during ESP is making a comeback (for the 3rd time), let's hope this time it goes well, as Microsoft previous attempts of rolling it out hasn't gone according to plan. The option to disable it in ESP has already been there for a while, but the button hasn't really done anything as the feature was rolled back.

![WindowsUpdates](/assets/images/2026-14-01-WindowsUpdates-Intune-Early2026/WindowsUpdate-OOBE.png?raw=true "Windows Update during OOBE")

If you don't want any updates during ESP, be sure to set this option to "No" in your ESP. Otherwise this functionality will be re-implemented in the January 2026 as pr. comms from Microsoft. Carefully consider the impact of using this feature, as it will most likely significantly increase the enrollment time of your windows devices.

More about this in this [blog post from Microsoft](https://techcommunity.microsoft.com/blog/windows-itpro-blog/get-ready-for-windows-quality-updates-out-of-the-box/4434498)

## Gradual rollout functionality is back

The gradual rollout functionality was deprecated in October 2025, much to the dismay of a lot of IT Admins. The Gradual rollout functionality was great in many ways, as it allowed the update engine in Intune to carve out smaller groups of devices and offer feature updates in small chunks rather than offering it at the same time to all devices in a group. This allowed IT Admins more leeway in regards to testing and gauging impact of the rollout of a new feature update.

But Microsoft must have received a lot of pushback when they removed this feature, because they have decided to re-implement it. It's already available today. I wasn't even aware before a few of my customers reached out to me and asked about it. Looking at "What's new" pages, it looks like it was just silently re-implemented. I would love to link to a blog post from Microsoft, but as of this date, also after confirming with a few folks from Microsoft, it seems to have been silently implemented again. I'm sure some comms will be sent out soon

![GradualRollout](/assets/images/2026-14-01-WindowsUpdates-Intune-Early2026/GradualRollout.png?raw=true "Gradual Rollout")


That's all for now.

Happy new years to everyone :)