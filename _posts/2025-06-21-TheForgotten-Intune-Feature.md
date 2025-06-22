---
title: "The Forgotten Intune Features and Some Words on Traditional Application Management"
date: 2025-06-21
categories:
  - Intune
tags:
  - Update available apps
  - Update available applications
  - Application assignments
  - Allow available uninstall
  - Auto update available apps
---

What if I told you there’s a feature in Intune that lets you automatically update apps installed by users as *Available* via the Company Portal? Some of you might say this feature doesn’t exist, while others might say you don’t care. In my experience, I’ve seen almost no companies using this feature — well, except for one: [Robopack](https://robopack.com/), who is currently transforming automatic app management as we speak.

But why is it that so many companies haven’t heard of this feature, or more importantly: Why don't they care?

Over the past two years, we've received — in my opinion — two major improvements to Intune app management:

- **[Allow Available Uninstall](https://learn.microsoft.com/en-us/intune/intune-service/apps/apps-win32-add#step-2-program)**: When you create Win32 apps and assign them as *Available* to end users, you can allow users to uninstall the app themselves, without requiring local admin rights. This is useful both when users no longer need an app and also for troubleshooting purposes.
  
- **[Auto-update Available App](https://learn.microsoft.com/en-us/intune/intune-service/apps/apps-win32-supersedence#use-auto-update-with-app-supersedence)**: If a user installs an app from the Company Portal, you can now publish a newer version using supersedence and enable the “Auto update” checkbox for the *Available* assignment - This makes sure to automatically update the app on any users/devices that subscribed to the app. Until recently, this wasn’t possible, making *Available* app assignments a dead-end in terms of lifecycle management in a lot of scenarios.

![AutoUpdateAvailableApp](/assets/images/2025-06-24-Forgotten-IntuneFeatures/Auto-update-AvailableApp.png?raw=true "Auto update available app")
![UninstallWin32App](/assets/images/2025-06-24-Forgotten-IntuneFeatures/AllowAvailableUninstall.png?raw=true "Uninstall Win32 app")  

## The Traditional Way of Assigning Applications

Historically, application assignment has typically followed this pattern:

- **Core apps**: Apps like VPN clients, antivirus software, or the Microsoft 365 suite are deployed using the *Required* intent. These are pushed to users' devices automatically, with no option to opt out.
  
- **Optional apps**: These are often assigned via Entra ID groups (formerly Azure AD groups) or SCCM Collections, where apps are also deployed as *Required*. When a user or device is added to a group, the app installs automatically. Many organizations have built extensive processes and automations around this approach — sometimes spanning years or even decades.

### The Traditional Way for Optional Apps

Here’s a deeper look at how this works in practice:

1. **Assignment via Entra ID groups or collections**: The user or the user’s device is added to an Entra ID group or collection where the app is deployed as *Required*:
   - An app typically has two groups or collections: `AppName_Install` and `AppName_Uninstall`.
   - When a user wants to install an app, IT adds them to the install group. Later, if the user wants it removed, they are removed from the install group and added to the uninstall group.

2. **Apps follow users across devices automatically**: At first glance, this sounds great — but in reality, it often leads to a poor user experience:
   - Users can’t control the installation order. The app they need most might be the last to install.
   - Users may forget which apps were assigned to them over the years. When they receive a new device, 10–15 apps they no longer use may install automatically, wasting time and ressources.
   - Without local admin rights, uninstalling becomes a hassle. IT intervention, temporary admin accounts, or custom tooling are needed — none of which scale well in large organizations.

3. **License-awareness is lacking**: Users may end up “subscribed” to licensed apps they no longer use. Shifting to a self-service model encourages users to be more aware about the apps they subscribe to — potentially reducing licensing costs.

There are variations of this traditional approach, but the one described above is the most common I encounter. Features like **Allow Available Uninstall** and **Auto-update available apps** is not pertinent in this scenario, since apps are assigned with the "Required" intent. And that is why a lot of companies simply don't care or see these features.

## Optional Apps: Adopting a Modern Approach

Moving to a modern model of optional app deployment helps eliminate legacy baggage, improves user productivity and saves time and ressources:

1. **Empower self-service**: Let users install the apps they need via the Company Portal, on their own schedule.
   - Use features like *Allow Available Uninstall* and *Auto-update* with supersedence to provide greater flexibility for the users and for automatic application updates.

2. **Keep Required apps to a minimum**: Only enforce installation of truly essential apps needed for security, compliance, or baseline functionality. Let users choose the rest.

3. **Manage licensed apps smartly**:
   - Use tools like {Microsoft My Groups](https://learn.microsoft.com/en-us/entra/identity/users/groups-self-service-management), where users can request group membership to gain access to licensed apps.
   - Assign application owners as group approvers.
   - This shifts decision-making to the business side — IT no longer has to play gatekeeper.

## Rounding Up

Streamlining app management no longer requires IT to be the sole authority. The challenge isn’t technical—it’s about changing processes, improving communication, and shifting mindsets. Convincing stakeholders is key, but expect resistance from traditional IT staff who cling to old models and the "if it ain't broke, don't fix it" mentality. They often micromanage end-users to save minutes, while IT wastes hours maintaining outdated systems.

While many organizations remain stuck in legacy app deployment models, modern Intune features like **Auto-update for Available apps** and **Allow Available Uninstall** offer a more scalable, user-friendly, and efficient approach to app management.

Start exploring these forgotten features today — your end users (and your IT team) will thank you. :)
