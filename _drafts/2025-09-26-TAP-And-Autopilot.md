---
title: "Temporary Access Pass and Autopilot"
date: 2025-09-25
categories:
  - Intune
tags:
  - Temporary Access Pass
  - TAP
  - Autopilot with TAP
  - MFA
---

Temporary Access Pass (TAP) has been here for a while, but when I talk to my customers about it, it's not on their radar at all. I've also had the pleasure of helping a few of my own customers implement TAP for new-hires so they can enroll their autopilot devices without using a password.

There are plenty of articles out there already that explains what TAP is and what the target use cases are. Here are the [official docs from Microsoft](https://learn.microsoft.com/en-us/entra/identity/authentication/howto-authentication-temporary-access-pass)

There are also a lot of blog posts about it. Some of them are really informative, but others contains - in my opinion - lack of information or misleading information. Perhaps because this has evolved a lot during the past year or so, hence this blog post. I have also spoken with someone internally in Microsoft about the current user experience.

So what is the current fuzz about?

## Autopilot enrollment and (un)expected reboots

This fact is already well known in the community for several years, but in case you missed out here is a quick recap:

* When initiating an autopilot enrollment, if there is any reboot in the enrollment process, the persistent/cached user token is lost during the reboot. This results in additional login prompts + MFA Prompts for the end-user before the user can enroll for WhfB (Windows Hello For Business).
* The common policies to look for causing reboots is Windows Update Policies, Security Baseline and AppLocker. There is a great blog post by Rudy Ooms that explains how you can pinpoint which policies assigned to a device that caused a reboot. You can find it [here](https://patchmypc.com/blog/autopilot-unexpected-reboot-what-really-triggers-a-device-restart-and-how-to-fix-it/)
* A known workaround is to assign any policies (or Apps) causing the reboot to user groups rather than device groups.

However, using this workaround to completely eliminate the reboot can have unwanted side-effects, such as the following:

1) Device Health Attestation (DHA) requires a reboot. This is pertinent where compliance policies checking for Bitlocker, Secure Boot or Code Integrity is in place. If the reboot is not performed, it will be unable to measure compliance = Not Compliant.
![Compliance](/assets/images/2025-09-26-TAP-And-Autopilot/DHA-Bitlocker.png?raw=true "Compliance Policy DHA")
2) Device Control Policies like Device Guard and Virtualization-Based Security (VBS) requires a reboot to activate. If you are using security baseline, they will deploy by default
3) By assigning your apps/policies to users, you might end up in a scenario where they follow them to shared devices which can be a biggie in some scenarios

## Enrolling Autopilot device with a TAP - With Reboots

If you enroll an autopilot device with a TAP, when there is enforced reboots during the autopilot process, this will have interesting side effects. But I just need to highlight this is a seperate issue altogether, but in opinion it's totally related. We can call it a bug, problem or expected behaviour, I won't decide the terminology used, I'm sure someone at Microsoft has a better word for it.

But basically what will happen is the following:
A) User starts autopilot enrollment with a TAP
B) Device reboots during the autopilot process
C) This is where it gets interesting: You now get to a login screen where it says "Other user" with a "Sign in" Button. The user will have to press the sign in button twice (not once) in order for the TAP Prompt (which is the web sign in policy in place), for it to appear. Alternatively, the user can press Sign in options and press the globe.

There are blog posts and youtube videos out there showing this flow as it's perfectly normal, like it's supposed to work this way. But why? It's a horrible user experience. Because this should - in my opinion - be flagged as a bug. Let's call this **Problem #1**
![OtherUser](/assets/images/2025-09-26-TAP-And-Autopilot/OtherUser-1.png?raw=true "Other User - Sign in screen")
![OtherUser](/assets/images/2025-09-26-TAP-And-Autopilot/OtherUser-2?raw=true "Other User - Sign in screen")

D) Once the enrollment is finished, the user can finally setup Windows Hello. And then all of our problems are resolved, right? No. Wrong.
E) When the user locks the screen, they will then again be faced with the same screen as in C). But here it gets even more interesting. If the user/password button is pressed, it will say "Username" and "PIN". Wonky, right?

The user can unlock his/her device by providing the TAP again, or by entering the UPN + PIN they used to enroll for windows hello. And that's **Problem #2**

