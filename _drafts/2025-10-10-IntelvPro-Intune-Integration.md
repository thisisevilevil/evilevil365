---
title: "Intel vPro Integration with Intune"
date: 2025-10-14
categories:
  - Intune
tags:
  - Intel vPro
  - vPro Integration Intune
  - Intel AMT
  - Intel CIRA
---

Intel vPro and Intel AMT (Active Management Technology) is probably one of the most overlooked features in enterprises. It also seems like a lot of companies purchase Intel vProenterprise devices but without actually activating and using the features. Why is that? A combination Lack of awareness, lack of documentation, complexity and with a hint of paranoia because of some old articles suggesting CIA, NSA and aliens from outer space have backdoors into Intel AMT.

I helped configure Intel AMT in a retail environment some years back, but that was before there were any cloud functionality, so we maintained an excel sheet with the IP, User and passwords for each workstation. That was fun (not really..). Also the tools to configure Intel AMT over the years have had many names and variations. Intel ACU, Intel SCS and Intel EMA, don't be surprised if you hear about those.

But let's pause here for a second, and look at what Intel vPro and Intel AMT actually is.

* A business-centric PC platform providing enhanced hardware-based security, remote manageability, and business-grade performance

* Remote Management (Intel® AMT): Allows IT staff to remotely monitor, diagnose, and resolve issues on computers, regardless of their location or whether the operating system is running.

* Hardware-Enhanced Security: Provides multilayered security features built into the hardware to protect against malware, protect user credentials, and secure data, even at the firmware level.

In other words: If organizations had these capabilities when the crowdstrike debacle ensued, they would have been a lot better of to recover their devices remotely.

I exchanged a few emails and calls with the good folks at Intel, supporting this new Intel vPro portal. They helped me set up my tenant and we also troubleshooted a few issues I encountered.
Let's take a look at how to configure Intel AMT with these new toys from Microsoft and Intel, whilst also gaining some insight in what direction this product will take in the near future.

## Configuring Intel vPro w. Intune integration

Microsoft recently announced Intel vPro Portal integration in Intune. [Released in September 15 2025 for Intune service release 2509](https://learn.microsoft.com/en-us/intune/intune-service/fundamentals/whats-new#intel-vpro-fleet-services-integration-in-intune-partner-portal-), this feature allows you to integrate Intune tenant with the [Intel vPro portal](https://vprofleet.intel.com/)

This automatically provisions your Intel vPro tenant as well.

![IntelvPro](/assets/images/2025-10-10-IntelvPro-Intune-Integration/IntelvPro-Portal.png?raw=true "Intel vPro Intune Portal Integration")

Some quick goctha's regarding the Intel vPro portal:

1) Make sure to save your passphrase. **The passphrase is added security to your user, and should not be conflated with the password for the user that logs in**. I highly recommend ticking the box "Store passphrase in browser" when signing in, to avoid having to type that over and over.

2) By signing up for EntraID Integration, you eliminate the need for using passwords, when signing into the portal. Your users you decide to onboard to the Intel vPro Portal, should only use their passphrase. At some point in the future, we also get the option to completely disable passphrases altogether.

3) If you want to provide access to someone else than yourself, you can create their user manually in the vPro portal, under the user management section. In the future, we will get an options that allows us to automatically provision users in the Intel vPro portal, based on EntraID Groups.

>NOTE: If you have signed up for EntraID Integration make sure the "Email" attribute is populated for your users you are trying to onboard, otherwise SSO will not work, and you will face an error message in the Intel vPro portal when trying to sign in. Intel is working on migrating to using UPN instead of email attributes, as it's quite common for admin accounts to have a blank Email attribute in entra.

4) Right now, it's only possible to provision devices in what's referred to as User Consent Mode. If you want to remotely take over the device via the portal, the end-user on the other end will need to provide you a code, much like Quick Assist and Remote Help. However, in the not so distant future, we will get the option to provision device in Admin Controlled Mode (ACM) with this new portal. This could be in factory areas or retail spaces, or kiosk areas where unattended access is required, since the device is not a personally owned device. Devices in ACM, allows IT to take control of the device without requiring consent of the user logged on to the device

### Onboarding devices to vPro

Onboarding devices to Intel vPro is super simple in the new setup flow.

1) Configure an endpoint group or use the default endpoint group - Each endpoint group needs their own package/application for onboarding.
2) In the right side, you can press the dropdown box under actions. Select the "Download agent files" action
3) Download both files and make an Intune package using Intune Content Prep Tool. Do not rename the files, and keep both files in the same folder, when creating the .intunewin file
4) Finally upload the app to Intune as a Win32 app - The default install/uninstall command is prepopulated and you can use the default MSI Detection method as well

Once the package is ready, you can start rolling this out to your vPro-ready devices. As always, test on a few devices first, before you decide to roll this out broadly. Also consider how you group your devices up into Endpoint Groups. Endpoint Groups are important if you want to limit who can perform remote actions and take control of devices, to specific endpoint groups for specific IT Admins.

Once the device gets the package installed, it will automatically onboard itself into the Intel vPro portal.

For onboarding to work smoothly, I strongly recommend you perform the following steps before you try and onboard a device:

1) Ensure BIOS and Intel iCLS driver is fully up to date. If you are running a really old version not only is it most likely vulnerable but functionality might be limited. Always stay up-to-date.
2) Make sure your device is vPro-capable. It is possible to onboard devices that is not vPro capable, but they will never connect. <Note to self <PowerShell script requirement script/check to prevent installation of agent??>
3) If your device is not connecting you can check the following:

* Verify Intel MEBx is enabled in the BIOS. If you have previously tinkered with it, you might need to unprovision it. Most OEMs should ship it in an Unprovisioned state, allowing it to seamlessly onboard, once the onboarding package is deployed to the device
* Open the Intel Management and security app on the device and check the status. It should look similar to below if everything is working as expected
![IntelvPro](/assets/images/2025-10-10-IntelvPro-Intune-Integration/IntelME_Configured_1.png?raw=true "Intel vPro Intune Portal Integration")
![IntelvPro](/assets/images/2025-10-10-IntelvPro-Intune-Integration/IntelME_Configured_2.png?raw=true "Intel vPro Intune Portal Integration")

