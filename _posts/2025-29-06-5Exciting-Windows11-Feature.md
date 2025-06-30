---
title: "5 exciting features coming in Windows 11"
date: 2025-06-29
categories:
  - Windows11
tags:
  - Windows 11 new features
  - Windows 11 25H2
  - Local Administrator protection
  - Quick Machine Recovery (QMR)
  - PinGeneration
  - PC Migration
 
---

In the not so distant future, we will get some new and awesome features in Windows 11, as always. Here are some of them I find exciting.

![NewWindowsfeatures](/assets/images/2025-06-29-5Windows11-Features-Coming/Thumbnail.png?raw=true "New Windows Features thumbnail")

## Quick Machine Recovery (QMR)

**Quick Machine Recovery (QMR)** aims to reduce downtime when devices encounter critical issues. Instead of lengthy reimaging or rebuild processes, QMR allows devices to quickly recover to a clean and ready state — often within minutes. This is especially valuable for IT teams managing large device fleets or remote workers. What's even cooler is that Microsoft is looking to build automatic remediation for common OS Boot failures, that makes it easy to guide end-users to fix common issues that could cause a No boot scenario. It's still in preview, but this technology looks very promising.

Did someone say Crowdstrike? Also, a lot of companies are paying extra for Intel vPro devices, but very few is actually taking advantage of [Intel AMT](https://www.intel.com/content/www/us/en/developer/articles/guide/getting-started-with-active-management-technology.html)- why is that? Probably best left for a different blog post. 

Rest assured, I think it's great and sorely needed that Microsoft is entering this space.

👉 [Learn more about Quick Machine Recovery](https://techcommunity.microsoft.com/blog/windows-itpro-blog/get-started-with-quick-machine-recovery-in-windows/4398487)

## Local Administrator Protection

Local administrator accounts have long been a target for attackers. **Local Administrator Protection** introduces built-in controls that help safeguard these accounts, reducing the risk of privilege escalation and lateral movement during attacks. This feature integrates with existing identity and access management strategies to provide a stronger security posture.

👉 [See how Microsoft is protecting local admin accounts](https://blogs.windows.com/windowsdeveloper/2025/05/19/enhance-your-application-security-with-administrator-protection/)

## A New PC Migration Experience

Migrating to a new Windows 11 device is about to become far simpler. The **New PC Migration experience** helps users transfer settings, apps, and data with minimal friction, reducing the setup time and confusion that often comes with getting a new PC. This feature will integrate with Microsoft cloud services to offer a seamless handoff between old and new devices.

👉 [Explore Microsoft’s vision for easier PC migrations](https://blogs.windows.com/windows-insider/2025/06/02/announcing-windows-11-insider-preview-build-26200-5622-dev-channel/)

## Smarter Start Menu Pins with `PinGeneration="1"`

Windows 11 is introducing a **one-time pinned apps experience** for the Start menu. With `PinGeneration="1"` you will be able to make pinned apps, unpinnable by the end-user, allowing the user to freely customize their own taskbar, even though IT deployed a taskbar policy. For those of you that has been in the game for a while, you probably also remember the plethora of tools that could pin apps to the taskbar, but that suddenly stopped working after a windows udpate. Gone are the days where we need to rely on PowerShell wizardry to apply a one-time configuration to the taskbar.

👉 [More details about that here](https://learn.microsoft.com/en-us/windows/configuration/taskbar/pinned-apps?tabs=intune&pivots=windows-11#pingeneration)

## New Copilot + Search Enhancements

Microsoft is re-inventing the **Copilot** experience in Windows 11, making it even more powerful by integrating it deeper into the Search functionality. This means faster, smarter results — whether you’re querying your PC, files, settings, or the web. Copilot is becoming a true AI assistant that helps you get things done more intuitively. Of course they have also increased the security of Windows Recall that made its rounds through the media some time ago. For starters, it's now no longer enabled by default, and the Recall bits are protected by Windows Hello and is now encrypted.

👉 [Meet Windows Copilot](https://blogs.windows.com/windows-insider/2025/06/23/announcing-windows-11-insider-preview-build-26120-4452-beta-channel/)

## Honorary mention

There is a screenshot of a new policy making the rounds on social media, where we can enable a policy to easily remove built-in apps in Windows. If I had a nickel for each Debloater script there exist online, well.. you know what I mean. This is still only on the rumor mill, so take this with a grain of salt for now.

I still know some companies are stuck running autopilot but gets devices delivered with consumer-grade windows images, where software like Mcafee and other 3rd party apps are pre-installed. I don't think this policy will automatically remove 3rd party software like Mcafee and other junk, but it's a step in the right direction.

Either way, If Microsoft delivers on this policy, then Microsoft is truly delivering on a request that's been out there for years, almost ever since Windows 10 was released.

![RemoveBuiltInApps](/assets/images/2025-06-29-5Windows11-Features-Coming/ScreenshotPolicy.png?raw=true "New Policy / GPO")

## Final Thoughts

These features reflect Microsoft’s continued investment in security, manageability, and user experience. Whether you’re an IT admin preparing for deployment or a user eager for smarter tools, Windows 11’s future looks promising. If you ask me what is my favourite upcoming feature, I'd probably have to pick between QMR and the new experience for creating a start menu / pinned taskbar.

How many of these will be present in Windows 25H2 that's due to be released later this year, I don't know. For all intents and purposes, it looks like Windows 11 25H2 is a minor update that can be enabled through an enablement package if you are already running Windows 11 24H2. I guess we will have to wait and see :)
