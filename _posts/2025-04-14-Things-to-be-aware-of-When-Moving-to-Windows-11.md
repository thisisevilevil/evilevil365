---
title: "Avoid the Headache: 10 Things to Be Aware Of When Moving to Windows 11"
date: 2025-05-24
categories:
  - Windows 11
tags:
  - Windows 11 Migration
  - Windows 11 EoL (End of Life)
  - Intune Feature Update
---

The end of Windows 10 support is drawing near. October 2025 marks the last month Windows 10 will receive patches—unless you're on Windows 10 LTSC. After this date, you'll either need to pay the hefty price for ESU licenses (more about that [here](https://learn.microsoft.com/en-us/windows/whats-new/extended-security-updates)) or risk running unsupported devices, which is highly discouraged.

I’ve helped several organizations transition to Windows 11, and along the way, I’ve identified common pain points faced by small, medium, and large enterprises alike. 

Read on, to find out what I think you need to watch out for as an IT Admin as an organization.

![Thumbnail](/assets/images/2025-05-24-10-Mistakes-MigrationTo-11/BlogThumbnail.png?raw=true)

## 10. App Compatibility

I often hear, “We need to thoroughly test all of our applications before moving to Windows 11.” While this seems sensible, it usually results in stagnation. Many IT departments manage vast numbers of applications with decentralized ownership, especially in global organizations, making this approach impractical.

Microsoft is actually committed to helping companies with application compatibility issues via their App Assure program. More on that [here](https://techcommunity.microsoft.com/discussions/windows11/reminder-our-windows-11-application-compatibility-promise/3223595?).

From my experience, 9.9 out of 10 apps work just fine on Windows 11.

**My advice?** Flip the process. Start upgrading to Windows 11, and if you encounter issues, simply roll back to Windows 10 as needed on singular devices where required.

## 9. Credential Guard Breaks MSCHAPv2-Based Wi-Fi

If you use Wi-Fi profiles with PEAP-MSCHAPv2, they'll stop working on Windows 11. This is because [Credential Guard](https://learn.microsoft.com/en-us/windows/security/identity-protection/credential-guard/considerations-known-issues#wi-fi-and-vpn-considerations) is enabled by default. While Credential Guard is excellent for protecting against credential theft, it also blocks legacy protocols like MSCHAPv2.

Despite numerous [security advisories](https://learn.microsoft.com/en-us/security-updates/securityadvisories/2012/2743314) over the last decade, I still encounter organizations using MSCHAPv2.

**What to do?** Migrate to EAP-TLS. Use Intune to deploy PKCS or SCEP certificates. I recommend [PKCS certificates](https://learn.microsoft.com/en-us/intune/intune-service/protect/certificates-pfx-configure) due to their simpler configuration. Also consider [Intune Suite: Cloud PKI](https://learn.microsoft.com/en-us/intune/intune-service/protect/microsoft-cloud-pki-overview) for managing certificates.

You *can* disable Credential Guard to restore MSCHAPv2 functionality—but I strongly advise against it.

## 8. Memory Integrity Prevents Old Drivers from Loading

[Memory Integrity](https://learn.microsoft.com/en-us/windows-hardware/design/device-experiences/oem-hvci-enablement), part of [Virtualization-Based Security (VBS)](https://learn.microsoft.com/en-us/windows-hardware/design/device-experiences/oem-vbs), blocks unsigned or outdated drivers.

If a driver is blocked, users might see errors like this:

![Driver load error](/assets/images/2025-05-24-10-Mistakes-MigrationTo-11/MemoryIntegrity-DriverLoadError.png?raw=true)

Sometimes it may even trigger a BSoD, making it look like the application doesn't support Windows 11.

In such cases, disable Memory Integrity using a Settings Catalog Profile from Intune:

![Disable Memory Integrity](/assets/images/2025-05-24-10-Mistakes-MigrationTo-11/MemoryIntegrity-Disable-Intune.png?raw=true)

> **Note**: If VBS was enabled with a UEFI lock, simply disabling it via policy or registry won’t work. You'll need to either (1) clear the UEFI lock using `bcdedit` as described [here](https://learn.microsoft.com/en-us/windows/security/identity-protection/credential-guard/configure?tabs=intune#disable-virtualization-based-security), or (2) disable Secure Boot in the BIOS (not usually recommended, but sometimes necessary).

## 7. Devices in China Lacking TPM Support

This used to be a common blocker, but was lifted for commercial customers in 2021 ([see HP article](https://support.hp.com/us-en/document/ish_5031710-5031755-16)). By enabling TPM on devices supporting 2.0, it could potentially pave the way for them to move to Windows 11, provided they have a CPU that's supported.

> **Note**: You may still encounter systems where TPM is disabled in the BIOS. Some vendors (like Dell) offer tools or scripts to enable TPM remotely—even on devices with unique BIOS passwords. Here's a PowerShell script I used to help a customer with Dell devices do exactly that, available on my [GitHub](https://github.com/thisisevilevil/IntunePublic/tree/main/Packages/Dell%20Enable%20TPM%20w.%20BIOS%20Password).

## 6. Aging Hardware Without Windows 11 Support

Start replacing unsupported hardware as early as possible—especially in global organizations. Devices running specialized hardware or software may be difficult to replace, and timing can be tricky during peak seasons.

Unsupported hardware, can be singled out in Intune, Reports -> Endpoint Analytics -> Work from Anywhere -> Windows -> “Add Filter” -> Windows 11 Readiness -> Select “Not Capable”
![Unsupported Hardware](/assets/images/2025-05-24-10-Mistakes-MigrationTo-11/UnsupportedHardware.png?raw=true)

The alternative? Pay $61 per device for ESU licenses for each device in October 2025, or do nothing and run on unsupported hardware—which exposes your organization to security risks.

> **Note**: There’s a third option—forcing Windows 11 onto unsupported hardware. It’s risky and generally not recommended. See my post [here](https://evil365.com/windows%2011/ForceWindows11-Upgrade-UnsupportedHardware/) for more details.

## 5. Reinstallation Not Required for Windows 11 Migration

Some IT teams still believe users must reinstall their OS to migrate. While a clean slate sounds appealing, it’s usually unnecessary. Most of the same apps will be reinstalled anyway, wasting time and resources.

Instead, use the seamless upgrade process deployed via Intune feature update policies, leveraging Windows update to lift the device to Windows 11. On modern hardware, the upgrade process can take less than 30 minutes.

It’s time to let go of the trauma from migrating Windows 7 to 10.

## 4. Communication

Start communicating early. Let users know why this change is happening and when. Even if you’re late to the party, it’s never too late to start.

Post clear and concise announcements on your intranet (e.g., SharePoint) to build awareness and reduce confusion.

## 3. Leave Time for Troubleshooting

Some devices will fail to upgrade. Allow time to investigate and resolve these issues before October 2025. Otherwise, you may be stuck with hundreds or thousands of Windows 10 devices.

Here’s my guide on [troubleshooting feature updates](https://evil365.com/intune/troubleshooting/Troubleshoot-featureupdate-Setupdiag/) and a [script](https://github.com/thisisevilevil/IntunePublic/blob/main/Remediations/On%20Demand%20-%20Force%20Windows%2011%2024H2%20Update/Remediate-ForceWin11_24H2_Update.ps1) to trigger Windows 11 updates on-demand for testing purposes.

## 2. Let End Users Choose When to Upgrade

Microsoft now allows optional feature updates—users can choose when to upgrade. This is ideal for early adopters and minimizes disruption.

Deploy this setting via Intune. More info [here](https://techcommunity.microsoft.com/blog/windows-itpro-blog/more-flexible-windows-feature-updates/4139230).

Let users opt in early. Later, you can enforce upgrades if needed.

## 1. Avoid Manual Group Management

Manually assigning devices or users to Windows 11 upgrade groups doesn’t scale. If regional managers send upgrade lists manually, you’ll struggle to meet the deadline.

Instead, use Intune’s built-in gradual rollout or Autopatch’s multi-phase deployment. It automatically batches devices and staggers the rollout.

![Gradual Rollout](/assets/images/2025-05-24-10-Mistakes-MigrationTo-11/FeatureUpdate-GradualRollout.png?raw=true)

---

## In Summary

October 2025 is approaching fast. Here's how to break down your Windows 11 upgrade project:

1. **Communicate**: Inform your organization of the change.  
2. **Deploy Gradually**: Use Intune feature update policies or Autopatch.  
3. **Replace Hardware**: Start with unsupported systems.  
4. **Troubleshoot**: Identify and resolve upgrade blockers.  
5. **Vendor & App Support**: Engage vendors for incompatible software/hardware.

Each task can be time-consuming depending on your organization—so start now if you haven’t already.

**Hope you found this helpful. :)**