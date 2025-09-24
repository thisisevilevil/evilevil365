---
title: "Autopilot, TAP, and the Reboot Problem"
date: 2025-09-24
categories:
  - Intune
tags:
  - Temporary Access Pass
  - TAP
  - Autopilot with TAP
  - MFA
---

Temporary Access Pass (TAP) has been around for a while, but when I talk to customers, it’s often not on their radar. I’ve recently helped several customers use TAP for new hires so they can enroll their Autopilot devices without a password.

There are plenty of articles that explain what TAP is and its use cases. Here are the [official docs from Microsoft](https://learn.microsoft.com/en-us/entra/identity/authentication/howto-authentication-temporary-access-pass).

You’ll also find many blog posts about TAP. Some are great, while others lack detail or contain outdated information. TAP and Autopilot enrollments has evolved in the past year, which is why I wanted to share an updated perspective. I’ve also spoken with Microsoft internally about the current user experience.

So what’s the fuss about?

## Autopilot enrollment and (un)expected reboots

This has been known in the community for years, but here’s a quick recap:

* During Autopilot enrollment, if a reboot occurs, the cached user token is lost. This forces the user to sign in again, often with additional MFA prompts/Sign-in, before they can set up Windows Hello for Business (WHfB).
* Common policies that cause reboots include Windows Update, Security Baseline, Device Control policies, and AppLocker. Rudy Ooms has a great post explaining how to pinpoint which policies triggered a reboot—[read it here](https://patchmypc.com/blog/autopilot-unexpected-reboot-what-really-triggers-a-device-restart-and-how-to-fix-it/).
* A known workaround is assigning reboot-causing apps or policies to user groups instead of device groups. For apps, you can also disable mandatory reboots.

This helps, but avoiding reboots entirely has side effects:

1. **Device Health Attestation (DHA):** Requires a reboot to check compliance items like BitLocker, Secure Boot, and Code Integrity. Without it, the device shows as non-compliant.  
   ![Compliance](/assets/images/2025-09-26-TAP-And-Autopilot/DHA-Bitlocker.png?raw=true "Compliance Policy DHA")
2. **Security features:** Device Guard and Virtualization-Based Security (VBS) need a reboot to activate. These are included by default in the Security Baseline.
3. **User-based targeting:** Assigning to users instead of devices can cause apps and policies to follow users onto shared or secondary devices, which isn’t always desirable.

## Enrolling an Autopilot device with TAP – with reboots

Here’s where things get messy. If a TAP is used and the Autopilot process includes reboots, some odd behavior appears.

The flow looks like this:  
A) User starts Autopilot enrollment with TAP  
B) Device reboots  
C) After reboot, the login screen shows "Other user" with a "Sign in" button. The user must press the button twice before the TAP prompt (web sign-in) appears. Alternatively, they can use **Sign-in options → globe**.

Some blogs and videos show this as normal. But it’s not—it’s poor user experience. This should be treated as a bug. Let’s call it **Problem #1**.  
![OtherUser](/assets/images/2025-09-26-TAP-And-Autopilot/OtherUser-1.png?raw=true "Other User - Sign in screen")  
![OtherUser](/assets/images/2025-09-26-TAP-And-Autopilot/OtherUser-2.png?raw=true "Other User - Sign in screen")

D) After enrollment finishes, the user sets up Windows Hello. Problem solved? Not really. But the user is now on the desktop.  
E) When the device is locked, after the user is on the desktop, the same issue from step C reappears. Clicking the user/password button shows "Username" and "PIN" fields.

The user can unlock the device either by using TAP again or by entering UPN + PIN from Windows Hello enrollment. This is **Problem #2**.  
![OtherUser](/assets/images/2025-09-26-TAP-And-Autopilot/OtherUser-LockScreen-1.png?raw=true "Other User - Lockscreen")  
![OtherUser](/assets/images/2025-09-26-TAP-And-Autopilot/OtherUser-LockScreen-UserPass.png?raw=true "Other User - Lockscreen")

