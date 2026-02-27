---
title: "EPM and MSI Shenanigans"
date: 2026-02-27
categories:
  - EPM
tags:
  - Microsoft Intune
  - Endpoint Management
  - Microsoft EPM
  - Disk space management
---

I recently faced an issue at one of my customers where a smaller amount of users reporting they had no free disk space, even though they barely had any apps installed or documents stored locally.

If you have been using Microsoft EPM for a while, then spoiler alert: You might also be (unknowingly) affected by this problem.

![Thumbnail](/assets/images/2026-02-27-EPMandMSI-Shenanigans/Thumbnail.png?raw=true "Thumbnail")

## Locating the problem

I jumped on a users device, and a first glance I couldn't find out where the disk space had disapearred off to, so I had to fire up treesize to see what was going on.

![Treesize](/assets/images/2026-02-27-EPMandMSI-Shenanigans/Treesize.png?raw=true "Treesize overview")

Looks like the following path was stuffed with MSI Files: `C:\Windows\System32\Config\systemprofile\AppData\Local\MDM`

![MSI](/assets/images/2026-02-27-EPMandMSI-Shenanigans/MSIFile.png?raw=true "EPM MSI Files")

After examining a few MSI File, it was clear that it's an EPM MSI. Examining EPM logs on the device showed that it was stuck in an update loop. It kept failing with error 1603 (Fatal error.) It seems EPM, or more specifically, the CSP/Provider that EPM uses to update the EPM Agent, is not cleaning up after itself. There might also be an underlying issue causing the MSI to fail with 1603, but nevertheless, it should know how to clean up after itself when things go wrong, to avoid this scenario.

I also wanted to check if more users had this problem, so I wrote a remediation / detection script only, to check if that MDM Folder was above 1GB, and if it was, it would return with issue. I also made sure that it would print the total disk space of the folder to the Intune console, to see how bad it really was, and boy it found some really bad ones. In the worst cases there was over 300GB of EPM MSI Files stuck in that folder. Luckily, the issue had only seem to hit a small percentage of their devices so far. But these types of issues can easily snowball very fast, and will impact end-users productivity.

![Diskspace](/assets/images/2026-02-27-EPMandMSI-Shenanigans/DiskSpace-remediation-output.png?raw=true "Diskspace output")

## The fix

I've already engaged the EPM team at Microsoft, and they are looking into the problem. But while we are waiting for a permanent fix, I've written a remediation to clean up the folder. It's a very simple detection script that simply checks the folder if it's above 1GB in size, it will proceed to remediation, where the remediation script cleans up all the files if the folder.

You can find the scripts [here](https://github.com/thisisevilevil/IntunePublic/tree/main/Remediations/Fix%20high%20diskspace%20consumption%20in%20MDM%20Folder).

You actually also re-use this script and point it to any other folder i.e: temp folders etc. and have them clean it up if it gets above a certain size. In the detection script, adjust the folder variable and also decide how much the threshold (in GB) should be, before it proceeds to clean up the folder. Then in the remediation script, also make sure to adjust that folder accordingly.

