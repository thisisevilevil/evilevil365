---
title: "Avoid the Headache: 10 things to be aware of, When Moving to Windows 11"
date: 2025-04-14
categories:
  - Windows 11
tags:
  - Windows 11 Migration
  - Windows 11 EoL (End of Life)
  - Intune feature update
---

The end for Windows 10 support is drawing near. October 2025 marks the last month Windows 10 will be patched, unless you are on Windows 10 LTSC. If you have devices on Windows 10 after this date, you will either have to pay the hefty price for ESU licenses, more about that [here](https://learn.microsoft.com/en-us/windows/whats-new/extended-security-updates) or simply do nothing and hope for the best, which is not recommended at all.

I have helped a few organizations move to Windows 11, and along the way I've picked up some common painpoints both small, medium and large organizations go through when embarking on this journey.


## Number 10: App compatibility

I have heard this phrase a lot "We need to thoroughly test all of our applications before moving to Windows 11". While there is nothing wrong with this approach, what usually ends up happening is just plain and pure stagnation. A lot of IT Organizations have a large amount of applications with many different app owners, and in companies that has offices all around the world, this approach will usually not get you over the finish line, far from it.

Microsoft is actually commited to help companies migrate any applications that is seemingly having compatibility issues with Windows 11, through their app assure program. You can read more about this, in a blogpost from Microsoft [here](https://techcommunity.microsoft.com/discussions/windows11/reminder-our-windows-11-application-compatibility-promise/3223595?)

Lastly, in my personal experience of assisting customers to migrate to Windows 11, 9,9/10 apps works fine with Windows 11.

My advice? Flip this upside down. Get started with upgrading your devices to Windows 11, and if you encounter any issues with apps, you can continue to rollback to Windows 10.

## Number 9: Credential Guard breaks MSCHAPv2 utilized with WiFi

If you use WiFi profiles using PEAP/MSCHAPv2 it will break on Windows 11. This happens because [Credential guard](https://learn.microsoft.com/en-us/windows/security/identity-protection/credential-guard/) is enabled out of the box on Windows 11. In short, Credential guard is a technology that can prevent leaked credentials to a malicious 3rd party. Unfortunately, it also breaks old protocols like MSCHAPv2. Some network admins already knows this, but I've also spoken to a lot that simply doesn't know. It has been working for years, why change it now?

MSCHAPv2 was released in the 90s, which as we all know, the circumstances was very very different on the big internet back then. There are [advisories and vulnerabilities](https://learn.microsoft.com/en-us/security-updates/securityadvisories/2012/2743314) warning against the use of MSCHAPv2 for more than 10 years, but I keep encounter orgnizations using it.

So what to do? Well glad you asked: Migrate to EAP-TLS for your WiFi needs! You can deploy PKCS or SCEP Certificates to your devices from Intune and have your WiFi rely on certificates instead. Personally, I have started to recommend [PKCS Certificates](https://learn.microsoft.com/en-us/intune/intune-service/protect/certificates-pfx-configure) to organisations due to it being much simpler to configure compared to SCEP Certificates, but ultimately I recommend you look at products like [Intune Suite: Cloud PKI](https://learn.microsoft.com/en-us/intune/intune-service/protect/microsoft-cloud-pki-overview) for all your certificate needs.

Ultimately, it is possible to disable Credential Guard to get MSCHAPv2 working again on Windows 11, but i strongly advise against it.

## Number 8: Memory Integrity prevents old drivers from loading

[Memory Integrity](https://learn.microsoft.com/en-us/windows-hardware/design/device-experiences/oem-hvci-enablement) is a security feature enabled out of the box on clean installs of Windows 11 that can prevent old or unsigned drivers from loading. It's a sub-feature of [Virtualization-Based Security(VBS)](https://learn.microsoft.com/en-us/windows-hardware/design/device-experiences/oem-vbs)

If an application launched a driver that is being blocked by memory integrity the error can look like this:
![MemoryIntegrity](/assets/images/2025-05-24-10-Mistakes-MigrationTo-11/MemoryIntegrity-DriverLoadError.png?raw=true "Driver load error")

There are also scenarios when it can cause a BSoD (Blue screen of death) upon OS Bootup, giving the impression that the application/driver causing a BSoD does not support Windows 11, while in reality it's Memory Integrity that's blocking the driver from load

Rather than not upgrading to Windows 11, it's better to just turn Memory Integrity off on the devices where you see this issue. This can be done using a Settings Catalog Profile from Intune:

![MemoryIntegrity](/assets/images/2025-05-24-10-Mistakes-MigrationTo-11/MemoryIntegrity-Disable-Intune.png?raw=true "Disable Memory Integrity")

>Note: If you are enabling VBS and Memory Integrity with a UEFI Lock, simply disabling it with a policy or a reg key will not work. If it has previously enabled with an UEFI lock, you will have 2 options: #1: Use bcdedit to manually clear the UEFI Lock for VBS, also described [here](https://learn.microsoft.com/en-us/windows/security/identity-protection/credential-guard/configure?tabs=intune#disable-virtualization-based-security) or Option #2: Disable Secure Boot in the sytem BIOS. Disabling secure boot is usually not recommended, but can be a good workaround if you are facing a lot of problems lifting the UEFI Lock.

## Number 7: Devices in China doesn't support Windows 11 due to lack of TPM Support

While this was the case many years ago, this has since then been lifted for commercial customers in 2021, for more details, see [this article from HP](https://support.hp.com/us-en/document/ish_5031710-5031755-16)

>Note: you might have a lot of systems out there where TPM is disabled in BIOS, which would block it to move to Windows 11, even when it's fully supported. Some hardware vendors have tools you can use to enable TPM with a script. It's possible to create a Win32 app in Intune, with a TPM Detection script, where you can run some code that enables the TPM. Specifically for Dell systems, I recently assisted a customer enabling TPM on 100's of Dell devices, where each device also had a unique BIOS Password, with a PowerShell script. You can find this script in my Github [here](https://github.com/thisisevilevil/IntunePublic/tree/main/Packages/Dell%20Enable%20TPM%20w.%20BIOS%20Password). That will pave the way for them to move to Windows 11, rather than replacing the devices upfront, saving some $$$ in the process.

## Number 6: Aging hardware with no support for Windows 11

The more hardware you have that needs to be replaced, the sooner you get started with that replacement process the better. Especially in companies that has offices all around the world, it's not always straight forward to replace devices, if it's running special software or hardware that needs special configuration. Also if your high season is approaching, it can also be daunting to embark on a large scale hardware replacement journey.

However, I will underline the alternative is much worse. If you don't have your aging hardware that doesn't support Windows 11 replaced by October 2025, you will either have to pay 61$ for ESU licenses, pr. device, or simply do nothing and risk being attacked.

>Note: Technically there is a 3rd option, where you can force upgrade hardware that doesn't support Windows 11, but it's highly ill-advised, doesn't always work, and should generally be avoided where possible. You can find one of my blog posts [here](https://evil365.com/windows%2011/ForceWindows11-Upgrade-UnsupportedHardware/) that describes it in more detail.

## Number 5: It does not require a reinstall of Windows to migrate to Windows 11

This one I get a lot, and I mean a lot. A lot of companies is saying "Ah, we want our users to Windows 11 rather than upgrading, because then we clean their PCs, and they can start from a clean slate". This is, in the vast majority of cases, not correct. What will usually just end up happening is when they get their device reinstalled to Windows 11, the exact same software they had before, will just be installed again and they are back to where they were before. So rather than having the user spend 30 minutes to upgrade their device from Windows 10 to 11 seamlessly, they now have to go through the trouble of performing a full reinstall of the device. What exercabates this issue is companies that haven't implemented Windows Autopilot yet, so some companies offers to reinstall their device using a Windows 11 USB, SCCM PxE Boot or similar.

It's a much better user experience just letting the end-user utilize that Windows 11 upgrade functionality via Windows update, which on newer hardware can be done in less than 30 minutes. This process is so smooth and seamless on newer hardware. Having end-users have to go through a full reinstall of their device the old fashioned way, will only prolong your Windows 11 migration efforts, as it's inconvenient for the end-user.

It's time to let go of the trauma you had to endure as an IT Admin, migrating from Windows 7 to 10.

## Number 4: Communication

This one can be super important to prepare your organization for Windows 11. The earlier you start the communication and shift towards Windows 11, the better. By communicating to your organization why this change is happening and in what timeframe, this can save you a lot of trouble down the road, in regards to how to approach your Windows 11 migration efforts, especially if you start early. While that ship has properly sailed already, and if you find yourself really far behind the curve, it's never too late to get started. Draft some simple and easy to understand communication for your organization, and publish it on your intranet, like sharepoint or similar, letting your organization know this change is occuring within the next few months.

## Number 3: Leave a buffer for troubleshooting

There are some devices that won't migrate to Windows 11. While it can be easier to ask the user to reinstall the device, in some cases, we would like to know what is causing the device to be blocked or preventing it from moving to Windows 11. Make sure to give yourself some time to troubleshoot and deal with these devices before the October deadline comes near, as an organization could easily have a few hundred or maybe thousands of devices that's just stuck on Windows 10 for whatever reason. In this case, it might just be easier to offer the user to reinstall/reset the device, but in some cases, it's also worth figuring out what is wrong.

I have previously written a blog post for how you can troubleshoot feature update issues, you can find it [here](https://evil365.com/intune/troubleshooting/Troubleshoot-featureupdate-Setupdiag/) 

You can also find a script that I have created, that you can use as an on-demand remediation from Intune, to immediately trigger the windows 11 update again, for troubleshooting purposes. You can find this script [here](https://github.com/thisisevilevil/IntunePublic/blob/main/Remediations/On%20Demand%20-%20Force%20Windows%2011%2024H2%20Update/Remediate-ForceWin11_24H2_Update.ps1)

## Number 2: Let your end users choose the time to perform the upgrade

Not so long ago, Microsoft gave us a new way to deal with feature updates. We can now make them optional for the end users, allowing the end user to choose the time of the update. This is super flexible, and allows you to communicate early to your organization for any early adopters or for people that want to help, just have them pop in to windows update and opt-in to getting Windows 11 ahead of the curve. This policy can be deployed using Intune with a few single clicks, you can read more about it [here](https://techcommunity.microsoft.com/blog/windows-itpro-blog/more-flexible-windows-feature-updates/4139230)

If you ask me, this is a super powerful way to get started: Let your end users choose the time, and coming back to number 4, you can communicate clearly that the users can start the upgrade themselves, but after a certain amount of time, you will eventually force users to upgrade.

## Number 1: Manually selecting devices/users for the upgrade

While this can be good for testing, using this as your general approach for rolling out Windows 11 is usually not a good idea. For someone to manually add users or devices to groups small batches at a time, will cause delays and will usually not work for a broad scale rollout of Windows 11, and you will probably find yourself with plenty of Windows 10 devices in your estate, come October. What can make this take even longer is if you have regional manager/directors for IT wanting to control the cadence by sending you small lists of devices at a time that should get the Windows 11 upgrade offered. This approach relies on so many human factors, it's bound to fail, and will not work on a broad scale.

It's much easier to use the built-in functionalities of Intune with Gradual rollouts of Autopatch multi-phase rollouts. It will automatically divide devices up in to small batches of groups, based on a start date and an end date, thus providing that staggered rollout methodology. This is the preferred way.

![FeatureUpdate](/assets/images/2025-05-24-10-Mistakes-MigrationTo-11/FeatureUpdate-GradualRollout.png?raw=true "Gradual Rollout")

## The end

The deadline is getting nearer, it's not long until October 2025. It's better to get started now, soon rather than later. You can essentially break this Windows 11 upgrade project down into several components:

1. Comms: Communicate the change to your orgnization
2. Pushing the upgrade to your end-users gradually using convential tools like Intune feature update policies or autopatch and monitor progress in the intune update report
3. Replacing aging hardware
4. Chasing down any devices or users that is not upgrading for whatever reason, and start troubleshooting
5. Vendor/App management in case of software or hardware that doesn't support Windows 11 for now.

Each of these tasks can be equally time consuming  compared to your orgnization, so it's time to get started, if you haven't already

I hope you found this useful. :)