---
title: "SENSE Client missing on OEM Builds for Windows 11 24H2"
date: 2024-10-12
categories:
  - Intune
  - Windows 11 24H2
tags:
  - SENSE Client
  - Windows Defender
  - Microsoft MDE
  - Security
---

There has been some chatter about this already, and I also covered this in a [previous blogpost](https://evil365.com/windows%2011/Windows11_24H2_NotableThings/) some weeks ago. However, I now have 1 customer that keeps receiving new Windows 11 24H2 devices directly from Dell without the SENSE Client enabled. The SENSE Client is required for Microsoft Defender onboarding. If it's not enabled, a Defender onboarding profile will not work, and will be evaluated with "Not Applicable" when trying to send it down from Intune.

So this is just a quick blog post detailing how you can use Intune to deploy a Remediation to easily enable it where it's missing.

## Detection Script

```PowerShell
$sensecheck = Get-WindowsCapability -online -name 'Microsoft.Windows.Sense.Client~~~~'
if ($sensecheck.sate -eq 'Installed') {Write-output "SENSE Client correctly installed, no actions performed"}
    else {Write-output "SENSE Client is not installed.. proceeding to remediation" ; exit 1}
```

## Remediation Script

```PowerShell
Add-WindowsCapability -online -name 'Microsoft.Windows.Sense.Client~~~~'
```

Deploys with the following settings:

* Run this script using the logged-on credentials: No
* Enforce script signature check: No
* Run script in 64-bit PowerShell: Yes

## Targeting

Assing the remediation to all Windows 11 24H2 Devices. We can create a new Device Filter in Intune. Create the new filter with the following properties:

* (device.osVersion -contains "10.0.26100")

![Device Filter](/assets/images/2024-10-12-SENSEClient-Missing-Windows11-24H2/DeviceFilter_24H2.png?raw=true "Device Filter for Windows 11 24H2")

Once this is done, we can assign the new remediation to "All Devices" with the newly created Windows 11 24H2 Device Filter.

![Remediation](/assets/images/2024-10-12-SENSEClient-Missing-Windows11-24H2/AssignRemediation.png?raw=true "Assign Remediation")

That's all. This will enable the SENSE Client.

>Note: If you are using WSUS or you have devices that's on a closed network, you might need to specify a local source where to fetch the source file. See the official Microsoft docs for Add-WindowsCapability [here](https://learn.microsoft.com/en-us/powershell/module/dism/add-windowscapability?view=windowsserver2022-ps)