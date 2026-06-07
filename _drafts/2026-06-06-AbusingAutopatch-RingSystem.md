---
title: "Using Autopatch's ring system for your own benefit"
date: 2026-06-09
categories:
  - Intune
tags:
  - Autopatch
  - Windows Autopatch
  - Windows Patching
  - Staggered rollout
  - ring rollout
---

It's one of those secrets that's used a lot by consultants: Whenever we make changes, we don't want to just YOLO-rollout an app, policy, script etc. to all devices, nor do we wan't to use that static entra group that is manually maintained by 1 fella, where half of the devices are inactive/stale anyway. When Autopatch was released a couple of years ago, I quickly noticed the ring system in autopatch and how you could create more rings and have it dynamically spread devices into those groups based on a % you as the IT Admin defines.

Did you know that autopatch simply creates those manually defined rings as static entra groups? Autopatch will then take care to dynamically spread the devices to those groups, when devices are registered.

But let's back up a bit. What is Autopatch in the first place?

## What is Windows Autopatch

Windows Autopatch is Microsoft's managed update service that takes the heavy lifting of patch management off IT teams' plates. It is a cloud-based service from Microsoft that automates the patching of Windows and other Microsoft software such as Edge, Microsoft Office and all software supported by the Windows Update engine.

When autopatch is enabled in your tenant, and you define new "Autopatch groups", it will automatically create all the relevant policies for Windows Patching, Feature updates, Edge, Office etc., so the IT Admin doesn't have to go throuh a click-a-thon to manually configure it.

I still meet a lot of customers whose very skeptical of autopatch, What some of you probably don't know that WufB has already been merged into the autopatch a while back. Those of you who spend a lot of hours in the Intune Admin console, you probably noticed things have moved around a lot, and that's actually more so due to the many changes made to autopatch as it has matured during the past few years.

![Announcement](/assets/images/2026-06-06-AbusingAutopatch-RingSystem/September2024-Autopatch-Unification.png?raw=true "September2024 WufB Unification")

## Getting started with Autopatch

To start configuring autopatch, you will need an Entra group, which will be used for onboarding your devices to autopatch. This can be a static or dynamic entragroup. Navigate to Tenant Administration > Autopatch groups and click "New autopatch group".
![Autopatch](/assets/images/2026-06-06-AbusingAutopatch-RingSystem/Autopatch-1.png?raw=true "Getting started with autopatch")

Give it a nice name and click next.
>NOTE: The name you will give your autpatch group will be appended to all of the auto-generated policies and the name for the Entra groups.
![Autopatch](/assets/images/2026-06-06-AbusingAutopatch-RingSystem/Autopatch-2.png?raw=true "Getting started with autopatch")

Now for the important bits. First you will add the Entra group that you will use to onboard devices to your autopatch setup. I recommend using a dynamic group so you don't have to manually maintain it.

>Warning: I recommend against using Cloud-synced groups from SCCM Collections, where possible. Try to work within the parameters of entra dynamic groups to keep your setup simple. SCCM Cloud sync make things uneccessarily complicated and slow.

Click "Add Deployment ring" for each ring you would like to have. if you have 10-20k devices, 5 rings is a pretty good starting point. Proceed to adjust the % of each ring, that will decide how many devices will get to be member of rings. The example in my screenshot is 5%, 15%, 20%, 30% and 30%, which is a good even split in the later rings, while you still have fair volume in the earlier rings allowing to capture potential noise earlier. 

Once you are happy, click next.

![Autopatch](/assets/images/2026-06-06-AbusingAutopatch-RingSystem/Autopatch-3.png?raw=true "Getting started with autopatch")

>NOTE: The Test and early rings are not populated automatically. They will need to be manually populated by an IT Admin with access to the Intune portal by using the "Autopatch group membership" blade. The Test ring can commonly be used by IT Personnel in the device team, helping to test potential changes before proceeding to Ring 1. Ring Last can be used for devices that needs a long deadline/grace period before enforcing a reboot.

