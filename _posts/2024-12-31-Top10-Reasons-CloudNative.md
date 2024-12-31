---
title: "Top 10 reasons for Cloud Native"
date: 2024-12-31
categories:
  - Intune
  - Autopilot
tags:
  - Cloud Native
  - Autopilot
  - PxE Boot
  - OS Imaging
  - Old school deployment
  - Modern device management
---

2024 is coming to an end, and what a year it has been for Microsoft and Intune. Not only did we get a new version of autopilot but Intune and management of macOS devices also received a whole lot of love. We also got a whole heap of new features and lots of improvements in general.
For this post I want to give you the top 10 reasons for going Cloud native for your endpoints as I see them today, as it stands now, in the end of 2024. I know a lot of companies out there is contemplating taking the big plunge but is hesitant to push the button, perhaps for a variety of reasons. Hopefully this blog post will give you food for thought and give you some extra insights as to why it is such a good idea to be cloud native.

## 1. Supportability

Supporting your devices will be so much simpler when issues occur, since you won't have to worry about troubleshooting your old domain, i.e: group policies, or group policy changes not affecting your devices since they are not onprem or not on VPN. Also SCCM or other 3rd party systems is also out of the picture. This can be a huge time saver in case of troubleshooting any issues with the device.

Also a huge boon of completing this journey will be time saved troubleshooting mundane issues like sound issues, performance issues: It will be so much faster just asking the user to go to company portal -> Device -> Device actions and hit that reset button to factory reset the device and start over. On a new device, the process of resetting and re-enrolling the device can be done in less than 30 minutes! So rather spending hours of troubleshooting issues on singular devices, why not just reset the device and start over.

## 2. Single pane of glass

All stakeholders will see a massive improvement in regards to managing and reporting. Managing will be so much easier when you need to make adjustments, as you don't have to worry about where to make the change: SCCM, Group Policy or Intune (or whatever 3rd party system you are using). It will always be Intune. And from a reporting perspective, there is just 1 source of truth and you don't have to worry about things like SCCM Workloads not switched etc.

## 3. Increased performance

Since the device is not domain joined, co-managed or enrolled into some other MDM/Management system, the performance will increase, some times in such a drastic fashion you would be amazed of how big of a change it can make. Just remember to transition into that cloud native state on a windows device, the only supported way is to perform a factory reset of the device, and there is no way around it, if you want to stay supported.

## 4. No need to support PxE Boot or USB Drives

Going cloud native for windows usually means you also adopted autopilot. So you no longer need to support PxE boot and USB Drives which can be a huge pain for larger organizations that are also strict with their network and use 802.1x. Making a network change or setting up a new site can sometimes be a huge pain when you have to take into account that devices needs to be able to PxE boot to some SCCM or other 3rd party deployment system. While I know some of you out there knows that it can be easy to set up, in larger organizations that is usually siloed into smaller teams each managing their own subsection (I.e: Device team, network team, identity team) you all know the pain points here. Eliminating this bit alone can be such a huge timesaver, also not to mention that you can turn off all supporting OnPrem infrastructure like SCCM DPs on all sites. Lastly, you can tell everyone to can all of those USBs with WinPE Boot Images to escape PxE/WDS and OS Images that are usually severely outdated anyway.

## 5. No need to support imaging/driver pack management

I know a lot of old-time SCCM folks really like playing around with this, they dream about it at night (I know because I use to be one of them..), but this is so time consuming. I know some tools exists out there to make it a lot easier, but it doesn't change the fact this process is so bloated, old school and super unnecessary with the tools at our disposal today. If we are fully cloud native we will utilize autopilot to re-use the OEM Image out of the box, thus eliminating the need for manual imaging and driver pack management every time there is a new update or a new model of laptop/desktop we need to deploy.

## 6. Hiring IT Staff is suddenly much simpler

Hiring new people to manage your devices never got simpler, as you dont have to spend a long time teaching them about your +20 year old Active Directory and SCCM Infrastructure. I have seen this so many times, new-hires starts at company, gets completely lots in all the legacy group policies and after they got lost in the active directory they take a look at the SCCM infrastructure, with +10000 SCCM Collections and with the strangest SCCM Collection queries you can imagine. Eliminating the need to teach a new-hire about AD and SCCM and only about Intune makes it so much easier (and faster!) to onboard new people to your setup.

And for all of those reasons, if you are responsible for hiring new people for a position to manage devices, your job listing suddenly got so much shorter and simpler.

## 7. Recover quickly in case of failure

