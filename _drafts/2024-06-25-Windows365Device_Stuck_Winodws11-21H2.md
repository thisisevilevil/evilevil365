---
title: "Fix Windows 365 Devices stuck running Windows 11 21H2"
date: 2024-06-25
categories:
  - Windows 11
tags:
  - Intune
  - Windows 365
  - Windows 11
  - Feature Updates
---

A while back I had an issue at one of my customers, where around ~600 Windows 365 PCs was unable to complete an inplace uupgradet. After trying to manually apply the Windows 11 22H2 at the time, we found in the log files that something was causing a blue screen upon the first bootup on Windows 11 22H2, prompting the device to rollback to WIndows 11 21H2.

I used all the usual tricks up my sleeve to troubleshoot feature updates, using SetupDiag (no signatures found) and analyzing the logs under the hidden folder `C:\$Windows.~BT` for the failed upgrade, which usually helps me resolve these stubborn devices that refuses to update, but this one was just a doozy. I had to get the assistance of Microsoft support for this one.

## Backstory

Well the first problem is the logistics. Because we couldn't reproduce it, and almost none of their IT Personnel had a Windows 365 device with the issue. Since there is no backdoor onto a Windows 365 PC, we had to nag end-users with the issue to have them share their screens, log on to their Windows 365 device, so we could perform our tests and perform diagnostics. That included Downloading the full Windows 11 ISO, then running setup.exe manually. We experimented with a few different switches with the setup.exe (I.e: setup.exe /Auto Upgrade /DynamicUpdate Disable etc. etc.), we also tried applying reg keys to override WufB safeguard and HW requirements for WIndows 11. It was attempted on multiple Windows 365 PCs with the issue, and we ran [SetupDiag.exe](https://learn.microsoft.com/en-us/windows/deployment/upgrade/setupdiag) on all of them, none of them came back with any issues found.

Also manually analyzing the logs under `C:\$Windows.~BT\Sources\Panther` we found some seemingly interesting clues about "Microsoft XPS Document Writer" and "Microsoft Print to PDF" which I had seen many times before to be the cause of a blocking feature update. When we tried manually removing them, after the feature update applied it seemed they were just put back into the operating system. It turned out to be a red herring in the end however, was not the cause of the issue.

Luckily, someone from the Customers ServiceDesk team had a CloudPC with the exact issue, which allowed us to experiment a lot, and finally capture what is known as a TSS Log for Microsoft support. This is a log collecting tool from Microsoft that basically captures the motherload of log files: https://aka.ms/GetTSSv2

## Locating the root cause

After Microsoft support received the TSS Logs, I got the strangest request which was to perform the following steps:

1. Download and extract PSExec
2. Open an elevated cmd and change directory to the downloaded location
3. Run a PSEXEC.exe -I -s cmd.exe (This launches the command prompt as NT AUTHORITY\SYSTEM)
4. Confirm by running a whoami
5. Open Regedit from system context.
6. Export the Security Hive.
7. Upload to Microsoft Support for analysis

It was obvious they found something from the TSS Logs, and low and behold a short while after they got those logs they came back with the following instructions:

1. Run a .\psexec.exe -I -s cmd.exe.
2. Go to the Computer\HKEY_LOCAL_MACHINE\SECURITY\Policy\Accounts hive.
3. Do a backup of the Accounts.
4. Delete any keys and subkeys which do not follow the following pattern:
S-1-5-21-GUID1-GUID2-GUID3-501
Only limit to deleting S-1-5-21 Security Ids ending in 501 Guest accounts [Reference: Security identifiers | Microsoft Learn](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-identifiers#well-known-sids)
5. After this is done, re-attempt the update.

After we manually removed that specific registry key, we were finally successful updating the Windows 365 PC.

So after all of these weeks and weeks of troubleshooting, Microsoft found out that a bug had sneaked itself inside the Windows 11 21H1 Gallery Image for Windows 365. Specifically it seems like it's a bad Guest Profile in the security hive that is preventing the Windows 365 from successfully performing an inplace upgrade.

![Bugs](https://media4.giphy.com/media/v1.Y2lkPTc5MGI3NjExZjdkaDY2MWd5cmY1dW9uNXR0OXZsZjdvNnRjbDV0aDhjajVkMndxbCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/A1SNSC8s40O64/giphy.webp)

## Fixing the feature update issue on +600 Windows 365 Cloud PCs

To remove the bad guest profile, I created the following 1-liner in PowerShell and assigned it to all affected Cloud PCs using Intune:

```PowerShell
gci HKLM:\Security\Policy\Accounts | Where-Object {$_.Name -like '*S-1-5-21-*-*-*-501'} | Remove-Item -Force -Recurse
```

After the script had run on all Cloud PCs, all of the customers ~600 Cloud PCs successfully upgraded to the latest version of Windows 11.

## Finalizing words

If there is one thing this will have taught us, don't be afraid to ask Microsoft for help. Chances are they have access to information and people that knows exactly how to solve a given problem you might have been spending weeks on troubleshooting yourself. In this case, the only possible alternative for myself and this customer was to reprovision all ~600 Cloud PCs, which would have been a really bad option.
But thanks to Microsoft Support's help, they helped us resolve the issue and also made the customer super satisfied in the process. I know they were the ones that introduced the bug in the image, but everyone makes mistakes.

Shout out to the Microsoft supporter who helped the customer and myself trough this ordeal, he was super awesome!

That's all for now folks :)