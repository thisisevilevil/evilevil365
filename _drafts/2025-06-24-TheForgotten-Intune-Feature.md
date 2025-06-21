---
title: "The forgotten Intune feature"
date: 2025-06-24
categories:
  - Intune
tags:
  - Update available apps
  - Update available applications
  - Feature Update Error
---

What if I told you there is a feature in Intune that lets you update apps automatically, for users that installed them as "Available" apps via Company Portal? Some of you might say this feature doesn't exist, while others will say you don't care. I encountered no companies using this feature yet, (well except for 1, that would be [Robopack](https://robopack.com/) whose currently transforming automatic app management as we speak).

But why is it that a lot of companies haven't heard of this feature or perhaps doesn't care?

If we look back the past 2 years we have actually, in my book, received 2 major features from an app-management perspective in Intune:

- "[Allow Available Uninstall](https://learn.microsoft.com/en-us/intune/intune-service/apps/apps-win32-add#step-2-program)": When you create Win32 apps and assign them as available for the end-users, we have the option to allow the user to uninstall the app themselves, without needting local admin rights. This can be useful if the user doesn't need the app anymore but also for troubleshooting purposes.
- "[Auto-update available app](https://learn.microsoft.com/en-us/intune/intune-service/apps/apps-win32-supersedence#use-auto-update-with-app-supersedence)": To update an app that has been installed as "available" by a user, user the company portal, you can add a new app and use the supersedence feature to enable the "Auto update" tickbox for the available app assignment. Up until recently, this functionality wasn't there, which made assigning apps as available non feasible.

![UninstallWin32App](/assets/images/2025-06-24-Forgotten-IntuneFeatures/AllowAvailableUninstall.png?raw=true "Uninstall Win32 app")
![UninstallWin32App](/assets/images/2025-06-24-Forgotten-IntuneFeatures/Auto-update-AvailableApp.png?raw=true "Auto update available app")

## The traditional way of assigning applications

The most traditional way of assigning apps, historically have mostly always been like so:

- **Core apps**: Core apps like VPN Software, Antivirus, Microsoft 365 Office suite gets deployed with the "required" intent, meaning end-users cannot opt-out, it will be forcefully deployed to users devices
- **Optional apps**: For each optional app, a collection or an EntraID Group exists, where the app is deployed as "required". When dropping the user or the users device into the group, it will automatically be deployed with the "required" intent. Some organisations even built extensive automation and processes around this system over the span of several years, even decades.

### The optional apps: The traditional way

By switching from the "traditional" way of assigning optional apps, and assigning them as available instead, this provides several advantages:

1. Adopting the user-self service mantra: Instead of building a lot of automation and processes on top of optional assignments, and assigning them as "required", you can let go of those dated.  
2. Apps following users to new devices automatically: Some companies this is a great idea. Assigning optional apps as required in user groups. It's not. It's the worst user experience for the following reasons:
  - The user cannot decide the order of which apps are installed - The app that they really need might be installed as the last app, making it difficult for the user to be productive
  - The user might forget what apps they are subscribing to, and during the span of several years, they suddenly have 10-15 apps assigned to them, that they forgot about. They now follow the user to a new devices
  - Unless the user has admin rights, the user will not be able to uninstall the apps themselves, unless custom tooling is developed to facilitate it, temporary admin account is provided, or worse yet, someone from IT has to log in and do it for them - This can be incredibly debilitating for IT Ressources, the larger the organisation grows
3. Licensed apps: Users might subscribe to licensed apps, that they are no longer using. By having the end-user go in and install the software themselves, it will make them more aware of consuming apps that costs money.

In the above 3 scenarios, we can now see why features like "Auto update available apps" or "Allow available uninstall" is not used at all in some companies.

### Optional apps: Adopting a modern approach

By adopting a modern approach to optional app assignment, not only can you get rid of your dated processes and you will also make your end-users much more productive.

1. Let end-user self-service by going to company portal and install the apps they required to do their jobs, at their own leisure
  - Use features like "allow available uninstall" and "Auto-update apps" through supersecence for more flexibility
2. Only assign core apps as "required" that are absolutely neccesarry for the device - Let the user decide what to install in their own time
3. For apps requiring a license, you can use functionalities like Microsoft MyGroups, and have users request memberships of groups where licenses apps are assigned as available. Assign application owners as approvers of the groups.
  - There are plenty of other ways to do this, suffice to say, IT do now have to be the arbiters here. Let the app owners decide and approve who will get the app.

## Rounding up


