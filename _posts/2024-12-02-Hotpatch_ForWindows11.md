---
title: "Windows 11 Hotpatch is finally here!"
date: 2024-12-02
categories:
  - Windows 11
tags:
  - Windows 11
  - Patching
  - Intune
  - Security
---

The day has finally come. We have heard whispers about this for years, and now that it's finally here it's hard to believe. [Windows 11 Hotpatch has arrived!](https://support.microsoft.com/en-us/topic/release-notes-for-hotpatch-public-preview-on-windows-11-version-24h2-enterprise-clients-c117ee02-fd35-4612-8ea9-949c5d0ba6d1)

Why should you care?

* Much faster deployment times of updates, as updates are kept smaller and as a result will install much faster
* Less restarts: Hotpatch will eliminate the amount of restarts required thus increasing security and end-user productivity

This is a huge game changer for Microsoft and for Windows Client computing in general if you ask me, and as this technology improves, we will see a massive increase in security and the general security posture of companies. I also suspect some companies that adopt very old school testing of each monthly patch, will also greatly benefit from this technology, as they will eventually find that manually testing each patch is futile, and should instead adopt technologies like autopatch that will divide your entire estate into rings/waves of devices.

For those of you managing Servers in Azure you will also know this technology has been here for a while, but only available in Azure. Now it's in preview for Windows Server 2025 and now also for Windows 11. Super awesome!

## Prerequisites

To use Windows 11 Hotpatch your device needs to meet the following criteria:

* Run Windows 11 24H2 (Build 26100.2033 or later)
* Windows Enterprise Edition
* Microsoft 365 Licenses F3, E3, E5, A3 or A5
* VBS (Virtualization-based security) Must be enabled (This is enabled by default on Windows 11, unless you have opted to install Windows 11 on unsupported hardware/configuration)
* Latest Baseline Release: Devices must be on the latest baseline release version to qualify for Hotpatch updates. Microsoft releases Baseline updates quarterly as standard cumulative updates. For more information on the latest schedule for these releases, see [Release notes for Hotpatch](https://support.microsoft.com/en-us/topic/release-notes-for-hotpatch-in-azure-automanage-for-windows-server-2022-4e234525-5bd5-4171-9886-b475dabe0ce8?preview=true)

If a device doesn't meet any of these pre-reqs it will simply revert to downloading the regular cumulative update

## Activating hotpatch

Navigate to Intune, Windows Updates -> Quality Updates and create a new profile. Select "Windows Quality Update Policy (preview)". Make sure to set the setting ""When available, apply without restarting the device ("hotpatch")" to "allow", as this is the magic lever to enable hotpatch for your devices. Assign to your Windows 11 24H2 devices.

![HotpatchPolicy](/assets/images/2024-12-02-Hotpatch_ForWindows11/CreatePolicy-1.png?raw=true "Create hotpatch policy")
![HotpatchPolicy](/assets/images/2024-12-02-Hotpatch_ForWindows11/CreatePolicy-2.png?raw=true "Create hotpatch policy")
![HotpatchPolicy](/assets/images/2024-12-02-Hotpatch_ForWindows11/CreatePolicy-3.png?raw=true "Create hotpatch policy")

This profile type doesn't currently support device filters, so you will need to create a dynamic entraID group to capture your Windows 11 24H2 devices. You can use this Dynamic rule to capture them: `(device.deviceOSVersion -startsWith "10.0.26100")`

And that's it! You are now ready to test the new hotpatch feature :)

## Final notes

Not all Windows Updates released will be hotpatch eligible. Same as for servers, there is a hotpatch calendar to see if a patch is hotpatch eligible or if it will be a "baseline" update that requires a reboot. For now, this calendar can be found [here](https://support.microsoft.com/en-us/topic/release-notes-for-hotpatch-public-preview-on-windows-11-version-24h2-enterprise-clients-c117ee02-fd35-4612-8ea9-949c5d0ba6d1).

Also note that if a later hotpatch-eligible patch is released but your device doesn't have the previous baseline patch, the device will still have to reboot to apply the baseline patch, in order to get the later hotpatch-eligible patch, so keep this in mind.

Otherwise this is such great news for Windows Client computing and this will greatly improve security and end-user productivity by deploying patches much faster without those required reboots! 

Read the full announcement from Microsoft from [here](https://techcommunity.microsoft.com/blog/windows-itpro-blog/hotpatch-for-client-comes-to-windows-11-enterprise/4302717)