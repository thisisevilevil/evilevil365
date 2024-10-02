---
title: "Windows 11 24H2 - Notable things for IT Admins"
date: 2024-09-27
categories:
  - Windows 11
tags:
  - Windows 11 24H2
  - Windows 11 Feature Update
  - Windows 11 Cumulative Update
  - 7zip
  - WinRAR
---

I got my hands on a new Dell XPS 13 Qualcomm based device back in late July, and I have been following the buzz around Windows 11 24H2 in the media for the past few months and boy does it seem like it will be a proper feature update this time. Based on what I know and what I've seen published, Windows 11 22H2 and 23H2 is going to pale in comparison.

And why did I mention that I got a Dell XPS 13 Qualcomm based device? Well the new Qualcomm based devices comes with some Copilot features, I.E: The copilot button on the keyboard but more important it also includes a new kid on the block: The NPU! So to proper utilize those new features on a new shiny device, the OEMs has been shipping Windows 11 24H2 way ahead of schedule, even before it has been released to the public, as that feature update has AI written all over it..

Let's get into the fun stuff already :)

## Built-in 7zip support

It's hard to believe after all of these years that Windows will now feature built-in support for 7zip.. gone are the days where we need to push 7zip to our users.

![CompressFile](/assets/images/2024-09-27-Win11_24H2_NotableThings/CompressTo_1.png?raw=true "Compress File - 24H2")
![CompressFile](/assets/images/2024-09-27-Win11_24H2_NotableThings/CompressTo_2.png?raw=true "Compress File - 24H2")

## Massive Improvements to Cumulative Update delivery

The way cumulatives updates will be delivered to Windows 11 24H2 will see a massive improvement in the shape of much smaller and incremental patches based on the new "Checkpoint cumulative updates" feature [announced by Microsoft back in July 2024](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/introducing-windows-11-checkpoint-cumulative-updates/ba-p/4182552). TL;DR: You will save a bunch of bandwidth, CPU Cycles and HDD Space :)

>Note: Did you know that WSUS is being deprecated? Are you still stuck running WSUS in your organization, I recommend you give [this a read as well](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/windows-server-update-services-wsus-deprecation/ba-p/4250436)

## Microsoft Recall: A CoPilot+ feature