![OtherUser](/assets/images/2025-09-26-TAP-And-Autopilot/OtherUser-LockScreen-1.png?raw=true "Other User - Lockscreen")
![OtherUser](/assets/images/2025-09-26-TAP-And-Autopilot/OtherUser-LockScreen-2.png?raw=true "Other User - Lockscreen")

I spoke to someone from Microsoft about this problem, and he was well aware of the "workaround" to assign policies and apps that causes reboots, to users instead, to prevent reboots during the autopilot process. He generally recommended against it as a solution to this problem, as we can never guarantee to completely prevent the reboots during the autopilot process. Microsoft might make changes on their end that could cause a reboot somewhere in the process in the near future, we don't really know. Also we spoke about the user experience in this flow, and what happens in what I described in C) and E) surprised me a bit. When the user enrolls for windows hello in this scenario, the windows hello provisioning/enrollment has actually not completed. That is why we are seeing the wonky sign-in screen in E).

However, when speaking to other experts in the community, it seems to be perfectly normal to workaround this problem by simply locating all policies and apps that causes a reboot, then assign to users rather than devices. For DHA Compliance checks, we can implement a grace period and a forced reboot at some point, to allow the device to perform the reboot within the specified time, allowing to become compliant within a certain time period. This seems to be the most common solution today.

## Alternative workaround: To ESP or not to ESP

I'm working with one customer whose very reluctant to change their grouping, targeting and filtering strategy in Intune to accomodate their TAP Rollout with Autopilot, so basically they want to retain all of their core policies and apps assigned to their devices. They have also logged a support ticket for the issues we have found during the TAP rollout, that has now spanned a couple of months. But as I have let them know, logging a support ticket and getting this "fixed" will probably take a while. It also requires Microsoft will acknowledge this is a problem in the first place, and is not "Working as designed".

So what can we do in the meantime?

Turn off the ESP!

This is extremely counterintuitive for someone who has been implementing autopilot for organizations for many years, because the ESP is vital to ensure all policies and company-assigned core apps is present and working on the device before the device hits the desktop. However, consider the alternative:
If the ESP is completely turned off, what will happen is the following:
a) User starts device provisioning using their TAP
b) Within 30-60 seconds the user goes directly to the WhfB enrollment screen
c) After WhfB enrollment the user is now on the desktop. The issues described before, Problem #1 and Problem #2, is no longer present

This user experience is super awesome, they will probably be over the moon. They can start working almost immediately. Everyone is happy, right?

Well not IT and Security as you probably figured. In this scenario they are not super happy, because no profiles/policies or apps is present on the device once the user hits the desktop.

**Applications:** As a workaround you can install any super-duper-required apps as a PowerShell script (Platform script in Intune). They process before any Win32 apps or LoB apps is evaluated. Not super slick, but it works consistenly.

**Profiles/policies:** During testing, we have found they start applying within 1-2 minutes after the user hits the desktop, so it's not neccessarily a biggie. However if the user launches Edge fairly quickly, they will be faced with a lot of prompts to sign in etc, which is not super great, not bad either.

**Compliance policies using DHA and Security Policies**: If you are using compliance policies with DHA, like a Bitlocker check you will need to restart the device at some point. There are multiple ways to guide the user to perform a reboot in this scenario like the following:
a) Set the following flag on a required app in Intune "Intune will force a mandatory reboot". Just remember to enable the Reboot Grace Period on the app assignment, otherwise the user will experience an abrupt reboot without any warning. Once the app deploys successfully, the user will be prompted to perform the reboot, also based on the timers you configured in your win32 app
b) I have created this PowerShell script that can be run in current-user context, that will guide the user to perform a reboot within 10 minutes. The user will get a popup asking them for a reboot after 10 minutes, and if they click No, it will not perform the final reboot. 

When I say I created it, Copilot did most of the heavy lifting actually, as it contains a lot of PowerShell forms stuff.

## Final words

Reboots during the autopilot process has been a common painpoint for many years, but we have kinda learned to either use the workaround by assigning policies/apps to users or alternatively we simply cater to this scenario in a user/onboarding guide for windows devices to avoid confusion in regards to having to authenticate twice. 

The workaround I described regarding turning off ESP completely might not work for everyone. For instance if you are using a 3rd party AV or you simply have a lot of apps/Dependencies you want to have installed before the user hits the desktop, then it's probably not for you. While I always encourage my customers to have a minimum amount of apps in the ESP, sometimes it cannot be avoided for various reasons.

I hope you found it useful, have an awesome day :)

