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

There are also a lot of blog posts about it, some of them contains - in my opinion - lack of information or misleading information. Perhaps because this has evolved a lot during the past year or so, hence this blog post. I have also spoken with someone internally in Microsoft about the current user experience.

So what is the current fuzz about?

## Autopilot enrollment and (un)expected reboots

This fact is already well known in the community, but in case you missed out here is a quick recap:

* When initiating an autopilot enrollment, if there is any reboot in enrollment process, the persistent/cache token is lost. This results in additional login prompts + MFA Prompts for the end-user
* The common policies to look for is Windows Update Policies, Security Baseline and AppLocker. There is a great blog post by Rudy Ooms that explains how you can pinpoint which policies assigned to a device that caused a reboot. You can find it [here](https://patchmypc.com/blog/autopilot-unexpected-reboot-what-really-triggers-a-device-restart-and-how-to-fix-it/)
* A known workaround is to assign any policies (or Apps) causing the reboot to user groups rather than device groups.

However, using the workaround to completely eliminate the reboot can have unwanted side-effects, such as the following:

1) Device Health Attestation (DHA) requires a reboot. This is pertinent where compliance policies checking for Bitlocker, Secure Boot or Code Integrity is in place. If the reboot is not performed, it will be unable to measure compliance = Not Compliant.
![Compliance](/assets/images/2025-09-26-TAP-And-Autopilot/DHA-Bitlocker.png?raw=true "Compliance Policy DHA")
2) Device Control Policies like Device Guard and Virtualization-Based Security (VBS) requires a reboot to activate. If you are using security baseline, they will deploy by default

## Enrolling Autopilot device with a TAP - With Reboots

If you enroll an autopilot device with a TAP, when there is enforced reboots during the autopilot process, this will have interesting side effects. But I just need to highlight this is a seperate issue altogether, but in opinion it's totally related. We can call it a bug, problem or expected behaviour, I won't decide the terminology used, I'm sure someone at Microsoft has a better word for it.

But basically what will happen is the following:
a) User starts autopilot enrollment with a TAP
b) Device reboots during the autopilot process
c) This is where it gets interesting: You now get to a login screen where you can press "Other user" and below that you can press "Sign in options". If you have implemented the Web-Sign in policy, the user can then press "Sign in options" and then press the globe to activate web sign in. This will allow the user to sign in with the TAP once more to complete the enrollment.

There are blog posts and youtube videos on the internet showing this flow as it's perfectly normal, like it's supposed to work this way. But why? It's a horrible user experience. Because this should - in my opinion - be flagged as a bug. Let's call this problem #1
d) Once the enrollment is finished, the user can finally setup Windows Hello. And then all of our problems are resolved, right? No. Wrong.
e) When the user locks the screen, they will then again be faced with the same screen as in C). But here it gets even more interesting. If the user/password button is pressed, it will say "Username" and "PIN". Wonky, right?

The user can unlock his/her device by providing the TAP again, or by entering the UPN + PIN they used to enroll for windows hello. And that's problem #2

I spoke to someone from Microsoft about this problem, and he was well aware of the "workaround" to assign policies and apps to users instead, to prevent reboots. He generally recommended against it, as we can never guarantee to completely prevent the reboots during the autopilot process. Microsoft might make changes on their end that could cause a reboot somewhere in the process. Also we spoke about the user experience in this flow, and what happens in what I described in C) and E) surprised me a bit. When the user enrolls for windows hello in this scenario, the windows hello provisioning/enrollment has actually not completed. That is why we are seeing the wonky sign-in screen in E).