As you can probably guess, Windows 11 24H2 is going to feature some new CoPilot-based features. [Windows Recall](https://support.microsoft.com/en-us/windows/retrace-your-steps-with-recall-aa03f8a0-a78b-4b3e-b0a1-2eb8ac48701c) Definitely had it's round in the media after some security researches found that this feature was working, well.. less than ideal, but Microsoft has since then made modifications and also made sure it's not enabled by default on new devices unless the user opts in. All of these new features will also be able to make use of the new NPU in Copilot+ devices. More information for IT Admins can be found in [this article from Microsoft](https://learn.microsoft.com/en-us/windows/client-management/manage-recall). Expect this will change a lot during the next 2-3 months.

**UPDATE 29/09/2024**: It seems right after my blog post was released, Microsoft also released an update on the security and privacy architecture on Recall. You can find it [here](https://blogs.windows.com/windowsexperience/2024/09/27/update-on-recall-security-and-privacy-architecture/).

![NPU](/assets/images/2024-09-27-Win11_24H2_NotableThings/Arm_NPU_TaskManager.png?raw=true "NPU Task Manager")

## Other notable things

### WMIC

Our beloved WMIC is no more, and is disabled by default in the operating system, as it has been deprecated. Get-WmiObject should also get the axe soon, it was superseeded by Get-CimInstance all the way back with the release of PowerShell 3.0. I know at least a few of my PowerShell scripts needs some re-work, it will probably take me a while.. oh dear.

In the meantime, if you still need it you can still manually enable it with the following command: `DISM /Online /Add-Capability /CapabilityName:WMIC~~~~​`

![WMIC](/assets/images/2024-09-27-Win11_24H2_NotableThings/WMIC_Deprecated.png?raw=true "WMIC Deprecated")

>Note: VBScript support is getting the same treatment, albeit there is still time to migrate to different solutions. See [this article for more details](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/vbscript-deprecation-timelines-and-next-steps/ba-p/4148301)

### Systemreset

I didn't see this one coming, but at least on my Dell XPS device this command is no longer working. I didn't use this a whole lot, but it was handy if a device didn't want to receive a wipe/autopilot reset, and it was seemingly stuck. We could manually have the user initiate the factory reset by using systemreset from command prompt. Oh well.. I guess we will use the GUI instead :)

![Systemreset](/assets/images/2024-09-27-Win11_24H2_NotableThings/systemreset_Missing.png?raw=true "Systemreset command")

### SENSE client is missing from early 24H2 OEM Builds

This was actually the first thing I noticed back in late July, and I wanted to write a blog post about it but I had too many public speaker engagements + a holiday coming up, so I didn't get the time. But I did report it to Microsoft and since then there has been a bunch of blog posts written about it as well.

TL;DR: In the process of OEM's frantically releasing Windows 11 24H2 for CoPilot+ devices, if you purchased a CoPilot+ device with a Pro version of Windows, during the switch from Home edition to Pro Edition, the OEMs did not include the SENSE Client, which is not included by default in Home Edition. This is relevant for customers using Microsoft Defender for Endpoint.
Upon enrolling my Dell XPS 13 in my test tenant, I noticed my Defender onboarding profile from Intune evaluated as "Not applicable" towards my device. This usually means a pre-req has not been met of some sort, and after some poking around that's where I figured out the SENSE client was missing.

I do not expect we will see this issues once Windows 11 24H2 becomes officially available, and If you are feature updating from 23H2 to 24H2 you have nothing to worry about. This was just a fun little omission of sorts that has taken its rounds in various forums and blog posts already. :) If you want to enable it manually in the interim, simply use the following command: `DISM /online /Add-Capability /CapabilityName:Microsoft.Windows.Sense.Client~~~~`

### Cumulative Updates as part of OOBE/Autopilot

I was not sure to feel happy or depressed when I saw this feature, upon enrolling my Dell XPS Devices. Obviously we want our devices to be 100% patched once they are enrolled, but the reason I have advised my customers against baking their own cumulative update process during OOBE is because it can be extremely time consuming to go through OOBE depending on network connection and how far you are behind from a patch perspective. I'm expecting the new Windows 11 Checkpoint feature will greatly enhance this experience, but as some of you probably know [Microsoft has decided to roll this feature back due to community feedback](https://techcommunity.microsoft.com/t5/intune-customer-success/important-changes-to-the-windows-enrollment-experience-coming/ba-p/4246689).

In my opinion, if Microsoft is to be successful rolling this out, I would hope it can be enabled/disabled as part of the ESP assigned to device/user so the IT Admins can do their own due diligence in regards to testing and rolling this out. In the mean time, I recommend you also take a look at having your OEM installing the latest cumulative update before they ship the device. Dell for instance has an offering called ["Ready Image"](https://www.dell.com/en-us/lp/dt/imaging), where you pay a small amount of $ extra pr. device and you get a clean + fully updated device upon delivery to you.

### CoPilot app will be replaced by M365 App

For enterprise customers, the Copilot app will disappear and be replaced by the Microsoft 365 app, where copilot can be pinned, using settings from the M365 admin center. The M365 app will allow you to access Microsoft Copilot and Microsoft 365 Copilot (M365 Copilot only if you have a M365 Copilot license). Both will be protected by Enterprise Data Protection (EDP), this is in place of Commercial Data Protection (CDP) if you have signed in with an Entra ID account. More information can be found in [this article from Microsoft](https://learn.microsoft.com/en-us/windows/client-management/manage-windows-copilot#enhanced-data-protection-with-enterprise-data-protection)

![CopilotCDP](/assets/images/2024-09-27-Win11_24H2_NotableThings/Copilot_CDP.png?raw=true "Copilot CDP")

## That's it for now

There is no official date of the release of Windows 11 24H2 yet, but my guess is mid or late October. If you haven't yet started your journey from Windows 10 to Windows 11 now is a better time than never :)

**UPDATE, 2nd of October 2024**: Looks like I was only off by half a month. Windows 24H2 has officially been released.

**Windows 24H2 and related announcements**
* Windows Microsoft Blog (Pavan Davuluri) - [Blog](https://blogs.windows.com/windowsexperience/?p=179099)
* Windows Experience Blog (John Cable) - [Blog](https://blogs.windows.com/windowsexperience/?p=179067)
* Windows IT Pro blog (Harjit Dhaliwal) -  [Blog](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/windows-11-version-24h2-what-s-new-for-it-pros/ba-p/4259108)
* KB tracking Copilot+ PC feature availability - [KB](https://support.microsoft.com/help/5045646)
* Windows 11 IoT Enterprise  - [IoT](https://learn.microsoft.com/en-us/windows/iot/iot-enterprise/whats-new/Windows-11-IoT-Enterprise-24H2)
* Support Lifecycle - [Lifecycle](https://learn.microsoft.com/en-us/lifecycle/products/?terms=windows%2011)
* Security Baseline - [Blog](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/windows-11-version-24h2-security-baseline/ba-p/4252801)
* Copilot Microsoft Blog – Blog[https://blogs.microsoft.com/blog/2024/10/01/an-ai-companion-for-everyone/](https://blogs.microsoft.com/blog/2024/10/01/an-ai-companion-for-everyone/)
* Microsoft October 2024 News - [Link](https://news.microsoft.com/microsoft-october-2024-news/)
* Message Center Announcement - [Link](https://learn.microsoft.com/en-us/windows/release-health/windows-message-center#3403)
 
* Recall Preview - [Blog](https://blogs.windows.com/windowsexperience/2024/09/27/update-on-recall-security-and-privacy-architecture/)
* AVD Learn.com - [Details](https://learn.microsoft.com/en-us/azure/virtual-desktop/windows-11-language-packs)
* Download Administrative Templates - [Download](https://www.microsoft.com/en-us/download/details.aspx?id=106254)
* Download GPO Setting Reference Spreadsheet - [Download](https://www.microsoft.com/en-us/download/details.aspx?id=106255)
* Update History - [Support](https://support.microsoft.com/en-us/topic/windows-11-version-24h2-update-history-0929c747-1815-4543-8461-0160d16f15e5)
* How to get it - [video](https://www.youtube.com/watch?v=hhqzKNXt3C8)
* What’s new for Consumers - [Support](https://support.microsoft.com/en-us/windows/inside-this-update-93c5c27c-f96e-43c2-a08e-5812d92f220d)
* What’s new for Commercial -  [Learn](https://learn.microsoft.com/en-us/windows/whats-new/whats-new-windows-11-version-24h2)
