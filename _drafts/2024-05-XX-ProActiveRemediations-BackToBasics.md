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

ProActive Remediations or just [Remediations](https://learn.microsoft.com/en-us/mem/intune/fundamentals/remediations) or just remediations, has been around for a while now, but even today when I visit customers, I still see a lack of awareness or basic understanding of them. Nevertheless, this post will take us back to basics in regards how Remediations work, so you can get started creating your own. If you are already savvy with PowerShell, you will find it's super easy to get into. But fret not, if you are not good with PowerShell, you can often find assistance using CoPilot or googling to find some good remediations out there. I have written loads of Remediations over the years, and in the end of this post, I will share the ones that I've made over the years for some inspiration as well.

Let's dive into it! :)

## Getting started

There are some pre-reqs to using Remediations in Intune, I won't list them all here, but basically you will need E3, E5 or A3 or A5 licenses in your tenant. You can find all the pre-reqs [here](https://learn.microsoft.com/en-us/mem/intune/fundamentals/remediations)

When you create Remediations, you can't copy/paste the code directly into the editor in Intune, you will need to have them as .ps1 files on your drive.

Let's pretend you just want to check for a condition on your devices, without actually changing anything, and then have it report back if that condition is not met. Then you would just deploy a remediation with a detection script only, without a remediation. Here is some simple code you can use to check if a folder exists, and if yes, return "With issue":

```PowerShell
if (Test-path C:\SWSetup) {Write-output "SWSetup folder has been located" ; exit 1}
```

Note the "exit 1". Anything that exits with an error code of 1 is considered as "With issues" so this is key to ensure the detection script returns the correct error code so Intune knows that device is "With Issue".

Let's save this script as a .ps1 and create a new remediation in Intune. Give it a nice name, and always make sure "Run in 64-bit context" is ticked. In this case we also don't need to emulate the logged-on user so ensure that is disabled.

Deploy it to a test group and scope tag of your choice. Also note we can have this script run on a specific frequency. Let's just chose the default on this one: 1 day.

Anything you can think of with PowerShell to check for, the sky is the limit here.

But what if you also wanted to run a remediation in attempts to fix the condition?

## Creating a remediation script

Let's pretend you also want your remediation to actually fix the issue it's detecting for. Well, that's also simple in this case. Let's follow our example with the folder detection.

Let's say we wanted to delete the folder to save disk space, then you can use the below code and add it as a remediation script. Save this script to a .ps1 file and activate the remediation as well.

```PowerShell
if (Test-path C:\SWSetup) {Remove-Item C:\SWSetup -recurse -force}
```

When you create the remediation you will also be asked how often you want to run this remediation. Every hour, every day, weekly etc.


it's only your PowerShell skills that will set the limit here.

## On Demand Remediations
IF you dont want to wait for your remediation to trigger because of testing reasons or just for ad-hoc problem solving, Intune supports runnings Remediations on-demand. Go to any supported Windows device in Intune -> Press the elipses (3 dots) -> Run remediation on demand (preview)

You should now see all your remediations, which you can run on demand. Previously this was very slow to trigger, but this has since then been resolved so it's now super fast. The device just needs to be turned on and online, and usually it triggers within 1-2 minutes after running the remediation.

If you just want to utilize your remediation On Demand, then don't assign it to any group, just leave it unassigned. It will still be visible under Remediations. Also consider making remediations available for ServiceDesk personnel for ad-hoc problem solving. You can utilize scope tags and custom roles in Intune to only show select Remediations for ServiceDesk, when running them On Demand, which can be a great tool and time saver for them, rather than hopping on to the users device manually in a remote session.

## Wrapping up
I hope you found this useful, and it's enough to get you started with remediations. The advantage remediations have over PowerShell (Platform scripts) in Intune is the following

* Run your scripts on a specific recurring schedule
* Usage of device filters when assigning to groups
* Use the "output" view in the remediation to print useful information to the Intune console
* Supports running [on-demand remediations](https://learn.microsoft.com/en-us/mem/intune/fundamentals/remediations#run-a-remediation-script-on-demand-preview)

## Reference Remediations

You can always check my github for inspiration regarding remediations. You can find them [here](https://github.com/thisisevilevil/IntunePublic) - Some of them is pretty derpy and was created a long time ago, but I hope you find them useful to inspire you to get started to creating your own. :)