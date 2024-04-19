---
title: "What happened to Policy Sets and the rise of Device Filters"
date: 2024-04-19
categories:
  - Microsoft Intune
tags:
  - Policy Sets
  - Device Filters
  - Grouping, Targetting, Filtering
---

If you have been using Intune for a few years, I'm sure you at the very least have noticed the [Policy Sets](https://learn.microsoft.com/en-us/mem/intune/fundamentals/policy-sets) button in Intune. It's one of those features that's been in preview forever. You might also be one of those who actually use policy sets.
I used to leverage policy sets at my customers some 2-3 years ago, but I stopped doing it, and I'm now recommending to migrate away from Policy Sets. Why? Well let's first go through what Policy Sets is.

![PolicySets](/assets/images/2024-04-19-WhatHappened-To-PolicySets/PolicySets.png?raw=true "Policy Sets in Intune")

## What is Policy Sets
Policy Sets allow you to bundle policies and apps into 1 assignable "set" of policies. This is actually a great idea, because if you have a lot of profiles and apps, that you want to assign to a new group, it's an absolute click-a-thon. Policy Sets also gives you that nice overview of what exactly is assigned to an EntraID group. If you have a process where you say you will only assign stuff using Policy Sets, then you can be sure you have the full overview in the Policy Set. Super neat, right? Because today there is no way in the Intune or Entra UI where you can see what is assigned to a group, simply by looking at it. Well, Policy Sets to the rescue!

![PolicySets](/assets/images/2024-04-19-WhatHappened-To-PolicySets/PolicySets-2.png?raw=true "Policy Sets in Intune")

## So what is the problem?
Well, where do I begin. For starters, Policy Sets lacks support for using filters, which is definitely here to stay. But it also lacks support for things like Remediations, PowerShell scripts and Win32 apps. It has been like this for many years. Also, by the time settings catalog was introduced, I started seeing configuration profiles created as "Settings catalog" didn't always apply if it was applied through a policy set. 
So what does that tell us? This feature has most likely been abandoned and has been left to suffer a very slow and painful death! The whole idea of policy sets die when we can't use all assignable policies, apps in the policy set.


## What is the alternative: Device Filters!
You should migrate to using [Device Filters](https://learn.microsoft.com/en-us/mem/intune/fundamentals/filters). Device filters lives natively in Intune, and if you use Device Filter with All Devices/All Users, you will see a huge improvement in evaluation times! In fact, you will notice sometimes it's almost instant. But why is that?

Well, consider this when you assign things to an EntraID group. The EntraID Group lives outside intune, and is not native to intune. Also consider the scenario where you assign to a dynamic EntraID group, if you have wondered why things are slower one day compared to the other, that is because the evaluation times of dynamic groups can differ. Sometimes there can be a [delay up to 24 hours!}(https://learn.microsoft.com/en-us/troubleshoot/azure/entra/entra-id/dir-dmns-obj/troubleshoot-dynamic-groups#members-are-not-added-or-removed-as-expected). If you use All Devices or All Users they are referred to as "Virtual groups" that doesn't exist or relies on communication with EntraID, so they are super fast when it comes to evaluation. So the recommendation is to use Virtual Groups with device filters where possible.

![VirtualGroups](/assets/images/2024-04-19-WhatHappened-To-PolicySets/VirtualGroups-vs-EntraID.png?raw=true "VirtualGroups-vs-EntraID")

One other great thing you get with the device filters, is the "Associated Assignments" button. In that view, you can see exactly where the filter is used, which gives you that all-around view of where the filter is used. While it doesn't replace the need to take a collection of policies, apps and settings as 1 assignable object, you still get that overview of where the filter is assigned.

![AssociatedAssignments](/assets/images/2024-04-19-WhatHappened-To-PolicySets/AssociatedAssignments.png?raw=true "Associated Assignments")

## The end (Perhaps also for policy sets..)
I hope you learned something. If you haven't started using Device Filters in Intune yet, I highly recommend you get started now. Now is always the best time :)

To stay updated in the Groupting, Targetting and Filtering space with Intune, I recommend you give [this article a read](https://learn.microsoft.com/en-us/mem/intune/fundamentals/filters-performance-recommendations)