In the update types, I recommend you disable feature updates. Enabling feature updates in the autopatch blade will have autopatch create what is referred to as a "feature update anchor policy". This creates an "Immediate Start" policy for the feature update you set, which will cause all your devices to update immediately, if they are not already on the feature update you specify. What you really want to do, to control feature updates, is to use the Autopatch [multi-phase feature update rollout](https://learn.microsoft.com/en-us/windows/deployment/windows-autopatch/manage/windows-autopatch-windows-feature-update-overview#multi-phase-feature-update). 

Also do not select Driver Updates if you are already managing driver updates with the OEM's own solution, i.e: Dell Command | Update, HPIA or Lenovo System Update. 

Click next again

![Autopatch](/assets/images/2026-06-06-AbusingAutopatch-RingSystem/Autopatch-4.png?raw=true "Getting started with autopatch")

In the next blade, you can change which rings will get Edge Beta and Edge Stable. Edge Beta for Ring test is default, just be prepared for some strangeness in your Edge browser sometimes, because you will get new features before the masses.

Click next again.

![Autopatch](/assets/images/2026-06-06-AbusingAutopatch-RingSystem/Autopatch-5.png?raw=true "Getting started with autopatch")

In the next section, you will be able to select the timing of when updates are delivered to your different rings. For security reasons, I strongly suggest you lower the defaults, but before you modify them, make sure you understand the basic concepts:

**Client Deferrals**: Amount of days after a cumulative update is released, will be offered to the device.

Example: The Client Deferrals is set to 10. Autopatch will then wait 10 days before offering the update to the device

**Deadline**: Amount of days before the update has to be installed.

Example: Deadline is set to 3. It will wait up to 3 days before automatically installing the update.

**Grace**: This is often misunderstood. This is the amount of days for the user to perform the reboot. The user will be presented with a number of notifications when the update has been installed and is now in a grace period. The grace period is counted ind amount of days the device is active, so keep that in mind when setting the grace period.

Example: Grace period is set to 3 days. A device has already been offered and installed the update, and is now in the grace period. 2 out of the 3 days passes in the grace period, but now it's Friday and the user shuts down the device. The user continues work on Monday and powers on the device. The user now has 1 day left to perform the reboot.

Adjusting these timers to fit your organizations need and your threat level is extremely important. If you are in the defense industry, security sector or similar, you will want to keep these as low as possible, but still high enough in the later rings to be prepared for potential noise early on. The default values will be ok for most organizations, but if you can get away with a lower deferral number, and just raising the grace period that is usually a good choice. A lot of users will perform the reboot early on. So by keeping the Deferral period low, but the grace period high, you will at lesat give the user the choice to reboot earlier. There's pros and cons.

When you are done, click next. Apply any scope tags if relevant, and then click next and finish.


## Using Autopatch rings for your own benefit

Once you are setup, autopatch will start registering your devices that is specified in the Entra Group you assigned it to. Almost immediately after creating the autopatch group, you can find an Entra Group for all the rings in your autopatch

![Autopatch](/assets/images/2026-06-06-AbusingAutopatch-RingSystem/Autopatch-5.png?raw=true "Getting started with autopatch")

And that's super valuable whenever you need to rollout a new policy, app, script etc. - You can now re-utilize those same rings for your own benefit.

>NoTE: If you decide to create multiple autopatch groups, they will all be consolidated under the following groups: Modern Workplace Devices-Windows Autopatch-Test, Modern Workplace Devices-Windows Autopatch-First, Modern Workplace Devices-Windows Autopatch-Fast and Modern Workplace Devices-Windows Autopatch-Broad - These entra groups can be used as well, but the split of divices is very unevent when jumping between first and fast.

## The end

That's all for now, I hope you found it useful.

Remember to also craft a multi-phase feature update rollout, to start rolling out your preferred feature update rollout. This can be done in phases, and it's pretty straight forward.

![Autopatch](/assets/images/2026-06-06-AbusingAutopatch-RingSystem/Multiphase.png?raw=true "Getting started with autopatch")

