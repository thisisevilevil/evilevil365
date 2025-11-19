---
title: "Whats up with the Secure Boot certificates expiring in 2026?"
date: 2025-09-03
categories:
  - Intune
tags:
  - Secure Boot certificates
  - Certificate expiry
---

>**UPDATE, 18st of November 2025**: Removed all of the previous updates, since new information kept surfacing, so I will TL;DR this: New blog post released by Microsoft a few days ago, you can find it [here](https://techcommunity.microsoft.com/blog/windows-itpro-blog/secure-boot-playbook-for-certificates-expiring-in-2026/4469235)

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

This is the most hands off approach, but it's also where you basically pray to the Microsoft-gods that your device is part of a so called "high confidence bucket". If not, your device will not automatically receive the updated secure boot certificates.
To quote Microsoft directly "Microsoft may automatically include high-confidence device groups in monthly updates based on diagnostic data shared to date, to benefit systems and organizations that cannot share diagnostic data. This step does not require diagnostic data to be enabled." [(Source)](https://support.microsoft.com/en-us/topic/secure-boot-certificate-updates-guidance-for-it-professionals-and-organizations-e2b43f9f-b424-42df-bc6a-8476db65ab2f#bkmk_automated_deployment_assists)

High confidence buckets is otherwise described as devices that are processing updates correctly, but if you are not sending any diagnostic data, Microsofts ability to gauge the device's ability to process updates correctly is limited, and might not work correctly.

![Policy](/assets/images/2025-09-03-SecureBoot-Cert-Expiration/SettingsCatalog-TelemetryPolicy.png?raw=true "Telemetry Settings Catalog Policy")

### Option 2 - Automatic rollout via Microsoft Controlled Feature Rollout (CFR)

Recent additions to the Intune Settings catalog has allowed us to deploy policies to enable the secure boot rollout on-demand. Previously you had to set reg keys to achieve this. 

This option corresponds to the registry key "MicrosoftUpdateManagedOptIn".

By deploying this policy you will participate in a Microsoft-managed rollout also known as Controlled-feature rollout. This rollout will be fully controlled by Microsoft, and usually CFRs involves a careful and staggered rollout approach based on certain criteria, grouping devices by hardware and firmware, monitoring feedback, and pausing if issues appear. In other words: With this option you are also completely in the hands of Microsoft with this one, but it differentiates slightly from the High-Confidence option. Expect the CFR-rollout option to be much slower.

For this option to work you need to ensure you are sending required or optional diagnostic data to Microsoft. If you are already using WufB or Autopatch you probably already are doing it, but know that in March 2025 Autopatch revoked the policy they deploy by default to do this on your behalf (Ref: MC996580). If you want to be sure, you can craft your own policy and apply to devices in scope. Look for the "Allow Telemetry" setting in the settings catalog. [Source](https://learn.microsoft.com/en-us/windows/deployment/update/wufb-reports-configuration-intune#settings-catalog)

### Option 3 - Self-managed rollout using Intune policies

If you want to manage the rollout of the secure boot certificates yourself, search for "Secure Boot" in the settings catalog to find the relevant policies.
![Policy](/assets/images/2025-09-03-SecureBoot-Cert-Expiration/SettingsCatalog-SelfManaged.png?raw=true "Self-managed rollout of secure boot certs"). This option corresponds to the registry key "AvailableUpdates".

This option can be highly desirable if you want to be in complete control yourself, as this allows you to roll this policy out in your own rings/waves. This option also should not require for you to send diagnostic data to Microsoft.

>NOTE: There is also a GPO option for all of the above mentioned options if you are not yet in Intune, or haven't flipped the "Device configuration" workload to Intune yet. You can find more info about the GPOs [here](https://support.microsoft.com/en-us/topic/group-policy-objects-gpo-method-of-secure-boot-for-windows-devices-with-it-managed-updates-65f716aa-2109-4c78-8b1f-036198dd5ce7#bkmk_grouppolicyobject)

>**UPDATE, 19th of November 2025**: I have created a remediation you can use to check your devices are running with the updated cert. You can find it [here](https://github.com/thisisevilevil/IntunePublic/blob/main/Remediations/Check%20SecureBoot%20Certificates/Detect-SecureBootCerts.ps1). Deploys as 64-bit in system context.

## Wrapping up

Be aware that Microsoft is already working with OEM's to also push out BIOS Updates, where the updated certificates are also present. So if you are already keeping your BIOS Up-to-date in your org, chances are, you already received the updated certificates. You can find articles from [Dell](https://www.dell.com/support/kbdoc/en-us/000347876/microsoft-2011-secure-boot-certificate-expiration) and [HP](https://support.hp.com/us-en/document/ish_13070353-13070429-16) about how they are adressing things from their end. They are already updating the certificates from their end via BIOS Updates on newer models.

If your devices are in an air-gapped environment or with limited network connectivity, you will have to update these certificates manually. See [this article](https://techcommunity.microsoft.com/blog/windows-itpro-blog/updating-microsoft-secure-boot-keys/4055324) for more information.

The full Microsoft guidance is available [in this article](https://support.microsoft.com/en-us/topic/windows-devices-for-businesses-and-organizations-with-it-managed-updates-e2b43f9f-b424-42df-bc6a-8476db65ab2f)

Plenty of scripts online suggest you need to handle this certificate update yourself. For most organizations, that’s not the case. Microsoft will take care of it or you can choose to take matters into your own hands with new intune policies. If you’re running hardware from major OEMs like Dell, HP, or Lenovo and keeping them updated, you’re likely already covered to a certain extent. Also, this is issue is also present on servers, so make sure to prepare accordingly for your servers as well.

Hopefully this clears up the confusion and saves you from chasing unnecessary “DIY fixes.”.

Thanks for reading — and have an awesome day :)

