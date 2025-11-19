---
title: "Nudge users to upgrade to Windows 11 with compliance policies"
date: 2025-11-13
categories:
  - Intune
tags:
  - Compliance Policy
  - Access management
  - conditional access
  - Security
---

Windows 10 is officially out of support, and unlesss you purchases ESU's (Extended Security Updates) licenses, you will need to get those devices upgraded to Windows 11 ASAP to stay secure. While you probably already have loads of automation mechanisms in place, such as Scripts, Remediations, Task sequences etc. to force the upgrade they don't always work as expected for a variety of reasons. There can be a plethora of reasons for a devices being unable to upgrade to windows 11. Something I have [briefly touched base on in a previous blog post](https://evil365.com/intune/troubleshooting/Troubleshoot-featureupdate-Setupdiag/).

IT departments can do a large variety of things from a technical perspective to push devices to Windows 11, but is there perhaps a different approach to deal with those stubborn devices that is still supported but for some reasons not moving to Windows 11, especially in large organizations with small IT teams?

The answer is of course: yes!

## Compliance policies

Compliance policies in Intune has been around forever. There can be a variety of use-cases for them, where one of them is to use compliance policies to block access to company resources unless you meet the demands of the compliance policy. Blocking access to company resources, requires crafting a Conditional Access policy to actually enforce the blocking. If you don't have conditional access blocking devices based on compliance (which you probably should..) there are still other uses cases for compliance policies in Intune.

A different use case of compliance policies is the email/notification functionality. We can configure compliance policies to send emails (Or even push notifications on phones) to end users based on what we measure compliance on. This is a powerful tool to nudge end-users.

Let's take a look at how we can nudge users to update to Windows 11 if they are still on a device that is running Windows 10.

### Configuring a compliance policy

You can navigate to Intune -> Devices -> Compliance Policies.

Before you create a new compliance policy, we need to create some message templates we can use in our compliance policy. In this compliance policy, we are going to give them 30 days to upgrade to Windows 11, otherwise the device is going to be marked as non-compliant.