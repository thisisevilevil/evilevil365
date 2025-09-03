---
title: "Whats up with the Secure Boot certificates expiring in 2026?"
date: 2026-09-03
categories:
  - Intune
tags:
  - Secure Boot certificates
  - Certificate expiry
---

If you manage Windows devices using Intune, you might have noticed some alerts about Secure Boot certificates expiring in 2026. This is a common concern, but there's no need to panic. I've seen some conflicting and downright incorrect information about this online.

![Thumbnail](/assets/images/2025-09-03-SecureBoot-Cert-Expiration/Thumbnail.png?raw=true "Thumbnail")

## What you need to know

* The existing 2011-era certificates (KEK CA 2011, UEFI CA 2011, and Production PCA 2011) are expiring in mid‑2026, which would disrupt Secure Boot security.
* failing to update the boot certificates could result in the following implications:

1. Losing the ability to install Secure Boot updates after June 2026.  
2. Missing Windows Boot Manager fixes after October 2026.  
3. Devices failing to boot, depending on OEM, BIOS, and security settings.

[Source](https://techcommunity.microsoft.com/blog/windows-itpro-blog/act-now-secure-boot-certificates-expire-in-june-2026/4426856)

## Deploying the updated Secure Boot certificates

Microsoft can manage the rollout for your automatically. But for them to do that, there is 2 requirements:

**Requirement #1:** You need to ensure you are sending required or optional diagnostic data to Microsoft. If you are already using WufB or Autopatch you probably already are doing it, but know that in March 2025 Autopatch revoked the policy they deploy by default to do this on your behalf (Ref: MC996580). If you want to be sure, you can craft your own policy and apply to devices in scope. Look for the "Allow Telemetry" setting in the settings catalog. [Source](https://learn.microsoft.com/en-us/windows/deployment/update/wufb-reports-configuration-intune#settings-catalog)

![Policy](/assets/images/2025-09-03-SecureBoot-Cert-Expiration/SettingsCatalog-TelemetryPolicy.png?raw=true "Telemetry Settings Catalog Policy")

**Requirement #2:** You need to deploy a registry key for your devices to tell Microsoft that you are opting in to the automatic rollout. The reg key needs to be deployed like so:
Registry location: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Secureboot
Key name: MicrosoftUpdateManagedOptIn
Key type: DWORD
DWORD value: 0x5944

To make sure it's deployed, we can use Intune to deploy this using a PowerShell script: 

```PowerShell
$Path  = 'HKLM:\SYSTEM\CurrentControlSet\Control\SecureBoot'
$Name  = 'MicrosoftUpdateManagedOptIn'
$Value = 0x5944  # 22852 decimal

if (!(Test-Path $Path)) {New-Item -Path $Path -Force}
New-ItemProperty -Path $Path -Name $Name -PropertyType DWord -Value $Value -Force
```

>Note: Meeting these requirements doesn’t mean all devices update at once. Microsoft rolls out updates gradually, grouping devices by hardware and firmware, monitoring feedback, and pausing if issues appear.

Otherwise be aware that Microsoft is already working with OEM's to also push out BIOS Updates, where the updated certificates are also present. So if you are already keeping your BIOS Up-to-date in your org, chances are, you already received the updated certificates.

If your devices are in an air-gapped environmentm or with limited network connectivity, you will have to update these certificates manually. See [this article](https://techcommunity.microsoft.com/blog/windows-itpro-blog/updating-microsoft-secure-boot-keys/4055324) for more information.

The full Microsoft guidance is available [in this article](https://support.microsoft.com/en-us/topic/windows-devices-for-businesses-and-organizations-with-it-managed-updates-e2b43f9f-b424-42df-bc6a-8476db65ab2f)

## Wrapping up

Plenty of scripts online suggest you need to handle certificate updates yourself. For most organizations, that’s not the case. Microsoft will take care of it — as long as you’ve prepared properly. If you’re running hardware from major OEMs like Dell, HP, or Lenovo and keeping them updated, you’re likely already covered.

Hopefully this clears up the confusion and saves you from chasing unnecessary “DIY fixes.”

Thanks for reading — and have an awesome day :)