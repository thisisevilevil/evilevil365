---
title: "Whats up with the Secure Boot certificates expiring in 2026?"
date: 2025-09-03
categories:
  - Intune
tags:
  - Secure Boot certificates
  - Certificate expiry
---

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

Microsoft can manage the rollout for your automatically. But for them to do that, there is 2 requirements:

**Requirement #1:** You need to ensure you are sending required or optional diagnostic data to Microsoft. If you are already using WufB or Autopatch you probably already are doing it, but know that in March 2025 Autopatch revoked the policy they deploy by default to do this on your behalf (Ref: MC996580). If you want to be sure, you can craft your own policy and apply to devices in scope. Look for the "Allow Telemetry" setting in the settings catalog. [Source](https://learn.microsoft.com/en-us/windows/deployment/update/wufb-reports-configuration-intune#settings-catalog)

![Policy](/assets/images/2025-09-03-SecureBoot-Cert-Expiration/SettingsCatalog-TelemetryPolicy.png?raw=true "Telemetry Settings Catalog Policy")

**Requirement #2:** You need to deploy a registry key for your devices to tell Microsoft that you are opting in to the automatic rollout. The reg key needs to be deployed like so:

* Registry location: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Secureboot
* Key name: MicrosoftUpdateManagedOptIn
* Key type: DWORD
* DWORD value: 0x5944

To make sure it's deployed, we can use Intune to deploy this using a PowerShell script: 

```PowerShell
$Path  = 'HKLM:\SYSTEM\CurrentControlSet\Control\SecureBoot\Servicing'
$Name  = 'MicrosoftUpdateManagedOptIn'
$Value = '1'

if (!(Test-Path $Path)) {New-Item -Path $Path -Force}
New-ItemProperty -Path $Path -Name $Name -PropertyType DWord -Value $Value -Force
```

You can also download it from my github [here](https://github.com/thisisevilevil/IntunePublic/blob/main/PowerShell%20Scripts/Deploy-SecureBoot-OptIn-Key.ps1)

>Note: Meeting these requirements doesn’t mean all devices update at once. Microsoft rolls out updates gradually, grouping devices by hardware and firmware, monitoring feedback, and pausing if issues appear.

Otherwise be aware that Microsoft is already working with OEM's to also push out BIOS Updates, where the updated certificates are also present. So if you are already keeping your BIOS Up-to-date in your org, chances are, you already received the updated certificates.

If your devices are in an air-gapped environment or with limited network connectivity, you will have to update these certificates manually. See [this article](https://techcommunity.microsoft.com/blog/windows-itpro-blog/updating-microsoft-secure-boot-keys/4055324) for more information.

The full Microsoft guidance is available [in this article](https://support.microsoft.com/en-us/topic/windows-devices-for-businesses-and-organizations-with-it-managed-updates-e2b43f9f-b424-42df-bc6a-8476db65ab2f)

>**UPDATE, 4th of September 2025**: Per Larsen has created a Remediation to check if the updated certs are already on your device, and if not, they will be flagged as "With Issue". You can find it [here](https://github.com/pelarsen/Remediation-Scripts/blob/main/SecureBootCheck.ps1)

## Wrapping up

Plenty of scripts online suggest you need to handle this certificate update yourself. For most organizations, that’s not the case. Microsoft will take care of it — as long as you’ve prepared properly. If you’re running hardware from major OEMs like Dell, HP, or Lenovo and keeping them updated, you’re likely already covered. Also, this is issue is also present on servers, so make sure to prepare accordingly for your servers as well.

Hopefully this clears up the confusion and saves you from chasing unnecessary “DIY fixes.”

Thanks for reading — and have an awesome day :)

>**UPDATE, 3rd of September 2025**: Some sources cite that all devices onboarded in [autopatch](https://learn.microsoft.com/en-us/windows/deployment/windows-autopatch/overview/windows-autopatch-overview) is automatically enrolled into the secure boot rollout, thus deploying the reg key is not necessary - But it's currently unclear. I'm chasing Microsoft for an official response and clarification regarding the scope/impact of setting the registry key mentioned in Requirement #2, if this is in fact necessary.

>**UPDATE, 4th of September 2025**: Initial response from Microsoft suggests that if you are using autopatch, you do **not** have to set the registry key as outlined in Requirement #2. It's still being clarified. Will update this blog post once I know more.

>**UPDATE, 7th of September 2025**: In a new response from Microsoft they have let us know that a more detailed blog post, documentation and FAQ is on the way. ETA is 2-3 weeks. They also underlined the staged rollout will be intentionally slow, due to the risks involved.

>**UPDATE, 21st of October 2025**: Updated documentation has now been released, you can find it [here](https://support.microsoft.com/en-us/topic/registry-key-updates-for-secure-boot-windows-devices-with-it-managed-updates-a7be69c9-4634-42e1-9ca1-df06f43f360d) - However, it's unclear if autopatch devices is automatically included, this still remains an open question. For now, it seems like it's opt-in, so only if the reg keys to opt in is set, the device will be included in the automatic rollout. They also seem to have changed the location+value of the reg key required to participate in the automatic rollout managed by Microsoft. I have updated the script with the new location+value.