It's super easy to wipe a device from intune and start from scratch, and this can be done from any location, no need for onprem connectivity (unless you somehow were cornered into a HAADJ Autopilot solution but we don't talk about that here..).
In case there is any issues with a device, and we do not want to spend a long time troubleshooting, we can simply start he wipe or autopilot reset process to get the device freshly reinstalled. And for you SCCM Folks insisting on keeping the SCCM Server turned on, in case the device no longer boots to the OS: In case the device OEM image is FUBAR, OEMs like HP, Dell and Lenovo also have tools to cloud recover the OS Image, allowing us to completely reinstall the OS from the cloud thus completely eliminating the need for any PxE/Imaging infrastructure. You can also be sure Microsoft will bring something to the table in this regard very soon.

## 8. Streamlined setup

This is so overlooked in these journeys to going cloud-native, for big companies that are over 20 years old. Think of all the old group policies, OU Structure, SCCM Collections and all the legacy that has been piling up over the years that nobody dares to touch because nobody knows what half of it does, and the people that do usually left the company or got transferred to some other team internally in the organization. By building a new cloud native setup, your business essentially gets a fresh starts and also a renewed overview off what is required from a device to enable the business to thrive. In the process all stakeholders involved will get a good overview of what exactly is required and the small quirks that we sometimes have to implement to enable the business.

## 9. Saving $$$

Some of you are probably using some 3rd party MDM or system today to manage your devices. for macOS, I know a lot of you are probably using jamf. Some of you hardcore macOS folks, I know I can't convince all of you Intune management for macOS is even a thing, but also some of you are probably not the ones paying the bills either. If you can achieve 60-70% of what you can do in jamf by enrolling your macOS devices into Intune instead, some of those companies out there with just a small amount of macOS devices, you can really save big on those jamf licenses. With the newly [announced migration path from Microsoft](https://github.com/microsoft/shell-intune-samples/tree/master/macOS/Tools/Migration) it has never been easier.

The same goes for SCCM infrastructure and the personnel needed to manage and maintain it. Lastly of course, if you use some 3rd party MDM Solution you are probably paying for that today. By migrating from that over to Intune, of course you can really save some $$$ as everything you need is usually already part of your existing Microsoft 365 licensing you are using today.

## 10. Reduced attack surface

Last but definitely not least, you will find your overall attack surface has been reduced (sometimes even drastically reduced). By eliminating that Hybrid-joined and Co-managed state, the overall security of the device will be much higher, as the amount of attack vectors are reduced. Also personally when I help customers go cloud native, I also strongly recommend enabling the [Intune security baseline](https://learn.microsoft.com/en-us/mem/intune/protect/security-baselines) and [all ASR Rules](https://learn.microsoft.com/en-us/defender-endpoint/attack-surface-reduction-rules-reference) where possible. While this is easier said that done, if you built the proper processes around it for how to exclude singular devices, processes etc. when issues occur, this, accompanied by going cloud native, will elevate your windows endpoint security to the moon compared to what you have today if you are not using any of these components today.

## End of 2024

2024 has been such an interesting year for Intune and Microsoft on so many levels. [The Secure Future Initiative](https://www.microsoft.com/en-us/security/blog/2024/05/03/security-above-all-else-expanding-microsofts-secure-future-initiative/) is probably one of the major ones that affects us all more than you probably know. Also with the introduction of ["Device Preparation" (APv2)](https://techcommunity.microsoft.com/blog/microsoftendpointmanagerblog/windows-deployment-with-the-next-generation-of-windows-autopilot/4148169) this will be a very interesting change and addition to the whole autopilot ecosystem.

For Apple and macOS we also saw the addition of a groundbreaking new feature known as [Platform SSO (PSSO)](https://techcommunity.microsoft.com/blog/identity/platform-sso-for-macos-now-in-public-preview/4051574) which makes the end-user experience so much better. Ever since that feature got announced, I suddenly received a lot more macOS management/migration projects to Intune from customers that either wanted to migrate from jamf to Intune, or wasn't doing anything at all in regards to macOS management, so I really had to polish those macOS skills I didn't know I had to begin with.

I helped my first large-enterprise customer go cloud native all the way back in 2019, and now that I'm looking at the end of 2024 and peeking into 2025, my 2025 calendar is also fully booked for assisting one of my new customers going cloud native for 2025 whilst also migrating away from Capainstaller in the same process, so no SCCM for a change.

My new years resolution for 2025 will definitely be to get out and speak more at public events about going cloud native, to break it down a bit and make it digestible for some of you that is afraid to take the big cloud native plunge.

That's all for now folks, I hope you have a happy new year and I will see you all in 2025 :)

![HappyNewYear](/assets/images/2024-12-31-Top10-Reasons-Cloudnative/HappyNewYear.jpg?raw=true "Happy New Year")
