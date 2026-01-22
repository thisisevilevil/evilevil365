---
title: "Use Intune and Company Portal to Install Windows OS Patches"
date: 2026-01-22
categories:
  - Intune
tags:
  - Windows Updates
  - January 2026-01 Out-of-Band patch
  - Company Portal
---

This suddenly came up out of the blue: installing or offering Windows updates manually via Intune and the Company Portal.

I’ve previously done this for customers to patch stubborn devices—for example, when Windows Update is broken or misbehaving. In those scenarios, having a Windows update available through Company Portal can be very useful for troubleshooting purposes.

In this case, however, this blog post was prompted by the **January 2026-01 Out-of-Band (OOB) patch** released for Windows 11 by Microsoft. Microsoft decided **not** to release this update through the normal Windows Update channels, meaning it is not offered via **Windows Update for Business (WUfB)**. Unfortunately, this OOB patch contains fixes that may significantly impact users—or even you as an IT admin.

## What’s in the January 2026-01 OOB patch?

For **Windows 11 23H2**, the January 2026-01 OOB patch  
([KB5077797](https://support.microsoft.com/en-us/topic/january-17-2026-kb5077797-os-build-22631-6494-out-of-band-3fb07d6a-0e35-4510-8518-4e333ed78edc))  
contains the following fixes:

1. **[Remote Desktop] Fixed:**  
   Some users experienced sign-in failures during Remote Desktop connections. This issue affected authentication steps for different Remote Desktop applications on Windows, such as the Windows App.

2. **[Power & Battery] Fixed:**  
   Some devices with Secure Launch enabled restarted instead of shutting down or entering hibernation.

For **Windows 11 24H2 and 25H2**, only the **Remote Desktop issue** appears to be relevant; the shutdown issue does not apply. If your users suddenly experience generic authentication errors when connecting to **AVD** or **Windows 365**, there’s a good chance this January 2026-01 update is the cause.

## How do we offer this patch via Intune?

Short answer: **not via WUfB**.  
We need to package and deploy it manually 🙂

---

## Packaging and offering the update in Company Portal

It is technically possible to wrap updates for Windows 11 **23H2, 24H2, and 25H2** into a single package. However, I don’t recommend this due to the size of the updates.

Instead, we’ll create **one Win32 app per Windows 11 version**, where applicable. We’ll use **requirement rules** to ensure the update is only offered to relevant devices. Using a Win32 app also ensures we can take advantage of  
[Delivery Optimization](https://learn.microsoft.com/en-us/windows/deployment/do/waas-delivery-optimization), if it’s correctly configured.

In the example below, we’ll download the update for **Windows 11 23H2**, create a PowerShell script to install it, package it, and make it available in Company Portal.

---

## Step-by-step

1. Go to the [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/) and search for the KB number of the patch you want.

2. Click **Download** and download all relevant files for the update.

   ![WindowsCatalog](/assets/images/2026-01-22-Intune-CompanyPortal-OSPatches/WufB-Catalog-Download-1.png?raw=true "Microsoft Update Catalog Download Windows update")

3. Create a PowerShell script to install the update and add the following line:

   ```powershell
   Add-WindowsPackage -Online -PackagePath "$PsScriptRoot\windows11.0-kb5077797-x64_af2b9a9ab390d2081e3e4eb52a2f8b81b5be1d7f.msu"

4. Wrap the files using the  
   [Intune Content Prep Tool](https://learn.microsoft.com/en-us/intune/intune-service/apps/apps-win32-prepare#convert-the-win32-app-content)  
   and generate an `.intunewin` file.

5. Upload the `.intunewin` file to Intune as a **Win32 app** and give it a clear, descriptive name.  
   In this example, we’re offering the **Windows 11 23H2 January 2026-01 OOB update** to Windows 11 23H2 devices.

---

## Win32 app configuration

### Install command

```PowerShell
PowerShell.exe -ExecutionPolicy Bypass -NoProfile -File Install-WindowsUpdate-23H2_2026-01-OOB.ps1
```

> **Note:**  
> To find the correct uninstall package name for a specific update, use the following PowerShell command to list installed packages:
>
> ```powershell
> Get-WindowsPackage -Online
> ```


## Requirement rule

Use a **registry requirement rule** to ensure the app is only offered to relevant devices.

**Rule configuration:**

- **Key path:**  
  `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion`
- **Value name:**  
  `CurrentBuild`
- **Requirement type:**  
  String comparison
- **Operator:**  
  Equals
- **Value:**  
  `22631`

![WindowsCatalog](/assets/images/2026-01-22-Intune-CompanyPortal-OSPatches/RequirementRule-23H2.png?raw=true "Requirement rule settings")

> **Notes:**
>
> - Windows 11 **24H2** → `26100`  
> - Windows 11 **25H2** → `26200`


## Detection rule

Use a **registry detection rule** to confirm the update is installed.

**Rule configuration:**

- **Key path:**  
  `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion`
- **Value name:**  
  `UBR`
- **Detection method:**  
  Integer comparison
- **Operator:**  
  Greater than or equal to
- **Value:**  
  `6494`

![WindowsCatalog](/assets/images/2026-01-22-Intune-CompanyPortal-OSPatches/Detection-23H2.png?raw=true "Detection")

> **Notes:**
>
> - Windows 11 **24H2 and 25H2** → use value `7627`


## Reboot behavior

Make sure to carefully configure and observe the reboot behavior.

If you want to **nudge users to reboot**, enable the following option on the Win32 app:

- **“Intune will force a mandatory reboot”**

Enabling this option unlocks the **Restart Grace Period** settings on your app assignments. This allows you to notify users and give them multiple reminders before the reboot is enforced.

> **Important:**  
> Always enable the **Restart Grace Period** when using  
> **“Intune will force a mandatory reboot”**.  
>  
> If the grace period is not configured, installing the update will result in an **immediate and abrupt reboot**, which is a very poor user experience.

![RestartApp](/assets/images/2026-01-22-Intune-CompanyPortal-OSPatches/AppInstallBehaviour.png?raw=true "Intune restart on Win32 app")
![RestartApp](/assets/images/2026-01-22-Intune-CompanyPortal-OSPatches/AppRestart-graceperiod-1.png?raw=true "Intune grace period on Win32 app")
![RestartApp](/assets/images/2026-01-22-Intune-CompanyPortal-OSPatches/AppRestart-graceperiod-2.png?raw=true "Intune grace period on Win32 app")

---

## Wrapping up

It’s far from ideal to have to offer updates this way, but I’ve received a large number of questions and concerns from customers regarding this January update. That’s why I felt compelled to write this post and demonstrate a practical approach for manually packaging and offering the update to end users.

By using a **Win32 app** and making it available in **Company Portal**, you can treat this update as a *fix-on-failure*—guiding users to install it only when needed. Of course, you can also push it proactively to all users if many are affected, but in that case, I strongly recommend increasing the restart grace period so users have more than 24 hours to complete the mandatory reboot.

That’s all for now.  
Have a great day ahead 🙂
