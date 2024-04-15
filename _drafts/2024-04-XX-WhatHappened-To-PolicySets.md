---
title: "What happened to Policy Sets and the rise of device filters"
date: DRAFT2024-04-24
categories:
  - Microsoft Intune
tags:
  - Policy Sets
  - Device Filters
  - Grouping, Targetting, Filtering
---

If you have been using Intune for a few years, I'm sure you at the very least have noticed the "Policy Sets" button in Intune. It's one of those features that's been in preview forever. You might also be one of those who actually use policy sets.
I used to leverage policy sets at my customers some 2-3 years ago, but I stopped doing it, and I'm now recommending to migrate away from Policy Sets. Why? Well let's first go through what Policy Sets is.

## What is Policy Sets
Policy Sets allow you to bundle policies and apps into 1 assignable "set" of policies. This is actually a great idea, because if you have a lot of profiles and apps, that you want to assign to a new group, it's an absolute click-a-thon. Policy Sets also gives you that nice overview of what exactly is assigned to an EntraID group. If you have a process where you say you will only assign stuff using Policy Sets, then you can be sure you have the full overview in the Policy Set. Super neat, right? Because today there is no way in the Intune or Entra UI where you can see what is assigned to a group, simply by looking at it. Well, Policy Sets to the rescue!

## So what is the problem?
Well, where do I begin. For starters, Policy Sets lacks support for using filters. But it also lacks support for things like Remediations, PowerShell scripts and Win32 apps. It has been like this for many years. So what does that tell us? This feature has most likely been abandonded and has been left to suffer a very slow and painful death!

But who knows, if it might be revived, but I doubt it.

## What is the alternative
You should migrate to using Device Filters. Device filters lives natively in Intune, and if you use Device Filter with All Devices/All Users, you will see a huge improvement in evaluation times! In fact, you will notice sometimes it's almost instant. But why is that?

Well, consider this when you assign things to an EntraID group. The EntraID Group lives outside intune, and is not native. Also consider the scenario where you assign to a dynamic EntraID group, if you have wondered why things are slower one day compared to the other, that is because the evaluation times of dynamic groups can differ. Sometimes there can be a [delay up to 24 hours!}(https://learn.microsoft.com/en-us/troubleshoot/azure/entra/entra-id/dir-dmns-obj/troubleshoot-dynamic-groups#members-are-not-added-or-removed-as-expected).

One other great thing you get with the device filters, is the "Associated Assignments" button. In that view, you can see 
