---
title: "Whats up with the Secure Boot certificates expiring in 2026?"
date: 2025-11-19
categories:
  - Intune
tags:
  - Secure Boot certificates
  - Certificate expiry
---

>**UPDATED 10th of December 2025**: Microsoft hosted a live AMA that gave us a lot more information about how all of this is going to work. You can find the link for it [here](https://techcommunity.microsoft.com/event/WindowsEvents/ama-secure-boot/4472784?after=MjUuOXwyLjF8aXwxMHwxMzI6MHxpbnQsNDQ3NjkwMCw0NDc2ODgw&topicRepliesSort=postTimeDesc)

If you manage Windows devices, you might have noticed some alerts about Secure Boot certificates expiring in 2026. This is a common concern, but there's no need to panic. I've noticed some conflicting information about to manage this issue, hence this blog post to clear things up.

![Thumbnail](/assets/images/2025-09-03-SecureBoot-Cert-Expiration/Thumbnail.png?raw=true "Thumbnail")

## What you need to know

* The existing 2011-era certificates (KEK CA 2011, UEFI CA 2011, and Production PCA 2011) are expiring in mid‑2026, which would disrupt Secure Boot security.
* Failing to update the boot certificates could result in the following implications:

1. Lose the ability to install Secure Boot security updates after June 2026.
2. Not trust third-party software signed with new certificates after June 2026.
3. Not receive security fixes for Windows Boot Manager by October 2026.

[Source](https://techcommunity.microsoft.com/blog/windows-itpro-blog/act-now-secure-boot-certificates-expire-in-june-2026/4426856)

## Deploying the updated Secure Boot certificates

You have several options for deploying the secure boot certificates. The most interesting options are the following:

### Option 1 - Automatic rollout via High-confidence buckets

This is the most hands-off approach, but it also requires a bit of faith that your device has been placed into one of Microsoft’s “high-confidence buckets.” If it hasn’t, the device won’t automatically receive the updated Secure Boot certificates. But otherwise note that this option is turned on by default, and is one you have to opt-out of, if you don't want to be part of the automatic rollout.

Microsoft describes this process as follows:

> “Microsoft may automatically include high-confidence device groups in monthly updates based on diagnostic data shared to date, to benefit systems and organizations that cannot share diagnostic data. This step does not require diagnostic data to be enabled.” [(Source)](https://support.microsoft.com/en-us/topic/secure-boot-certificate-updates-guidance-for-it-professionals-and-organizations-e2b43f9f-b424-42df-bc6a-8476db65ab2f#bkmk_automated_deployment_assists)

**Microsoft hosted an AMA the 10th of December 2025 where they clarified the following:**
> "A high-confidence device refers to one that Microsoft can reliably identify and update automatically through Windows Update without additional intervention. These devices typically meet criteria such as: Trusted diagnostic data signals confirming the device’s identity and compatibility, Secure Boot enabled and using supported UEFI firmware, Running a supported Windows version that can receive updates and No anomalies in the boot chain or firmware keys that could block the update process."

There should be no harm in letting Microsoft update devices via this channel, but if you want to opt-out of this option for whatever reason, you can set a policy to opt out using Intune.

![Policy](/assets/images/2025-09-03-SecureBoot-Cert-Expiration/SettingsCatalog-OptOut.png?raw=true "High Confience Opt-out")

### Option 2 - Automatic rollout via Microsoft Controlled Feature Rollout (CFR)

This option corresponds to the registry key "MicrosoftUpdateManagedOptIn".

By deploying this policy you will participate in a Microsoft-managed rollout also known as Controlled-feature rollout. This rollout will be fully controlled by Microsoft, and usually CFRs involves a careful and staggered rollout approach based on certain criteria, grouping devices by hardware and firmware, monitoring feedback, and pausing if issues appear. In other words: With this option you are also completely in the hands of Microsoft with this one, but it differentiates slightly from the High-Confidence option. Expect the CFR-rollout option to be much slower.

For this option to work you need to ensure you are sending required or optional diagnostic data to Microsoft. If you are already using WufB or Autopatch you probably already are doing it, but know that in March 2025 Autopatch revoked the policy they deploy by default to do this on your behalf (Ref: MC996580). If you want to be sure, you can craft your own policy and apply to devices in scope. Look for the "Allow Telemetry" setting in the settings catalog. [Source](https://learn.microsoft.com/en-us/windows/deployment/update/wufb-reports-configuration-intune#settings-catalog)

![Policy](/assets/images/2025-09-03-SecureBoot-Cert-Expiration/SettingsCatalog-TelemetryPolicy.png?raw=true "Telemetry Settings Catalog Policy")

If you want to let Microsoft managed the rollout via the CFR process, search for "Secure Boot" in the settings catalog to find the relevant policies.
![Policy](/assets/images/2025-09-03-SecureBoot-Cert-Expiration/SettingsCatalog-MicrosoftManaged.png?raw=true "Microsoft managed rollout of secure boot certs")

>NOTE: Testing this policy through intune as of this date (22. November 2025), it gives an error 65000 in Intune. I'm guessing Microsoft will get this fixed/updated soon. If you also face this error, you can find the registry key to deploy this option, as a workaround, in [my github here](https://github.com/thisisevilevil/IntunePublic/blob/main/PowerShell%20Scripts/Secure%20Boot%20Certificate%20Deployment/Deploy-SecureBootCert-MicrosoftCFR.ps1)

### Option 3 - Self-managed rollout using Intune policies

This option corresponds to the registry key "AvailableUpdates".

If you want to manage the rollout of the secure boot certificates yourself, search for "Secure Boot" in the settings catalog to find the relevant policies.
![Policy](/assets/images/2025-09-03-SecureBoot-Cert-Expiration/SettingsCatalog-SelfManaged.png?raw=true "Self-managed rollout of secure boot certs") 

This option can be highly desirable if you want to be in complete control yourself, as this allows you to roll this policy out in your own rings/waves. This option also should not require for you to send diagnostic data to Microsoft.

>NOTE: Testing this policy through intune as of this date (22. November 2025), it gives an error 65000 in Intune. I'm guessing Microsoft will get this fixed/updated soon. If you also face this error, you can find the registry key to deploy this option, as a workaround, in [my github here](https://github.com/thisisevilevil/IntunePublic/blob/main/PowerShell%20Scripts/Secure%20Boot%20Certificate%20Deployment/Deploy-SecureBootCert-SelfRollout.ps1)

>NOTE: There is also a GPO option for all of the above mentioned options if you are not yet in Intune, or haven't flipped the "Device configuration" workload to Intune yet. You can find more info about the GPOs [here](https://support.microsoft.com/en-us/topic/group-policy-objects-gpo-method-of-secure-boot-for-windows-devices-with-it-managed-updates-65f716aa-2109-4c78-8b1f-036198dd5ce7#bkmk_grouppolicyobject)

### Monitoring for updated certificates

I have created an Intune remediation you can use to monitor your devices are running with the updated secure boot certificate. You can find it [here](https://github.com/thisisevilevil/IntunePublic/blob/main/Remediations/Check%20SecureBoot%20Certificates/Detect-SecureBootCerts.ps1).

Assign it with 64-bit enabled, whilst disabling signature check and disable running with logged-on credentials.

Here is a screenshot of the Remediation being used in production, where Option #3 (self-managed rollout) is being rolled out in rings - The ones marked with "Without issues" have the updated certificate:

![Policy](/assets/images/2025-09-03-SecureBoot-Cert-Expiration/SecureBootCert_Remediation.png?raw=true "Remediation")

## Wrapping up

Microsoft is already working with OEM's to push out BIOS Updates, where the updated certificates are present. So if you are already keeping your BIOS Up-to-date in your org, chances are, you already received the updated certificates. You can find articles from [Dell](https://www.dell.com/support/kbdoc/en-us/000347876/microsoft-2011-secure-boot-certificate-expiration) and [HP](https://support.hp.com/us-en/document/ish_13070353-13070429-16) about how they are adressing things from their end. They are already updating the certificates from their end via BIOS Updates on newer models.

If your devices are in an air-gapped environment or with limited network connectivity, you will have to update these certificates manually. See [this article](https://techcommunity.microsoft.com/blog/windows-itpro-blog/updating-microsoft-secure-boot-keys/4055324) for more information.

The full Microsoft guidance is available [in this article](https://support.microsoft.com/en-us/topic/windows-devices-for-businesses-and-organizations-with-it-managed-updates-e2b43f9f-b424-42df-bc6a-8476db65ab2f)

Plenty of scripts online suggest you need to handle this certificate update yourself. For most organizations, that’s not the case. Microsoft will take care of it or you can choose to take matters into your own hands with new easy-to-deploy intune policies. If you’re running hardware from major OEMs like Dell, HP, or Lenovo and keeping them updated, you’re likely already covered to a certain extent. Also, this is issue is also present on servers, so make sure to prepare accordingly for your servers as well.

Hopefully this clears up the confusion and saves you from chasing unnecessary “DIY fixes.”.

Thanks for reading — and have an awesome day :)