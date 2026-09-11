---
title: "VBscript deprecation: Replacement for slgmr.vbs"
date: 2026-09-11
categories:
  - Windows
  - Intune
tags:
  - VBScript
  - PowerShell
  - Activation
  - slmgr
  - Windows 11
---

If your organization still uses `slmgr.vbs` to activate Windows, check product keys, or read licensing status, you should start planning the change now.

I also wrote about this earlier this year in the context of the broader VBScript deprecation story: [When Removing VBScript Breaks Your AMD Chipset Driver - Welcome to the land of everything Microsoft Intune!](https://evil365.com/vbscript/VBScript-Deprecation/)

Microsoft has also published a useful detection-focused article on monitoring for VBScript usage: [VBScript deprecation: Detection strategies for Windows - Windows IT Pro Blog](https://techcommunity.microsoft.com/blog/windows-itpro-blog/vbscript-deprecation-detection-strategies-for-windows/4414325)

The dependency is easy to miss because `slmgr.vbs` has been around for ages, but it is still a common automation touchpoint in scripts, runbooks, task sequences, you name it.

## Why this matters now

Microsoft’s current guidance is straightforward:

- VBScript remains available during the transition phase, but it is no longer the preferred method.
- It will no longer be enabled by default in upcoming Windows releases - Can be eanbled with a FoD (Feature on Demand)
- Eventually, it will be removed from future Windows releases.

That means any workflow depending on `slmgr.vbs` should be identified, tested, and migrated before the environment reaches the final removal stage.

> The key point is not replace everything immediately but start inventorying and validating now so you do not get caught by the final removal. The same goes for all other VBScript usage in your environment.

## The Windows activation replacement

Microsoft’s recommended replacement for common activation automation is the `OSLicense` module in PowerShell.

The mapping is pretty clean for the most common actions:

| Task | Legacy command | PowerShell replacement |
| --- | --- | --- |
| Activate Windows | `slmgr.vbs /ato` | `Invoke-OSLicense -ActivateOnline` |
| Install a product key | `slmgr.vbs /ipk <key>` | `Invoke-OSLicense -InstallProductKey <key>` |
| View activation details | `slmgr.vbs /dlv` | `Get-OSLicenseInfo` |

This gives you a supported path without relying on a deprecated scripting host.

### Example

```powershell
# Activate Windows online
Invoke-OSLicense -ActivateOnline

# Install product key
Invoke-OSLicense -InstallProductKey "XXXXX-XXXXX-XXXXX-XXXXX-XXXXX"

# Review license status
Get-OSLicenseInfo
```

## Check support before you migrate

Before rolling this out broadly, confirm the target device supports the `OSLicense` module.

According to Microsoft’s guidance, availability differs by platform:

- Windows 11: requires the appropriate servicing update, such as the August 27, 2026 Preview (KB5120998) or later
- Windows Server: availability is planned for the next major Windows Server release, with previews available for early validation in some builds

That means your first step should not be “rewrite everything on day one.”

It should be: inventory your scripts, confirm the supported Windows builds in your estate, and validate the module in a pilot environment.

## What to look for in your environment

A lot of activation automation hides in plain sight.

Search for these references:

- `slmgr.vbs`
- `cscript`
- `wscript`
- `.vbs` files
- runbooks or task sequences that call activation commands
- custom scripts that parse activation output or use legacy VBScript wrappers

This type of dependency often exists in:

- Intune or MDM device remediation logic
- legacy deployment scripts
- helpdesk or service desk automation
- software distribution workflows
- provisioning and post-provisioning tasks
- MDT and SCCM (Yes... big time)

## Final takeaway

For activation automation, the replacement path is already available in PowerShell, as long as your devices is running the latest OS Build from September 2026 or onwards (Nobody rolls out preview patches anyway..).

If you still have some device activation automation based on VBScript in your environment, now is the time to test the `OSLicense` module and migrate it before VBScript support will be removed.

For the full Microsoft guidance in regards to VBScript deprecation, see the following links:

- [Keep Windows activation automation working with PowerShell](https://techcommunity.microsoft.com/blog/windows-itpro-blog/keep-windows-activation-automation-working-with-powershell/4540459)
- [VBScript deprecation: Detection strategies for Windows - Windows IT Pro Blog](https://techcommunity.microsoft.com/blog/windows-itpro-blog/vbscript-deprecation-detection-strategies-for-windows/4414325)
- [VBScript deprecation: Timelines and next steps](https://techcommunity.microsoft.com/blog/windows-itpro-blog/vbscript-deprecation-timelines-and-next-steps/4148301)
- [OSLicense PowerShell reference](https://learn.microsoft.com/powershell/module/oslicense)
