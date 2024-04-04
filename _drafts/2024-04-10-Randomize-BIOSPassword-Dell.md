---
title: "DRAFT: Use Intune to randomize BIOS Passwords and set BIOS Settings on Dell devices"
date: DRAFT2024-04-03:DRAFT
categories:
  - Dell
  - BIOS
tags:
  - Randomize BIOS Password
  - Manage BIOS Password
  - Manage BIOS Settings
  - Endpoint Security
---

# Want to randomize BIOS password on Dell devices, using Intune? Look ahead!

A new feature has been added to Intune where we now natively can randomize the BIOS Password for Dell devices and also set BIOS Settings. This is great on so many levels, because:
* Randomizing BIOS Passwords today, requires custom tooling/custom scripting. Can be time consuming
* How should the BIOS Passwords be set and stored? Not in plain text and available for everyone, right? :)
* Streamline BIOS settings across your estate to ensure they are configured correctly.
-> I know this is possible in Dell TechDirect, but it's time consuming having to create BIOS Profiles for each model, especially in large enterprises. Also Dell TechDirect is very sluggish. Something Dell is aware of though.

Let's get into it!

## Creating the configuration profile
Let's look at the new option we now have in Intune. Head over to Intune -> Devices -> Configuration -> Create, New Policy -> Select Windows 10 and later -> Select Templates. Finally select "BIOS Configurations and other settings"

1. As of this blogs post, you can only select "Dell" in hardware. Let's hope Lenovo and HP shows up here soon as well.
2. Note the setting "Disable per-device BIOS password protection". Be aware of the reverse logic here. By default this is set to "No" meaning it will manage and randomize the password. If we select "Yes", the randomizing of the BIOS password will not be enabled.
* If you are not ready to manage BIOS Passwords as of yet, remember to select "Yes" here instead. If you roll this policy out to all of your devices and they all set a random password, be aware we currently can't modify the password strength of the BIOS Password. It has Upper/Lower Case characters, numbers and symbols as well. The latter is the tricky one, since the Keyboard language on Dell Laptops in the BIOS is by default set to US
3. Configuration file: This requires a .cctk file. And how is this done? [Dell Command | Configure!](https://www.dell.com/support/kbdoc/en-us/000178000/dell-command-configure)

![DellBIOS](/assets/images/XXXX-XX-XX-Randomize-BIOSPasswords-Dell/CreateConfigurationProfile-1.png?raw=true "BIOS Configuration Intune")
![DellBIOS](/assets/images/XXXX-XX-XX-Randomize-BIOSPasswords-Dell/CreateConfigurationProfile-2.png?raw=true "BIOS Configuration Intune")


Let us craft the .cctk file now!
Download and install Dell Command | Configure on a Dell machine. Preferably from a newer Dell Machine with an update BIOS. One installed, open Dell Command | Configure. 

Let's enable some BIOS options required by [Device Guard](https://techcommunity.microsoft.com/t5/iis-support-blog/windows-10-device-guard-and-credential-guard-demystified/ba-p/376419). That would be the following on an Intel-device:
1. [Intel Virtualization Technology](https://www.intel.com/content/www/us/en/support/articles/000005486/processors.html)
2. [Intel Trusted Execution Technology](https://www.intel.com/content/www/us/en/support/articles/000025873/processors.html)
3. [Secure Boot](https://learn.microsoft.com/en-us/windows-hardware/design/device-experiences/oem-secure-boot)

Once you have set all 3 settings to "enabled" in Dell Command | Configure, select the "export config" button.

![DellBIOS](/assets/images/XXXX-XX-XX-Randomize-BIOSPasswords-Dell/DellCommandConfigure-1.png?raw=true "Dell Command | Configure")
![DellBIOS](/assets/images/XXXX-XX-XX-Randomize-BIOSPasswords-Dell/DellCommandConfigure-2.png?raw=true "Dell Command | Configure")
![DellBIOS](/assets/images/XXXX-XX-XX-Randomize-BIOSPasswords-Dell/DellCommandConfigure-3.png?raw=true "Dell Command | Configure")

Once you have the .cctk file, you can see it's a very simple text file, just in a .cctk file format, no hocus pocus.
![DellBIOS](/assets/images/XXXX-XX-XX-Randomize-BIOSPasswords-Dell/DellCCTKFile.png?raw=true "Dell Command | Configure")
if you want to cheat and just the functionality really quickly, you can grab the .cctk file I just crafted from <a id="raw-url" href="https://raw.githubusercontent.com/thisisevilevil/evilevil365/master/assets/images/XXXX-XX-XX-Randomize-BIOSPasswords-Dell/multiplatform_202404041905.cctk">here</a>


### Verifying settings applied

## Fetching the BIOS Password

## Things to be aware of