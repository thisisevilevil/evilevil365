---
title: "Intel vPro Integration with Intune"
date: 2025-10-14
categories:
  - Intune
tags:
  - Intel vPro
  - vPro Integration Intune
  - Intel AMT
  - Intel Cira
---

Intel vPro and Intel AMT (Active Management Technology) are probably some of the most overlooked features in enterprises. It also seems like many companies purchase Intel vPro–capable devices without actually activating or using the features. Why is that? A combination of lack of awareness, lack of documentation, complexity—and perhaps a hint of paranoia due to some old articles suggesting that the CIA, NSA, and aliens from outer space have backdoors into Intel AMT.

I helped configure Intel AMT in a retail environment some years back, but that was before any cloud functionality existed, so we maintained an Excel sheet with the IPs, users, and passwords for each workstation. That was fun (not really…). Over the years, the tools to configure Intel AMT have had many names and variations: Intel ACU, Intel SCS, and Intel EMA. Don’t be surprised if you hear about those. Many companies already purchase vPro-capable devices, but they’re not using the functionality. Some might also have a split estate where some devices are vPro-capable and others are not.

But let’s pause for a second and look at what Intel vPro and Intel AMT actually are:

* **A business-centric PC platform** providing enhanced hardware-based security, remote manageability, and business-grade performance.
* **Remote Management (Intel® AMT):** Allows IT staff to remotely monitor, diagnose, and resolve issues on computers—regardless of their location or whether the operating system is running. For the server people out there, think of this like a miniature version of HPE iLO or Dell iDRAC.
* **Hardware-Enhanced Security:** Provides multilayered security features built into the hardware to protect against malware, safeguard user credentials, and secure data—even at the firmware level.

In other words: if organizations had these capabilities when the CrowdStrike debacle ensued, they would have been much better off recovering their devices remotely.

I exchanged a few emails and calls with the good folks at Intel supporting this new Intel vPro portal. They helped me set up my tenant and troubleshoot a few issues I encountered.  
Let’s take a look at how to configure Intel AMT with these new tools from Microsoft and Intel, while also gaining some insight into the direction this product is heading in the near future.

## Configuring Intel vPro with Intune integration

