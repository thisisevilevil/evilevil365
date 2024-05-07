---
title: "Back to Basic: ProActive remediations"
date: 2024-05-XX
categories:
  - Intune
tags:
  - ProActive Remediations
  - Remediation
  - PowerShell
---

ProActive remediations or just [remediations](https://learn.microsoft.com/en-us/mem/intune/fundamentals/remediations) as it has now been renamed to, has been around for a while now, but even today when I visit customers, I still see a lack of awareness or basic understanding of them. Nevertheless, this post will take us back to basics in regards how ProActive Remediations work, so you can get started writing your own. If you are already savvy with PowerShell, you will find it's super easy to get into. But fret not, if you are not good with PowerShell, you can often find assistance using CoPilot or googling to find some good remediations out there. I have written loads of ProActive remediations over the years, and in the end of this post, I will share the ones that I can, that are not customer specific, for some inspiration as well.

Let's dive into it! :)

# Getting started
There are some pre-reqs to using Remediations in Intune, I won't list them all here, but basically you will need E3, E5 or A3 or A5 licenses in your tenant. Microsoft has chosen to not offer this to customers using the Business licensing SKUs. You can find all the pre-reqs [here](https://learn.microsoft.com/en-us/mem/intune/fundamentals/remediations)

When you create Remediations, you can't copy/paste the code directly into the editor in Intune, you will need to have them as .ps1 files on your drive. 

Let's pretend you just want to check for a condition on your devices, without actually changing anything, and then have it report back if that condition is not met. Then you would just deploy a remediation with a detection script only, without a remediation. Here is some simple code you can use to check if a folder exists, and if yes, return "With issue":
```
if (Test-path C:\Drivers) {Write-output "Drivers folder has been located ; exit 1}
```
Note the "exit 1". Anything that exits with an error code of 1 is considered as "With issues" so this is key to ensure the detection script returns the correct error code so Intune knows that device is "With Issue".

Let's save this script as a .ps1 and create a new remediation in Intune. Give it a nice name, and always make sure "Run in 64-bit context" is ticket. In this case we also don't need to emulate the logged-on user so ensure that is disabled.

Deploy it to a test group and scope tag of your choice. Also note we can have this script run on a specific frequency. Let's just chose the default on this one: 1 day.

Anything you can think of with PowerShell to check for, the sky is the limit here.

But what if you also wanted to run a remediation in attempts to fix the condition?

## Creating a remediation script
Let's pretend you also want your remediation to actually fix the issue it's detecting for. Well, that's also relatively simple, depending on what it is you want to fix. Let's follow our example with the folder detection.

Let's say we wanted to delete the folder to save disk space, then you can use the below code. Save this script to a .ps1 file and activate the remediation as well.

```
if (Test-path C:\Drivers) {Remove-Item C:\Drivers -recurse -force}
```


## Wrapping up
I hope you found this useful, and it's enough to get you started with remediation. The advantage remediations have over PowerShell (Platform scripts) in Intune is the following
* Run your scripts on a specific schedule
* Usage of device filters when assigning to groups
* Use "output" view in the remediation to print useful information


## Reference Remediations

| Remediation Name| Detection | Remediation | Purpose |
|-----------------|-----------|-------------|---------|