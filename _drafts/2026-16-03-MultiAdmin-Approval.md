---
title: "Getting started with Multi-Admin Approval in Intune"
date: 2026-04-07
categories:
  - Intune
tags:
  - Intune
  - Security
  - Security Best Practices
  - Process
---

Recently there was a major hack: The Stryker incident. You can read more about it [here](https://www.stryker.com/us/en/about/news/2026/a-message-to-our-customers-03-2026.html).

The TL;DR version:

* Mass-wipe commands were sent using Microsoft Intune to all windows-based devices
* Servers were also impacted - But not using Intune
* In total, around 200.000 devices, including servers, laptops, desktops and mobile devices were affected

A compromised admin account (presumably a Global Admin, but we don't know yet), was the culprit. During the past many years, there's been a strong push for reducing the amount of Global Admins, Conditional access policies and phish resistant MFA for these accounts. Hell, phish resistant MFA should be implemented for everyone, but Global Admins should definitely be the first in line.

One thing I noticed during the week when the event ensued, was a seemingly knee-jerk reaction to enable MAA because that would prevent incidents like these. But would it? Let's find out.

## Multi-Admin Approval

Multi-Admin Approval (MAA) is a (semi) new feature from Microsoft Intune that adds an extra approval layer for high-impact actions such as Device Wipe and Device Delete. Microsoft is also adding more device actions and policy types, for now it supports a few different policy types, such as application, running script or modifying a role. But the majority will probably look at enabling MAA for the following device actions: **Delete**, **Retire** and **Wipe**

When you create any of these MAA policy types, it is a **tenant-wide policy**. Scope tags are not supported, and scoping a MAA-Policy only to certain devices or users is also not supported for now. When you create the MAA policy, you will need to assign an EntraID Group of who can approve requests and then it will need to be approved by another admin before the policy actually is in effect. Note that even Global Admins can't even approve their own MAA requests.
Once this the request approved by another admin, you will have to navigate back to the MAA Pane and finally click "Complete request". The approach is the same for the other MAA actions, let's break it down.

![MAAPolicy](/assets/images/2026-04-07-MAA/CreateNewPolicy-1.png?raw=true "MAAPolicy Creation")
![MAAPolicy](/assets/images/2026-04-07-MAA/CreateNewPolicy-2.png?raw=true "MAAPolicy Creation")
![MAAPolicy](/assets/images/2026-04-07-MAA/CreateNewPolicy-3.png?raw=true "MAAPolicy Creation")
![MAAPolicy](/assets/images/2026-04-07-MAA/CreateNewPolicy-4.png?raw=true "MAAPolicy Creation")

### How it works in practice

To simpify this process, let's pretend we will have 2 admins. Admin 1 is Bob and Admin 2 is Rachel.

1) Bob initiates a device wipe towards a device from Intune. Since there is an MAA-Policy deployed, it will need to be approved by a 2nd admin.
2) Rachel approves the request from the MAA Requests pane in Intune
3) Bob then has to go to the same Multi-Admin Approval pane, click his request, and finally hit the "Complete request" button to actually initiate that wipe. Bob will have to do this within 3 days after the approval was given, otherwise it will expire.
If the action expires before clicking "complete request", Bob will have to send a new wipe and get it approved again.

It's important to underline that the action doesn't automatically complete once it's approved, the requesting admin will have to go to click that "Complete request" button to finalize the action, so essentially a 3-step process.

>NOTE: Based on learnings from the field, I've found it can take between 15-30 minutes for MAA to actually process the request. Example: If you press "complete request" to delete or wipe a device, it doesn't immediately process.

## What it protects against

I want to be clear when saying that this does NOT protect against a compromised Global Administrator (GA) account. It was frequently misconstrued on various posts I found on Linkedin and Reddit by just implementing MAA, you were protected against these mass-wipe attacks, but this is not the case, if the compromised account is a GA. When you obtain GA you have full administrative rights in the tenant (I.e: Domain administrator). The GA will be able to simply delete the MAA policy or just create separate admin accounts to approve the MAA requests. Alternatively, an overprivileged app registration can also tamper with these settings, which is an additional blindspot I find many companies not wary of.

However, and to be clear, MAA does protect against the following:

* Accidental device wipe/deletion
* The disgruntled employee scenario: On his last day at work, the individual could initiate mass-wipes before heading out the door.
* Singular compromised Intune Administrator or similar (Think Custom RBAC Roles in Intune)

## A few gotchas

1. MAA does not protect against a Compromised GA Account
2. If you are using tools to automate device deletions, they will stop working after implementing MAA.
3. Before just blindly implementing MAA, I would strongly go and review your processes for using actions like wipe and delete. They tie heavily into the device lifecycle process. It can heavily impact the daily operations in an IT ServiceDesk or an IT team that is managing devices in a multi-region company.
4. Currently there is no option to assign an MAA Policy to a given set of devices or limit to specific scope tags or groups, it's an all or nothing switch. If you decide to enable MAA in your tenant, be sure to communicate to all stakeholders in advance.
5. Processing an action like Delete or wipe is not immediate
6. If using Custom-RBAC Roles with Groups via PIM, sometimes the MAA pane doesn't show all devices - This can confuse support personnel. As a rule of thumb, wait 15 minutes after activating the group-based PIM role, and then reopen the Intune tab, to avoid this problem.
