---
title: "Abusing Autopatch's ring system for your own benefit"
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

To start configuring autopatch, you will need an Entra group, which will be used for onboarding. This can be a static or dynamic entragroup. Navigate to Tenant Administration > Autopatch groups and click "New autopatch group"