I raised this with Microsoft. They were aware of the workaround (assigning reboot-causing apps/policies to users), but advised against relying on it since reboots can never be fully eliminated. Future platform changes could introduce new reboots at any point.  

What surprised me was their explanation of **Problem #2**: Windows Hello enrollment hasn’t actually completed in this flow, which explains the odd sign-in screen after device has been lock.

Other experts I've communicated with, otherwise often deal with this by targeting reboot-causing apps/policies to users. For DHA compliance, they add a grace period and schedule a forced reboot later.

## Alternative workaround: To ESP or not to ESP

One customer I work with is reluctant to change their targeting strategy in Intune. They want to keep all core policies and core apps assigned to devices. They’ve also logged a support ticket with Microsoft, but as I told them, this may take months—and Microsoft may not even classify it as a bug.

So is there another short-term option/workaround?

**Turn off the ESP.**

![NoESP](/assets/images/2025-09-26-TAP-And-Autopilot/NoESP.png?raw=true "Turn ESP Off")

This feels counterintuitive. The Enrollment Status Page (ESP) is designed to make sure policies and apps are in place before the user reaches the desktop. But if you disable ESP, here’s what happens:  
a) User signs in with TAP  
b) Within 30–60 seconds, they’re taken to WHfB enrollment  
c) After WHfB enrollment, they land directly on the desktop—without Problems #1 and #2.

From the user’s perspective, this is fantastic. They can start working almost immediately.  

From IT and Security’s perspective, not so much—because no apps or policies are there yet.

**Applications:** Critical apps can be installed via PowerShell (Intune platform scripts). These run before Win32 or LoB apps. It’s not elegant, but it works reliably.  

**Policies:** In testing, most policies applied within 1–2 minutes after hitting the desktop. The only issue is users who open Edge too soon will get extra sign-in prompts.  

**Compliance and security:** For DHA checks like BitLocker, you’ll need a reboot later. To force a reboot at some point, we have several options, which includes:  
a) Marking an app in Intune with **“Intune will force a mandatory reboot.”** Just remember to configure the restart grace period so users get a warning instead of an abrupt restart.  
   ![Appassignment](/assets/images/2025-09-26-TAP-And-Autopilot/AppAssignment-1.png?raw=true "App assignment: Mandatory Reboot")  
   ![Appassignment](/assets/images/2025-09-26-TAP-And-Autopilot/AppAssignment-2.png?raw=true "App assignment: Restart Grace Period")

b) Using a PowerShell script (run as current user) that prompts the user to reboot within 10 minutes. If they click “No,” the final reboot won’t run. This is just a PoC, to provide some inspiration.
 [here](https://github.com/thisisevilevil/IntunePublic/blob/main/PowerShell%20Scripts/Prompt-UserReboot.ps1).

 Can be assigned as a platform script in current-user context like so:
 ![Appassignment](/assets/images/2025-09-26-TAP-And-Autopilot/FinishSetup-UserPrompt-Assignment.png?raw=true "Platform script to prompt for reboot")

 It should look like this for the end user:
 ![Appassignment](/assets/images/2025-09-26-TAP-And-Autopilot/FinishSetup-UserPrompt-Reboot.png?raw=true "Platform script to prompt for reboot")

Of course I'm otherwise sure the community can figure or many different ways to nudge the user to perform a reboot

## Final words

Reboots during Autopilot have always been a pain point, but we’ve learned to work around them—either by assigning policies/apps to users or by documenting the behavior in onboarding guides so users aren’t confused by extra sign-ins.  

The bigger issue today is how TAP + Web Sign-in behaves. That flow really needs attention.  

Disabling ESP is a drastic workaround and won’t suit everyone—for example, if you use third-party AV or have many app dependencies. I usually recommend keeping ESP as light as possible, but sometimes you can’t avoid loading more into it.

I hope this helped. Have an awesome day :)