Microsoft recently announced Intel vPro Portal integration in Intune. [Released on September 15, 2025, for Intune service release 2509](https://learn.microsoft.com/en-us/intune/intune-service/fundamentals/whats-new#intel-vpro-fleet-services-integration-in-intune-partner-portal-), this feature allows you to integrate your Intune tenant with the [Intel vPro portal](https://vprofleet.intel.com/).

This automatically provisions your Intel vPro tenant as well.

![IntelvPro](/assets/images/2025-10-10-IntelvPro-Intune-Integration/IntelvPro-Portal.png?raw=true "Intel vPro Intune Portal Integration")

Some quick gotchas regarding the Intel vPro portal:

1. **Save your passphrase.** The passphrase adds an extra layer of security to your user account and should not be confused with your login password. I highly recommend ticking the box “Store passphrase in browser” when signing in to avoid retyping it repeatedly.
2. By signing up for Entra ID integration, you eliminate the need to use passwords when signing in to the portal. Users you onboard to the Intel vPro portal should only use their passphrase. In the future, we’ll also get the option to completely disable passphrases.
3. If you want to provide access to someone else, you can manually create their user account in the vPro portal under **User Management**. In the future, we’ll get an option that allows automatic user provisioning in the Intel vPro portal based on Entra ID groups.

> **NOTE:** If you’ve signed up for Entra ID integration, make sure the “Email” attribute is populated for the users you’re trying to onboard. Otherwise, SSO will not work, and you’ll see an error message in the Intel vPro portal when signing in. Intel is working on migrating to using the UPN instead of the Email attribute, as it’s common for admin accounts to have a blank email field in Entra.

4. Currently, it’s only possible to provision devices in what’s referred to as **Client Control Mode (CCM)**. If you want to remotely take over a device via the portal, the end user will need to provide a code to the IT Admin—similar to Quick Assist or Remote Help. However, in the near future, we’ll get the option to provision devices in **Admin Control Mode (ACM)** with this new portal. This mode is ideal for factory areas, retail spaces, or kiosk environments where unattended access is required, as the device has no user. Devices in ACM allow IT to take control without requiring user consent.

### Onboarding devices to vPro

Onboarding devices to Intel vPro is super simple in the new setup flow:

1. Configure an endpoint group or use the default endpoint group—each endpoint group requires its own onboarding package/application.  
2. On the right-hand side, open the dropdown menu under **Actions** and select **Download agent files**.

![IntelvPro](/assets/images/2025-10-10-IntelvPro-Intune-Integration/DownloadeAgentFiles.png?raw=true "Download agent files") 

3. Download both files and create an Intune package using the Intune Content Prep Tool. Do not rename the files, and keep both files in the same folder when creating the `.intunewin` file.  
4. Finally, upload the app to Intune as a Win32 app. The default install/uninstall commands are prepopulated, and you can use the default MSI detection method as well.

Once the package is ready, you can start rolling it out to your vPro-ready devices. As always, test on a few devices first before rolling it out broadly. Also consider how you group your devices into Endpoint Groups. Endpoint Groups are important if you want to limit who can perform remote actions and take control of devices—assigning specific endpoint groups to specific IT admins.

Once the package is installed, the device will automatically onboard itself into the Intel vPro portal.

If everything works correctly, you will now be able to remotely power on, powercycle and remotely access the device amongst other things.
![IntelvPro](/assets/images/2025-10-10-IntelvPro-Intune-Integration/IntelvPro-DeviceActions-1.png?raw=true "Intel vPro Intune Portal Integration")
![IntelvPro](/assets/images/2025-10-10-IntelvPro-Intune-Integration/IntelvPro-DeviceActions-2.png?raw=true "Intel vPro Intune Portal Integration")

## Onboarding and troubleshooting tips

There can be various causes to lack of connectivity to Cira, so here is a few tips/tricks to make sure the device will always be able to connect:

1. Ensure BIOS and Intel Management Engine drivers are fully up to date. If you’re running a very old version, it’s likely vulnerable and may not work correctly. Always stay up to date with drivers and BIOS updates from your OEM.  
2. Make sure your device is vPro-capable. It’s possible to onboard devices that aren’t vPro-capable, but they’ll never connect. The **Intel Management and Security** app in Windows will also look empty, since either vPro is not supported or Intel MEBx is disabled in BIOS.
![IntelvPro](/assets/images/2025-10-10-IntelvPro-Intune-Integration/IntelMEApp-vPro-notsupported.png?raw=true "Intel ME - vPro not supported")

   > _Note to self:_ PowerShell script requirement check to prevent installation of the agent on unsupported devices? Ask intel for PowerShell script 
3. Ensure the device is connected via Ethernet or Wi-Fi. However, be aware that **802.1x connections (certificate-based authentication)** are currently not supported. Support for this will be added later.
4. If your device is is hibernating or gone to sleep, it's likely you will not be able to connect to the device remotely or perform any power actions. This is partially due to the fact that we want to prevent powering on devices that it's a bag, to prevent overheating. If you are managing a factory floor or kiosk devices, I recommend disabling sleep to retain connectivity towards AMT.
5. If your device isn’t connecting, check the following:
   * Verify Intel MEBx is enabled in the BIOS. If you’ve previously modified it, you might need to unprovision it. Most OEMs ship devices in an unprovisioned state, allowing them to seamlessly onboard once the package is deployed.  
   * Open the **Intel Management and Security** app on the device and check the status. It should look similar to the examples below if everything is working as expected.

![IntelvPro](/assets/images/2025-10-10-IntelvPro-Intune-Integration/IntelME_Configured_1.png?raw=true "Intel vPro Intune Portal Integration")  
![IntelvPro](/assets/images/2025-10-10-IntelvPro-Intune-Integration/IntelME_Configured_2.png?raw=true "Intel vPro Intune Portal Integration")

In the vPro portal, it should show **“CIRA Connected”** and **“Power On”** with green indicators once it’s onboarded, as shown below:  
![IntelvPro](/assets/images/2025-10-10-IntelvPro-Intune-Integration/IntelvPro-Portal-DeviceConfigured-1.png?raw=true "Intel vPro Intune Portal Integration")

## Final words

Intel AMT has historically been a pain to configure, manage, and maintain—but with this new portal and the integration with Intune and Entra, things are really starting to look good. Intel AMT and the remote capabilities it provides can be a huge help in scenarios where the OS isn’t booting, allowing IT to take control of the device even when it’s offline. The unattended access feature will also be a great alternative to TeamViewer or other remote support tools once it’s released.

If you are already using Intel EMA, you will find that this new portal is missing a lot of features. Intel EMA is a self-hosted appliance in Azure that's been around for a few years, but I can only surmize that due to complexity and low adoption rates, Intel is pivoting towards this new cloud portal to make adoption and onboarding a breeze for IT Admins. So think of this new Intel vPro portal as version 1.0, with many more things to come.

With this new portal and integration, we now have a streamlined and simple approach to onboarding and configuring devices for Intel vPro and AMT—which is awesome!
