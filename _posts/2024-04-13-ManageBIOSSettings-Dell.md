---
title: "Configure BIOS settings and BIOS Password on Dell devices using Intune"
date: 2024-04-13
categories:
  - Dell
  - BIOS
tags:
  - Randomize BIOS Password
  - Manage BIOS Password
  - Manage BIOS Settings
  - Endpoint Security
---

A new feature has been added to Intune where we now natively can Manage the BIOS Settings on Dell Devices whilst also randomizing and managing the BIOS Password. This is great on so many levels, because:
* Streamline BIOS settings across your estate to ensure they are configured correctly.
* Randomizing BIOS Passwords today, requires custom tooling/custom scripting. Can be time consuming, and needs to be maintained.
* How should the BIOS Passwords be set and stored? Not in plain text, right? :)

I know configuring BIOS Settings is possible in Dell TechDirect, but it's time consuming having to create BIOS Profiles for each model, especially in large enterprises. Also Dell TechDirect is very sluggish to navigate which exacerbates the problem.

Microsoft already has it's own feature for doing these things, known as DFCI (Device Firmware Configuration Interface). It's been here for a while, and currently supports Microsoft Surface, Acer, Asus, Toshiba Dynabook, Fujitsu and Panasonic. If you ask me, it would be a lot nicer if Dell would just play ball with DFCI. But for reasons I won't get into here, Dell has decided to take a different route rather than using DFCI.
Either way, to stay updated in the BIOS Configuration vs DFCI feature comparison, Microsoft has a good comparison [here](https://learn.microsoft.com/en-us/mem/intune/configuration/bios-configuration#bios-configuration-vs-dfci)

Let's get into BIOS Configuration for Dell.

## Getting started - Packaging the Dell Command Endpoint Configure Intune app
To receive the settings you send down to the device from Intune, on Dell devices, you will need to package and deploy the Dell Command Endpoint Configure Intune app. This app also has a dependency to .NET Runtime 6 for x64 Windows. You can find everything you need [here](https://www.dell.com/support/kbdoc/en-us/000214308/dell-command-endpoint-configure-for-microsoft-intune) if you want to dive into this yourself.

For the dependency .NET Runtime, know that it receives security updates frequently through Windows Update. You can always find the latest version [here](https://dotnet.microsoft.com/en-us/download). There is a small button "All .NET Version", you need to click. Then Click .NET 6.0. In the lower right corner, under the .NET Runtime section, click the x64 button to download the correct file you need. 

I also packaged version .NET Runtime 6.0.29 x64, as an .intunewin / Win32 app which you can download from <a id="raw-url" href="https://raw.githubusercontent.com/thisisevilevil/evilevil365/master/assets/dotnet-runtime-6.0.29-win-x64.intunewin">here</a>

* **Install command:** `dotnet-runtime-6.0.29-win-x64.exe /install /quiet /norestart`
* **Uninstall command:** `dotnet-runtime-6.0.29-win-x64.exe /uninstall /quiet /norestart`
* **Required disk space:** 1000MB
* **Detection, Path:** `Path: C:\Program Files\dotnet\shared\Microsoft.NETCore.App\` `File or folder: 6.0.29` `File or folder exist`

![DellBIOS](/assets/images/2024-04-13-Randomize-BIOSPasswords-Dell/NETruntime_Detection.png?raw=true ".NET Runtime Detection")

Once the .NET Runtime has been packaged as a Win32 app, let's continue to package the Dell Command Endpoint Configure as well.

Grab my .intunewin file for the Dell Command Endpoint Configure app from here <a id="raw-url" href="https://raw.githubusercontent.com/thisisevilevil/evilevil365/master/assets/Dell-Command-Endpoint-Configure-for-Microsoft-Intune_T88X8_WIN_1.2.0.76_A00.intunewin">here</a>

* **Install command:** `Dell-Command-Endpoint-Configure-for-Microsoft-Intune_T88X8_WIN_1.2.0.76_A00.EXE /s /l=C:\Windows\Logs\Dell_Command_EndpointConfigure_1.2_exe_installer.log`
* **Uninstall command:** `msiexec /X {0D30F8A0-D0A5-40C9-A9AF-CEDE89934A45} /qn`
* **Required disk space:** 500MB
* **Detection, MSI String:** `{0D30F8A0-D0A5-40C9-A9AF-CEDE89934A45}`
* **Dependency:** `Set dependency to .NET Runtime 6.0.29 - Automatically install: Yes`

![DellBIOS](/assets/images/2024-04-13-Randomize-BIOSPasswords-Dell/NETRunetime6.0.29x64-Dependency.png?raw=true "Dependency towards .NET Runtime")

Assign both the .NET Runtime and Dell Command Endpoint Configure app to scope tags and a group of your choosing. For testing purposes, you can assign it to a test group, but once it's done testing, and you are ready to roll it out to everyone, I would recommend using "All Devices" with a Dell Manufacturer filter, for swift evaluation.

### Preparing the .cctk file
To craft a BIOS Configuration rule, we need a .cctk file. .cctk files are simple little .ini-like files that simply states which BIOS Settings should be enabled or disabled, usually reserved for Dell Command Configure.
Download and install Dell Command Configure on a Dell machine. Preferably from a newer Dell Machine with an updated BIOS. One installed, open Dell Command Configure. 

Let's enable some BIOS options required by [Device Guard](https://techcommunity.microsoft.com/t5/iis-support-blog/windows-10-device-guard-and-credential-guard-demystified/ba-p/376419). That would be the following on an Intel-device:
1. [Intel Virtualization Technology](https://www.intel.com/content/www/us/en/support/articles/000005486/processors.html)
2. [Intel Trusted Execution Technology](https://www.intel.com/content/www/us/en/support/articles/000025873/processors.html)
3. [Secure Boot](https://learn.microsoft.com/en-us/windows-hardware/design/device-experiences/oem-secure-boot)

Once you have set all 3 settings to "enabled" in Dell Command Configure, select the "export config" button.

![DellBIOS](/assets/images/2024-04-13-Randomize-BIOSPasswords-Dell/DellCommandConfigure-1.png?raw=true "Dell Command Configure")
![DellBIOS](/assets/images/2024-04-13-Randomize-BIOSPasswords-Dell/DellCommandConfigure-2.png?raw=true "Dell Command Configure")
![DellBIOS](/assets/images/2024-04-13-Randomize-BIOSPasswords-Dell/DellCommandConfigure-3.png?raw=true "Dell Command Configure")

Once you have the .cctk file, you can see it's a very simple text file, in a .cctk file format, no hocus pocus.
![DellBIOS](/assets/images/2024-04-13-Randomize-BIOSPasswords-Dell/DellCCTKFile.png?raw=true "Dell Command Configure")

if you want to cheat and want to test the functionality really quickly, you can grab the .cctk file I just crafted from <a id="raw-url" href="https://raw.githubusercontent.com/thisisevilevil/evilevil365/master/assets/images/2024-04-13-Randomize-BIOSPasswords-Dell/multiplatform_202404041905.cctk">here</a>

Once you have the .cctk file, now we have what we need to craft the BIOS Configuration profile!

## Creating the configuration profile
Let's look at the new option we now have in Intune. Head over to Intune -> Devices -> Configuration -> Create New Policy -> Select Windows 10 and later -> Select Templates. Finally select "BIOS Configurations and other settings"

1. As of this date, you can only select "Dell" in hardware.
2. Note the setting "Disable per-device BIOS password protection". Be aware of the reverse logic here. By default this is set to "No" meaning it will manage and randomize the password. If we select "Yes", the randomizing of the BIOS password will not be enabled.

**_If you are not ready to manage BIOS Passwords, remember to select "Yes" here instead. If you roll this policy out to all of your devices and they all set a random password, be aware we currently can't modify the password strength of the BIOS Password. It has Upper/Lower Case characters, numbers and special characters. The latter is the tricky one, since the Keyboard language on Dell Laptops in the BIOS is by default set to US_**

3. Configuration file for BIOS settings: This requires the .cctk file we previously crafted.

> **_Currently it's not suported to have multiple BIOS Configuration profiles that sets different BIOS settings, targetted to the same device. You need to consolidate your settings into 1 profile. Also older models might support different BIOS settings compared to newer ones, so be sure to test things out before deploying broadly._**

![DellBIOS](/assets/images/2024-04-13-Randomize-BIOSPasswords-Dell/CreateConfigurationProfile-1.png?raw=true "BIOS Configuration Intune")
![DellBIOS](/assets/images/2024-04-13-Randomize-BIOSPasswords-Dell/CreateConfigurationProfile-2.png?raw=true "BIOS Configuration Intune")

### Verifying settings applied
From intune, you can verify settings has applied using the configuration profile. Note it will say "Pending" for a while, and it's a bit slow to report back when it's successfully applied, so just know that's normal. It should eventually change to "Success".
![DellBIOS](/assets/images/2024-04-13-Randomize-BIOSPasswords-Dell/BIOSConfig-1.png?raw=true "BIOS Configuration Intune")
![DellBIOS](/assets/images/2024-04-13-Randomize-BIOSPasswords-Dell/BIOSConfig-2.png?raw=true "BIOS Configuration Intune")

From the client side, you can go to %programdata%\Dell\EndpointConfigure and review the EndpointConfigure.log to verify the agent is installed along with the .NET 6 dependency correctly loads. You should eventually see in the log "Key configuration sucessful" followed by "Key applied/updated". Finally it should say "Updated Results upon successful BIOS configuration operation" 

![DellBIOS](/assets/images/2024-04-13-Randomize-BIOSPasswords-Dell/Dell_EndpointConfigureLogs.png?raw=true "BIOS Configuration Intune")

## Fetching the BIOS Password
Currently it's only possible to view the password via Graph calls. It can be a blocker for many, but luckily, we can create tools and frontends to make it easier to fetch BIOS Passwords for ServiceDesk teams, when required. 
For now, let's explore how we can fetch the BIOS Password using [Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer). Based on the [docs from Microsoft](https://learn.microsoft.com/en-us/graph/api/intune-deviceconfig-hardwarepasswordinfo-get?view=graph-rest-beta) we will need the following permissions: `DeviceManagementConfiguration.Read.All`, `DeviceManagementConfiguration.ReadWrite.All`, `DeviceManagementManagedDevices.PrivilegedOperations.All`

1. Open Graph Explorer
2. Sign-in with your account
3. If not already done, press your profile picture in the top right corner -> Press Consent To Permissions -> Scroll all the way down to "DeviceManagementConfiguration" and press "Consent" for both permissions in this category. Do the same for the DeviceManagementManagedDevices category.

![DellBIOS](/assets/images/2024-04-13-Randomize-BIOSPasswords-Dell/ConsentPermissions.png?raw=true "Consent for permissions")
![DellBIOS](/assets/images/2024-04-13-Randomize-BIOSPasswords-Dell/ConsentPermissions-1.png?raw=true "Consent for permissions")
![DellBIOS](/assets/images/2024-04-13-Randomize-BIOSPasswords-Dell/ConsentPermissions-2.png?raw=true "Consent for permissions")

**_NOTE: If you don't have the correct permissions to consent for these permissions, log a ticket to the relevant team in your org and describe the use case for why you need it. Feel free to link to this article as well, as documentation._**

4. Once done, we can use the the following Graph calls to retrieves BIOS Passwords:
* Retrieve for all devices: `https://graph.microsoft.com/beta/deviceManagement/hardwarePasswordInfo`
* Retrieve for Specific devices: `https://graph.microsoft.com/beta/deviceManagement/hardwarePasswordInfo('<InsertIntuneDeviceID>')`

![DellBIOS](/assets/images/2024-04-13-Randomize-BIOSPasswords-Dell/Graph-GetBIOSPassword.png?raw=true "Get BIOS password using Graph")

**_If you use custom roles in your org, you will also need to assign the Read BIOS password permissions under roles. Go to Intune -> Tenant Administration -> Roles -> Click your custom role -> Managed Devices -> And select "Yes" in "Read Bios password"_**
![DellBIOS](/assets/images/2024-04-13-Randomize-BIOSPasswords-Dell/ManagedDevices_RBAC_Role.png?raw=true "Get BIOS password using Graph")



## Things to be aware of/Tips
* If Wiping -> Reusing devices, Intune cannot manage the BIOS password/Settings until manually removed. Consider only using Autopilot Reset or Fresh Start, if possible.
* No support for service principals to delegate fetching BIOS Passwords. Not super relevant, unless you are creating your own tools
* Not possible to modify BIOS Password strength. The password is generated with special characters. Can be tricky to type correctly, since we cannot change keyboard language in BIOS (Default: US). 
* If you completely lost the BIOS Password (This happened to me, seriously... :D), you can contact Dell to get them to give you a password to unlock it. You will need to generate a Challenge code in the BIOS, that you need to provide Dell support, before they can generate the password for you.
* If you are into scripting, know that Dell Command Configure is deployed as part of the Dell Command Endpoint Configure Intune app, no need to deploy it seperately. It is located under C:\Program Files\Dell\EndpointConfigure\X86_64\cctk.exe - You can reference this in scripts or when troubleshooting


## Wrapping up
A part of me still wants Dell to pivot towards DFCI, but I know they had their reasons to go their own way, still working together with Microsoft though. It's still cool that we now have this option. In the past I've simply use Remediations to set BIOS settings + BIOS Passwords, but it's not super pretty to do it that way. But hey, we'll take what we can get for now :)

You can find the user guides for all of this stuff we went through, from Dell's website [here](https://www.dell.com/support/home/en-us/product-support/product/command-endpoint-configure/docs). You can also follow Microsoft's official docs for this feature [here](https://learn.microsoft.com/en-us/mem/intune/configuration/bios-configuration). I also imagine this feature will undergo a lot of changes, so I will try and keep this blog updated as it evolves, it's still very fresh.

That's it for now. I hope you found it useful.