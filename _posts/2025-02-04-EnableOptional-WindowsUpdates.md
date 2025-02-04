---
title: "Enable Windows Devices to receive Optional (Preview) updates"
date: 2025-02-04
categories:
  - Intune
tags:
  - Cumulative Updates
  - Cumulative Preview Updates
  - Optional Updates
  - Patch Tuesday
---

Sometimes the preview updates from Microsoft can contain important fixes and changes that will (usually) be rolled out in the subsequent [patch tuesday](https://learn.microsoft.com/en-us/windows/deployment/update/release-cycle). Think of the preview updates like an appetizer of what is to come. It will usually be released at the end of each month

When you enable it, it doesn't mean it will automatically install. The users will still need to manually have to go to Windows Updates and opt-in to get the update, so it can sometimes be useful to offer it in your early rings (Test, First and Fast for instance).

So how do we enable this functionality? Intune policy of course!

## Enabling Optional updates

Head on over to Intune and perform the following steps:

1. Create a new policy -> Windows 10 and later -> Settings Catalog
2. Give the policy a nice name, I.e: Allow optional content
3. Press "add setting" and do a search for "Allow Optional Content". Press Windows Update for business and add the allow optional content setting
4. Configure the setting as follows: Automatically receive optioanl updates (including CFRs)
5. Deploy to test groups, Example: Autopatch device group Modern Workplace Devices-Windows Autopatch-Test, Modern Workplace Devices-Windows Autopatch-First and Modern Workplace Devices-Windows Autopatch-Fast

![Create Policy](/assets/images/2025-02-04-EnableOptional-WindowsUpdates/Policy-1.png?raw=true "Create Policy 1")
![Create Policy](/assets/images/2025-02-04-EnableOptional-WindowsUpdates/Policy-2.png?raw=true "Create Policy 2")
![Create Policy](/assets/images/2025-02-04-EnableOptional-WindowsUpdates/Policy-3.png?raw=true "Create Policy 3")
![Create Policy](/assets/images/2025-02-04-EnableOptional-WindowsUpdates/Policy-4.png?raw=true "Create Policy 4")
![Create Policy](/assets/images/2025-02-04-EnableOptional-WindowsUpdates/Policy-5.png?raw=true "Create Policy 5")
![Create Policy](/assets/images/2025-02-04-EnableOptional-WindowsUpdates/Policy-6.png?raw=true "Create Policy 6")

>Note: See this PolicyCSP for more details about the options of this policy: [https://learn.microsoft.com/en-us/windows/client-management/mdm/policy-csp-Update?WT.mc_id=Portal-fx#allowoptionalcontent](https://learn.microsoft.com/en-us/windows/client-management/mdm/policy-csp-Update?WT.mc_id=Portal-fx#allowoptionalcontent)

## End user experience

The end user will get the option to install the preview update by navigating to the Windows Update under settings. Only if the user opts in and install the updates does it actually install. If the user does nothing, it will simply continue to install the next cumulative update once it becomes available.

![End User Experience](/assets/images/2025-02-04-EnableOptional-WindowsUpdates/UX-1.png?raw=true "End User Experience")

I hope you found this useful. Happy testing :)
