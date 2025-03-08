---
title: "Cleanup Teams Classic using Intune"
date: 2025-03-08
categories:
  - Remediations
  - Intune
tags:
  - Teams Classic cleanup
  - Teams Cleanup script
  - Security vulnerability
---

I have been assisting a customer cleaning up some old apps visible in their Defender security portal -> Security recommendations. You are probably aware that there's mostly always vulnerable apps in your organisation where CVE's has been located with variying severity. Teams Classic is one of those vulnerable apps that will commonly popup in there, and it doesn't receive updates anymore, so it needs to be dealt with.

This blog post is for my own sanity as well, since I struggled a bit to clean up teams classic (The old version of teams). Apparently there exists several different mechanisms where we can supposedly remove the classic teams, but most of them never did the job 100%. The teamsbootstrapper.exe can also cleanup Teams Classic if we use the -u switch, but I still found it didn't completely do the job. It was particularly bad at cleaning up multiple user profiles. See this screenshot below from a production environment (removed some customer-specific information with red):
![Teams](/assets/images/2025-03-08-Cleanup-TeamsClassic-UsingIntune/TeamsClassic-MultipleUserprofiles.png?raw=true "Teams CLassic Detected")

## The solution

It's actually funny, since there existed so many cleanup scripts and tools online, it moved the official documentation/teams uninstallation script from Microsoft for this topic way down the search results when searching google. And when asking Copilot or ChatGPT is would suggest blog posts from 2023, that's not completely pertinent anymore. This is the link to the official docs: [https://learn.microsoft.com/en-us/microsoftteams/teams-client-uninstall-script](https://learn.microsoft.com/en-us/microsoftteams/teams-client-uninstall-script)

Microsoft actively maintains their own PowerShell script to remove teams classic. This will also remove teams machine-wide installer if it's found. The script also picks up old versions of teams in all user profiles effectively

I opted to write a quick PowerShell script with the purpose of running it as a Remediation in Intune. This allows me to track if it actually successfully removed it or if something went wrong.

You can find the detection script I wrote, and a copy of Microsoft's own teams cleanup script on my github repo [here](https://github.com/thisisevilevil/IntunePublic/tree/main/Remediations/Cleanup%20Teams%20Classic)

### Creating the remediation

To create the remediation, go to Intune -> Devices -> Script and Remediations. Then hit "Create". Ensure to create the remediation with 64-bit enabled

![Teams](/assets/images/2025-03-08-Cleanup-TeamsClassic-UsingIntune/TeamsCleanup-Remediation.png?raw=true "Teams CLassic Remediation")

Assign it to some test devices first, before assigning to everyone. Once you are satisfied, you can assign it to all your devices.

## Wrapping up

I hope you found this useful. I have used this script in 2 different environments now with great results. The below screenshots is from the Defender Security portal where we can clearly see a large dip in exposed devices.
![Teams](/assets/images/2025-03-08-Cleanup-TeamsClassic-UsingIntune/TeamsExposure-1.png?raw=true "Teams CLassic Remediation")
![Teams](/assets/images/2025-03-08-Cleanup-TeamsClassic-UsingIntune/TeamsExposure-2.png?raw=true "Teams CLassic Remediation")

Thanks, that is all. Have an awesome day!