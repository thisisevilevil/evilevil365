---
title: "Windows 11 24H2 Upgrade and the partitioning from hell"
date: 2025-06-16
categories:
  - Windows 11
tags:
  - Windows 11 24H2
  - Feature Update stuck
  - Recovery Partition not working
  - EFI Partition full
  - We couldn't update the system reserved partition
---

I have recently been engaged with a customer where some of their devices was failing to upgrade to Windows 11 24H2 with the error "We couldn't update the system reserved partition". Coincidentally, at the same time, they also reported that sometimes when they use "wipe" on some of their devices from Intune, they get the classic error "Could not find the recovery environment".

Initially I thought it was just the recovery partition that was too small. I have helped several customers over the years, with this particular issue where and ancient SCCM Task Sequence used for OSD only leaves 300MB for the recovery partition, that just won't fly today. For Windows 11 24H2, it seems the recovery partition recommendation has been raised yet again, and now it's recommended to set it to 1GB.

But in this case, it wasn't only the recovery partition that was the cause of this issue. It was the first time I've stumbled across it, but the customer said they had seen the error regarding the "reserved partition" issue, a while back. We eventually found the issue, and developed the fix, which was very simple in the end, but not very pretty at all.

![Thumbnail](/assets/images/2025-04-14-TheCase-OfTheMissing-GPU/ITAdmin_Hell.png?raw=true "Partitioning hell")

>DISCLAIMER: Please be careful when you start using the scripts mentioned in this blog post. Test in on a few test devices first, and roll it out slowly in waves/rings. I have tailor-made these scripts for my customers environments, there might be something in your environment that is different, that could break if you use these scripts.

## Fixing the recovery partition

The recovery partition issue is fixable in most scenarios, provided there is enough disk space available. I made a Proactive remediation to do just that a couple of years ago. But in this case, the customer had used Capainstaller to somehow shoehorn the recovery environment on the OS Disk rather than having it on a seperate dedicated recovery partition, during the deployment of their devices, and it was actually reporting as "Enabled" (Yeah, first time I've seen that as well..).
Having the recovery environment enabled on the OS partition is not supported, and can also cause issue with Bitlocker and using the system reset/wipe functionality from Intune. The recovery environment needs it's own dedicated partition.

The downside of this fix, is if there is devices in production that is low on disk space, they are now going to be 1GB lighter or the fix might not even work at all.

I recently adjusted my PowerShell script a bit to fix the recovery partition so it also searches for the condition where the recovery environment is actually enabled but on the OS partition. It's already available in my github, you can find it [here](https://github.com/thisisevilevil/IntunePublic/tree/main/Remediations/Recovery%20Partition)

Assign it like so:
-> Run in user context: No
-> Script signature check: No
-> Run in 64bit context: Yes

Frequency: I recommend just once or at least every 15 days. Do not set it to run hourly or daily.

### Breakdown of Detection script

**The detection script works like this:**

1) Check if the recovery environment is enabled using the `reagentc /info` command. If yes, it will continue to check if the recovery environment is enabled on the same partition as the OS Disk. If it detects this condition, it will continue to remediate, and be marked as "With issues"

2) If the recovery environment is disabled, it will continue to run the remediation part and will be marked as "With issues"

3) If the recovery environment is enabled, and it's not enabled on the OS Partition, no actions will be performed. The device will be marked as "Without issues"

### Breakdown of Remediation script

**The remediation script kicks off, if the detection script detects issue. It works like the following:**

1) Looks for the recovery wim file for your Operating System build under C:\Windows\System32\Recovery\WinRE.wim - If it's not present, it will need to download it from somewhere. I usually use blobstorage for the occassion, so if you find your devices doesn't the wim file locally on the device, you will need to re-host the corresponding .wim file for your specific OS Build on a fileshare, blob storage or similar.
Adjust the variables in the top of the script to link to the recovery .wim files for each of the OS Builds you support. To get this .wim, you will need to download the Windows ISOs, find the install.wim and open it using 7Zip - The recovery wim should be under .\Windows\System32\Recovery called "winre.wim"

2) Places a diskpart script under C:\windows\Logs\WinREFix (This part is really old school, but was the only way I could get it to consistently work). The diskpart script Selects Drive C, shrinks is by 1GB, then creates a new partition using the 1GB of free space and labes it WinRE, assigns it a temporary drive letter Q with some neccessary GPT Attributes

3) The script then creates a folder under the newly assigned Drive Q "Q:\Recovery\WindowsRE", copies the winre.wim file to the location, and attempts to assign and Re-enable the recovery environment in the newly created partition.

4) Then we run another diskpart script, that removes the temporary drive letter Q, but only after a 15 second pause. After some trial and error I found it's not supported to run diskpart scripts in quick succession, or else they will just fail.

5) In the end, it will do a final check if the recovery environment is enabled, which hopefully it is. If it's still not enabled for whatever reason, It will go through the logs that it leaves for 2 specific conditions that could prevent the OS Partition from being shrunk, and subsequently schedule a chkdsk to fix potential disk errors. (This bit only works for devices that is using English as the base language.. if your base language is different, this bit won't work, and it will simply skip it. Adjust where needed)

The last troubleshooting bit was left in for one of my customers where they had more than 10000 windows devices with this issue and after running this fix on them, they stil had a couple of hundred devices left, that was stubborn and the issue lingered. The chkdsk command in the script solved some of them, while asking users with devices that had very low disk space to free up some space also resolved some. But if there is some sort of underlying disk corruption issue, shrinking the drive might not be possible. Reinstalling the device or replacing it might be a better option, depending on the device age.

>NOTE: Make sure to adjust whatever tool you use for OSD (Operating System deployment), to create the correct partitioning scheme from the get-go, allowing at least 1GB disk space for the recovery environment.

## Fixing the EFI System Partition (ESP)

The fix for the EFI System Partition (ESP) simply involves mounting the ESP, manually freeing up some space, and then dismount it. I've been unable to locate how much space exactly is required on the ESP for Windows 11 24H2, but my research seems to point at 50MB, but I have no documentation to back it up. If you ask Copilot or ChatGPT they recommend partition sizes of at least 250MB, but it's sources is from community-backed sites like answers.microsoft.com and superuser.com.

You can find the script [here](insertlink).

Assign it like so:
-> Run in user context: No
-> Script signature check: No
-> Run in 64bit context: Yes

Frequency: I recommend just once or at least every 15 days.

Vendors like HP, Lenovo and Dell sometimes store BIOS or firmware files on the ESP to support capsule BIOS updates. But it seems some of them doesn't make it a habit of cleaning up after themselves, when they are finished. It's not super pretty having to go to a portion of your device fleet, mount the EFI Partition and move/delete some files to get a feature update working.

>NOTE: Like the recovery partition, remember to also adjust whatever tool you use for OSD, to create the system partition. Recommendation actually vary on how large it should be, but leaving at least 500MB for the EFI partition should be fine, but if you want to be future proof, whilst also safeguarding against any OEM's using it for staging BIOS/Firmware files, just make it around 1GB as well, if possible.

## Rounding up

Make sure to test, test and do more testing before you start rolling this out to any devices in production.

Given how many companies is still relying on performing old school OSD, I wish there was some official documentation and guidelines from Microsoft for how to create the correct partitioning schemes for Windows devices. It also seems like the goalpost moves with every new Windows 11 feature update, but at least I haven't been able to find any official docs about it yet, only community-driven content, which is rather strange, seeing how long tools like MDT and SCCM has been around.