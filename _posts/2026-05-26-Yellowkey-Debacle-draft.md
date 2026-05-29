---
title: "The Yellowkey debacle"
date: 2026-05-29
categories:
  - Intune
tags:
  - Yellowkey mitigation
  - Yellowkey vulnerability
  - Bitlocker vulnerability
  - Remediation
---

It's been almost 2 months since the individual known as "Nightmare-Eclipse" aka "Chaotic Eclipse" released 6 zero day exploits, and I've been closely monitoring the debacle as it unfolded. On one side you have a disgruntled security researched who firmly believes Microsoft has wronged him, and on the other side you have Microsoft with a no-bs approach saying normal protocol wasn't followed and making the source code for all of these exploits publicly available is ruthless and irresponsible. They also even went so far to ban the github repo where all of the source code was initially hosted. 

You can read more about the details [here](https://blog.barracuda.com/2026/05/19/nightmare-eclipse-zero-days-grudge) - The events are still unfolding, and I don't think we've seen the last of it yet. Microsoft released this blog post 2 days ago (as of todays date), where they are clearly distancing themselves from his kerfuffle, and according to them the security researcher is 100% to blame. You can read Microsoft's response [here](https://www.microsoft.com/en-us/msrc/blog/2026/05/a-shared-responsibility-protecting-customers-through-coordinated-vulnerability-disclosure).

Be that as it may, but what about the bitlocker exploit?

## What is the yellowkey exploit

The yellowkey exploit is a Bitlocker Bypass attacked, that allows an attacker with physical access to a device, to gain access to the data on a hard-drive even though it's encrypted with bitlocker. Mechanically, it involves placing specially crafted 'FsTx' files on a USB drive or EFI partition, rebooting into WinRE, and triggering a shell by holding down the CTRL key. It bypasses TPM-only BitLocker in particular, and the critical constraint is physical access. It cannot be abused remotely.

So it's not super good, in fact I would go as far as to say it's pretty bad. One of the main features of bitlocker is data-theft protection in case a device gets stolen.

It's worth noting that this vulnerability only affect Windows 11 24H2 and higher. It does not affect earlier version of Windows 11 or Windows 10.

So what can we do about it?

## Mitigation using Intune

Microsoft has released a PowerShell script as an interim mitigation that mitigates the attack, until an official patch is released via normal channels. You can find it in their security update report for this incident [here](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2026-45585). The fix is relatively simple: Mount the WinRE partition and remove the Autofstx.exe executable.

The PowerShell script can be deployed using intune as a platform script, but there is no detection or logging mechanism, so if it fails you won't know why. I recommend heading over to James Vincent blogpost about this and grab his remediation scripts instead, yuou can find his blog post [here](https://jamesvincent.co.uk/2026/05/21/mitigate-yellowkey-exploit-cve-2026-45585-using-intune/)

Download both the [detection](https://github.com/jamesvincent/Intune/blob/main/CVE-2026-45585/Detect-CVE-2026-45585.ps1) and the [remediation](https://github.com/jamesvincent/Intune/blob/main/CVE-2026-45585/Remediate-CVE-2026-45585.ps1) script.
![Scripts](/assets/images/2026-05-26-Yellowkey-Debacle/Github-Scripts.png?raw=true "Github Scripts")

>Note the logging in the scripts: Both the detection script and the remediation scripts logs to the IntuneManagementExtensionFolder under C:\ProgramData\Microsoft\IntuneManagementExtension\Logs

Once downloaded, I also recommend you create a new device filter in Intune, to make sure we can target devices that's running Windows 11 24H2 or Windows 11 25H2. In Intune, navigate to Devices > Assignment Filters and create a new filter. Use the "osVersion" property and starts with "10.0.26100" and then add an "or" rule and add an addition "osVersion" property and starts with "10.0.26200". It should end up looking like this: `(device.osVersion -startsWith "10.0.26100") or (device.osVersion -startsWith "10.0.26200")`
![Scripts](/assets/images/2026-05-26-Yellowkey-Debacle/DeviceFilterExample.png?raw=true "Device Filter")

Now head over to Intune > Scripts and Remediations and finally click "Create". Give it a nice name, i.e: "Yellowkey mitigation". Then add both the detection and the remediation script. Make sure to set "Run script in 64-bit PowerShell" to "Yes". Click next, and apply scope tags if required, then hit next. Now for the important bit: I recommend you assign to a few devices to test first, before starting the broad rollout. If you are already using autopatch, you will already have these rings. Just search for "Autopatch" and you should find the corresponding autopatch entra groups matching your rings.
![Scripts](/assets/images/2026-05-26-Yellowkey-Debacle/Remediation-1.png?raw=true "Remediation")

Adjust the "Schedule" so it only runs every 7 days or similar. There is no reason to have it run daily. Then make sure to apply the Windows 11 24H2 or Higher filter you created earlier.
![Scripts](/assets/images/2026-05-26-Yellowkey-Debacle/Remediation-2.png?raw=true "Remediation")

You are now ready to start deploying to a test and an early ring. Once you are happy with the results you can gradually continue the rollout by adding your remaining rings. Alternatively if you don't really care about testing and you just want to immediately deploy the mitigation to everyone, you can just deploy to "All Devices" with the Windows 11 24H2 or Higher filter.

## That's it for now

I hope you found it useful. Remember to only deploy to Windows 11 24H2 or higher, or you will most likely see an error when trying to remediate, such as `REAGENTC.EXE: Operation failed: c142011c REAGENTC.EXE: An error has occurred.`

I've used this approach at 2 customers already with great results: We got a few errors in the beginning, because we forgot to add the device filter, so Windows 10 and earlier version of Windows 11 devices were targetted as well.
![Scripts](/assets/images/2026-05-26-Yellowkey-Debacle/Remediation-Results.png?raw=true "Remediation")

Have an awesome day ahead :)