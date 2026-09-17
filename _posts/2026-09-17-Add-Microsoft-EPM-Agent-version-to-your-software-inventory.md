---
title: "Add Microsoft EPM Agent version to your software inventory"
date: 2026-09-17
categories:
  - Intune
tags:
  - EPM
  - Microsoft EPM
  - Endpoint Privilege Management
  - Software Inventory
  - Intune
---

If you're managing Microsoft Endpoint Privilege Management (EPM) with Intune, the agent version is one of those values that is useful to report on and include in your software inventory.

## Why this matters

When you are managing a modern endpoint estate, software inventory is about more than just knowing what is installed. It is also about knowing whether the right version is deployed, whether devices are healthy, and whether upcoming policy changes or feature updates may require action.

The Microsoft EPM agent is no exception. If you want to validate rollout progress, confirm the version on a device, or help support teams troubleshoot a policy issue, having the agent version in your inventory is incredibly useful.

## Remediation to write EPM Versioning to installed apps

You can use Intune's remediation system to continuously write the EPM version information to the installed apps on the device. When you do this, it makes sure the "Discovered apps" functionality in Intune can also inventory the agent version, whilst also adding support for your own 3rd party SAM systems such as Snow Inventory or similar.

You can find the remediation I wrote on my GitHub [here](https://github.com/thisisevilevil/IntunePublic/tree/main/Remediations/EPM%20Agent%20Version%20to%20Software%20list)

Assign to "All devices" and have it run daily. Make sure to tick "Run script in 64-bit PowerShell".
![EPM](/assets/images/2026-09-17-Adding-Microsoft-EPM-Agent-Software-Inventory-Reports/Remediation-Screenshot.png?raw=true "Remediation script settings - run in 64-bit PowerShell")

Once you have it running, this is what it's going to look like in your installed apps on your endpoints:
![EPM](/assets/images/2026-09-17-Adding-Microsoft-EPM-Agent-Software-Inventory-Reports/EPMReport-1-InstalledApps.png?raw=true "Microsoft EPM Agent in Windows Installed apps")

In Intune you will see it under discovered apps:
![EPM](/assets/images/2026-09-17-Adding-Microsoft-EPM-Agent-Software-Inventory-Reports/EPMReport-2-Intune.png?raw=true "Microsoft EPM Agent in Intune Discovered apps")

## Wrapping up

During the early days of testing and rolling out EPM at one of my customers, who was one of the early adopters, it was common that the EPM team would ask us for the client version during debugging sessions. We had to go and chase it in the registry, which is why I made this script. It's just a small thing, but in the end we actually discovered there were quite a few devices that were stuck running an old version. The EPM team assisted us in getting those fixed, so it was super awesome. And of course we did point out that we would like the EPM version written to "Installed apps" without having to resort to custom solutions, but they had their reasons for not doing it at the time.

That's all for now. Have a nice day :)
