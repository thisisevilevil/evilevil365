---
title: "Intel vPro Integration with Intune"
date: 2025-10-19
categories:
  - Intune
tags:
  - Intel vPro
  - vPro Integration Intune
  - Intel AMT
  - Intel CIRA
---

Intel vPro and Intel AMT (Active Management Technology) is probably one of the most overlooked features in enterprises. It also seems like a lot of companies purchase Intel vProenterprise devices but without actually activating and using the features. Why is that? A combination Lack of awareness, lack of documentation, complexity and with a hint of paranoia because of some old articles suggesting CIA and the NSA have backdoors into Intel AMT.

I helped configure Intel AMT in a retail environment some years back, but that was before there were any cloud functionality, so we maintained an excel sheet with the IP, User and passwords for each workstation. That was fun (not really..). Also the tools to configure Intel AMT over the years have had many names and variations. Intel ACU, Intel SCS and Intel EMA, don't be surprised if you hear about those.

But let's pause here for a second, and look at what Intel vPro and Intel AMT actually is.

* A business-centric PC platform providing enhanced hardware-based security, remote manageability, and business-grade performance

* Remote Management (Intel® AMT): Allows IT staff to remotely monitor, diagnose, and resolve issues on computers, regardless of their location or whether the operating system is running.

* Hardware-Enhanced Security: Provides multilayered security features built into the hardware to protect against malware, protect user credentials, and secure data, even at the firmware level.

In other words: If organizations had these capabilities when the crowdstrike-debacle ensued, they would have been a lot better of to recover their devices remotely.

Let's take a look at how to configure Intel AMT in todays day an age :)

## Configuring Intel vPro w. Intune integration

Microsoft recently announced Intel vPro Portal integration in Intune. [Released in September 15 2025 for Intune service release 2509](https://learn.microsoft.com/en-us/intune/intune-service/fundamentals/whats-new#intel-vpro-fleet-services-integration-in-intune-partner-portal-), this feature allows you to integrate Intune tenant with the [Intel vPro portal](https://vprofleet.intel.com/)

This automatically provisions your Intel vPro tenant as well.

![IntelvPro](/assets/images/2025-10-10-IntelvPro-Intune-Integration/IntelvPro-Portal?raw=true "Intel vPro Intune Portal Integration")

### Onboarding devices to vPro

Onboarding devices to Intel vPro is super simple in the new setup flow.

1) Configure an endpoint group or use the default endpoint group - Each endpoint group needs their own package
2) In the right side, you can press the dropdown box under actions. Select the "Download agent files" action
3) Download both files and make an Intune package using Intune Content Prep Tool. Do not rename the files, and keep both files in the same folder, when creating the .intunewin file
4) Finally upload the app to Intune as a Win32 app - The default install/uninstall command is prepopulated and you can use the default MSI Detection method as well

Once the package is ready, you can start rolling this out to your vPro-ready devices. As always, test on a few devices first, before you decide to roll this out broadly. Also consider how you group your devices up into Endpoint Groups. Endpoint Groups are important if you want to limit who can perform remote actions and take control of devices, to specific endpoint groups.

Once the device gets the package installed, it will automatically onboard itself into the Intel vPro portal.

<PowerShell script to gauge vPro readiness>
<Connectivity>