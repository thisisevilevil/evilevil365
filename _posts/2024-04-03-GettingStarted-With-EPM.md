---
title: "Getting started with EPM - Endpoint Privilege Management"
date: 2024-04-03
categories:
  - Microsoft Intune Suite
  - Getting Started
tags:
  - EPM
  - Endpoint Privilege Manager
  - PAM Solution
  - Local Administrator
  - Endpoint Security
---

# What is EPM?
EPM, short for [Endpoint Privilege Management](https://learn.microsoft.com/en-us/mem/intune/protect/epm-overview), is Microsofts new tool from the Intune Suite to control, audit and manage administrators rights on windows endpoints (MacOS soon to come as well!). This is also under a category of what we refer to as a [PAM Solution for Endpoints](https://www.microsoft.com/en/security/business/security-101/what-is-privileged-access-management-pam#:~:text=Privileged%20access%20management%20(PAM)%20is,privileged%20access%20to%20critical%20resources). Having a PAM Solution for endpoints is absolutely vital to control and audit the usage of elevating processes.

Some organizations have chosen to remove local admin rights altogether, but there can be certain times where users needs these admin rights to do their job, i.e: Changing system settings for development purposes, installing apps not present in the existing app catalogs or just general supportability, the list is can be endless. 

If we don't have a good and secure way to facilitate elevated privileges on behalf of the end-user, guess what? They are going to log a ticket to ServiceDesk or they are going to resort to shadow IT. That's where the good PAM Solution comes in. Not only will it eliminate the need to log a ticket to ServiceDesk, it will also still be able to facilitate that elevation without compromising security.

EPM is ever changing, and expect big changes coming this year, so keep checking back on this blog, once new features get added, as I will keep this one updated :)

> **_NOTE:_** **LAPS is not a PAM Solution - It is highly undesirable from a security perspective to use LAPS as a general means to elevate processes on end-users devices, unless it's for emergency/break-glass purpose**


**At it's core, EPM will give you the following functionalities:**
- Allow standard users to elevate processes in a guarded environment
- Eliminating one-time installs of software
- Reporting/Auditing across your estate of Administrator usage!
- Support for Windows - Mac will come at a later stage.

# Getting Started with EPM
Getting started with EPM is very simple, and only take a few clicks. But before we do anything ensure you have the correct licensing by heading to the Intune Portal -> Tenant Administration -> Intune Add-ons. Ensure either Microsoft Intune Suite or Endpoint Privilege Management is active! When it's correctly activated, you will get an extra pane under Endpoint Security called "Endpoint Privilege Management"
![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-Button_Small.png?raw=true "EPM Button in Intune")

Head over to the Endpoint Privilege Management section and press the "Create Policy" button and select the "Elevation Settings Policy". This is where we can craft a policy to enable EPM. Give it a friendly name, i.e: Default EPM Elevation Policy
![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules.png?raw=true "EPM Button in Intune")
![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-DefaultEPMElevationRules.png?raw=true "EPM Button in Intune")

At the next section, this is basically the on/off lever for EPM. We leave all of the settings on the default settings. Most of them is self-explanatory, but the most important one here is the "Default elevation response". This is super important to note, as this will have an important effect. 
If you choose "Not Configured", this means it will always default to "Deny all requests" for end-users UNLESS there is an elevation rule crafted for a given process. The other option called "Require user confirmation" is the most relaxed, as this allows the end-user to simply confirm the risks of an elevation and proceed to elevating a process without needing a specific elevation rule to be crafted in the first place and doesn't require any kind of approval from IT. 

The last option called "require support approval", allows the end-user to request elevations of files, regardless of whether there is a rule for a process crafted. Then someone from IT with the necessary access, can choose to approve/deny the elevation request.

For now, let's just choose "Not Configured" and leave everything as the default. Then assign a scope tag if required and finally assign to a test device of your choice using a group or a device filter.

![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-ConfigurationSettings.png?raw=true "EPM Policy")

> **_NOTE:_** **Microsoft recommends choosing "Not Configured" as the default elevation requests, for the majority of your users, to ensure full control of how administrator rights is used in your organizations endpoints!**

## The nuts and bolts
As soon as you assign the EPM policy to your test device, shortly after, the device should automatically get the EPM Client installed. It works the same way with the Intune Management Extension: If you have any PowerShell Scripts, Remediations, Win32 or Windows Store apps assigned to the device, the Intune Management extension will also automatically install. No need to manually maintain these binaries, Microsoft will do this for you!
Once the client installs, the most obvious things you will notice is of course when users right click, they will notice "Run with elevated access". The EPM Client will also install under the folder C:\Program Files\Microsoft EPM Agent whilst it will also install a service which will leave a process in task manager as well.

![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-Folder.png?raw=true "EPM Folder")
![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-Service.png?raw=true "EPM Service")
![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-RunWithElevatedAccess.png?raw=true "EPM RunWithElevatedAccess")

You will notice if you elevate anything at this stage with the current policy we assigned, the user would simply be denied. Remember we set the "Default elevation response" to "Not Configured" meaning the user can only elevate apps that we create specific rules for. We will come around to crafting elevation rules for specific apps in a moment.

![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-NotAllowed.png?raw=true "EPM Not Allowed")

> **_NOTE:_** **Microsoft has created a new channel for delivering EPM Policies, that is super fast! EPM is the only tool to use this new channel for now as of this blogs date, but not long from now, other elements of Intune will also migrate to this new channel to deliver policies. Depending on who you ask, they will refer to this as being "Dual Enrolled" since we now have 2 seperate channels for policies - Super nice!**

## Crafting an elevation rules policy

### Craft elevation rule to allow 7zip 24.03 beta .exe using file hash
Now we will craft our first elevation rule policy to allow 7zip. The elevation rule will use the filehash to allow the elevation. When using filehash, you achieve the strongest detection for a specific process as this is unique for every single file. That way, we ensure only the specific process we specify, will be allowed to run with elevated rights.

I will take you through every step of creating the policy.

Head on over to Intune -> Endpoint Security -> Endpoint Privilege Management. Press the "Create Policy" button, choose Windows 10 or later and choose "Elevation Rules Policy". GIve it a nice name like "Default Elevation Rules" and then press "edit instance"
![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-1.png?raw=true "EPM Elevation Rules")
![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-2.png?raw=true "EPM Elevation Rules 2")

**In elevation type, we have the following options:**
1. **User Confirmed**: User will have to confirm when elevating an app
2. **Automatic**: App will elevate without user confirmation. Best practice is to only use this for legacy apps that doesn't work with User Confirmed or support approved
3. **Support approved**: IT Support will approve elevation requests before users can elevate a process

For now, let us choose User Confirmed.

**In validation we have 2 optional boxes we can tick:**
1. **Business Justification**: User will have to present a business justification before being allowed to elevate a process
2. **Windows Authentication**: User will have to authentication using their login to Windows. This can be a password, but also works for Windows Hello and FIDO Keys

For now, let us only choose Windows Authentication:
![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-3.png?raw=true "EPM Elevation Rules 3")

Lastly to finish specifying our elevation conditions, we need to select child process behaviour. Be careful with this option, as lot of installers spawn subprocesses to install dependencies. Let's us expand a bit on the options:
1. **Allow all child processes to run elevated**: All processes spawned by the elevated process, will be allowed to elevate. This is also the default in Windows when you right click a process and select "Run as administrator". You will find that a lot of app installers rely on this functionality, so if you don't choose this, be sure to test it thoroughly!
2. **Require rule to elevate**: The subprocess will need a specific rule to elevate, before it can be run. While this is more secure, be careful choosing this one, unless for processes like cmd.exe or PowerShell.exe - Otherwise you will find yourself having to craft a lot more elevation rules that you might initially have signed up for.
3. **Deny All**: Most secure, but see notes in option #2.
4. **Not Configured**: Will revert to the default response which is "Require rule to elevate"

Let's select "Allow all child processes to run elevated" for now:
![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-4.png?raw=true "EPM Elevation Rules 4")

Finally we need to provide some file information. Since we are using file hash to craft this elevation rule, we will set "Signature Source" to "Not Configured". Notice when we set it to not configured, then we only need to give it a filename and a filehash. Getting the filename is easy, but to get the filehash, we have a few different methods at our disposal. Let's open a PowerShell and use the Get-FileHash  cmdlet

![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-6.png?raw=true "EPM Elevation Rules 6")
![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-7.png?raw=true "EPM Elevation Rules 7")

Once you have extracted the filehash, you should have a policy that looks like the following:
![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-8.png?raw=true "EPM Elevation Rules 8")

Once you are satisfised with everything, lets take our new elevation rule for a spin. Assign your scope tags where required, then assign your elevation rule to your device. then on your device, perform a sync from company portal, it can take 5-10 minutes before the policy is applied on your device.

> **_NOTE:_** **EPM has it's own PowerShell module for troubleshooting/debugging purposes. cd to C:\Program Files\Microsoft EPM Agent\EpmTools and run import-module .\epmcmdlets.dll - then run Get-Policies -PolicyType ElevationRules to see if any elevationrules has applied to the device. If nothing is returned, policies has not yet applied. More information [here](https://learn.microsoft.com/en-us/mem/intune/protect/epm-overview#install-the-epmtools-powershell-module).**
> 
>![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-GetPolicies-ElevationRules_extra.png?raw=true "EPM Elevation Rules PowerShell")

Now we can go to the [7zip website](https://www.7-zip.org/download.html) and download 7zip 24.03 beta .exe on our test device. Right click the file and press "run with elevated access". Note when launching the file, the user will first be prompted to confirm the elevation (User Confirmation), and then after confirmation the user will be asked to verify their identity, just as we configured the rule. Then finally the user should be able to install 7zip without a hitch :)

![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-Launch7Zip-3.png?raw=true "7zip installed")

> **_NOTE:_** **Windows Security defaults dictates that files downloaded from the internet must first be unblocked. Use the Unblock-File cmdlet in PowerShell or simply right click the file -> Select Properties -> And tick "Unblock" in the bottom fo the file. If you don't do this, EPM will not be able to launch the file! I have asked Microsoft/EPM team to make sure EPM gives a better error message for this particular error**
>
> ![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-UnblockFile.png?raw=true "EPM Blocked File")
> ![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-UnblockFile-1.png?raw=true "EPM UnblockFile")

### Craft elevation rule to allow Adobe Reader using Signing Certificate
In our elevation rule for 7zip, we used a file hash for detection. Now we are going to create an elevation rule using a signing certificate instead. Using a signing certificate adds an extra layer of security, as it will reference a Windows API to verify the certificate validity and revocation status. 
Let's say we allowed Adobe Reader to be elevated, using a certificate. But after 1 week, Adobe decides to revoke the certificate for security reasons. That will prevent any further elevations using EPM for any elevation rules that uses that signing certificate, until both the executable and the certificate is replaced..

But how do we know if an executable is signed? Simply right click the file and press properties. If a "Digital Signatures" tab is present, the file is signed. 
Next up, is to validate if it's signed by a valid certificate. Press the "Details" button. First to check is the "Digital signature information" if it's ok. Next up is the validity period by pressing "View Certificate". In the below example you can see I have downloaded an english version of adobe reader where everything looks ok.

![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-Adobe-1.png?raw=true "Adobe Reader File information")

So now we have to craft a rule to allow this app, also based on the signing certificate. Let's import the signing certificate into the endpoint manager portal as a reusable setting. This allows us to easily reuse it for multiple rules, without having to import it over and over. So what is the easiest way to export the signing certificate from a file? EPM PowerShell cmdlets to the rescue! So like before, we need to open an elevated PowerShell on a device with EPM already installed. Then we need to load the epmcdmlets.dll once more like before, and this time we need to run the Get-FileAttributes cmdlet.

`C:\Windows\system32> Import-Module 'C:\Program Files\Microsoft EPM Agent\EpmTools\EpmCmdlets.dll'`

`C:\Windows\system32> Get-FileAttributes -FilePath C:\Users\ChristieCline\Downloads\Reader_en_install.exe -CertOutputPath C:\Users\ChristieCline\Downloads\`

![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-Adobe-2.png?raw=true "Adobe Reader File information")

Now we have what we need to create our elevation rule. Head on back to Intune -> Endpoint Security -> Endpoint Privilege Management. Press the "Reusable settings" button and finally hit the "add" button. Give it the name "Adobe signing certificate". Finally import the exported Adobe Signing Certificate, should be the one ending with the number 4.

![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-Adobe-3.png?raw=true "Adobe Reader Elevation Rules")
![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-Adobe-4.png?raw=true "Adobe Reader Elevation Rules")
![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-Adobe-5.png?raw=true "Adobe Reader Elevation Rules")

Now it's time to craft the actual rule. Let's go in and edit our old "Default Elevation Rules" and add a new entry. Give it a nice name. Set Elevation Type "User Confirmed", and in validation set it to "Business Justification" this time. Child Process Behaviour should be set to "Allow all child processes to run elevated"

![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-Adobe-7.png?raw=true "Adobe Reader Elevation Rules")

Now for the interesting bit, so pay attention here, as this is really important!
1. In the certificate section press the add or remove a certificate. This will open a new view, where you should be able to select the signing certificate we imported before. Then hit select in the bottom
2. In certificate type, select "Publisher". Because remember we selected the signing certificate of Adobe, which is a Publisher certificate. This is the most commonly used scenario. Very rarely would we ever choose to whitelist certificate authorities, as this could cause unpredictable and unexpected elevations using EPM!
* Once you have added the signing certificate, you can save the rule already. But consider the impact of this: All files signed with this exact Adobe signing certificate, can be elevated with admin permissions, using EPM. Is this what you want?
* Consider adding more attributes. If you only want to allow Adobe Reader for instance, you can consider also adding a file name. However, also consider this: Simply adding the file name, then the user can download another adobe product, and simply rename the installer to that given filename, and elevate that file. So if you only want a specific app, consider adding more metadata for the file! Think Get-FileAttributes again
3. After adding the adobe signing certificate, let's save and apply the elevation rule without adding a name or a path, only the certificate.

Sync from company portal and wait a few minutes to ensure you get the new policy. When you are ready, right click reader_en_install.exe and press "Run with elevated access". If it's working correctly, you should get the EPM Screen, but now the user only needs to put in a business justification, and will then be able to complete the installation.

![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-ElevationRules-Adobe-8.png?raw=true "Adobe Reader Installation")


# Wrapping up
In this blog post we learned the following:
* What is EPM and what can it currently offer
* What is a PAM Solution
* How does EPM Deploy and work on the Windows Endpoint
* How to craft elevation rules, using the different levers and toggles at our disposal

Thanks for sticking around, and I hope you found it useful :)

## Things I didn't cover in this blog that you can check out or should know:
1. **Support Approved**: Try to craft an elevation rule and then choose "Support approved" and go through and elevation so you can see the flow. This can save you valuable time for one-time configuration changes and one-time installs requests. Why have ServiceDesk spend time doing this manually, when you can empower the end-user to do it themselves? See more about Support approved [here](https://techcommunity.microsoft.com/t5/microsoft-intune-blog/endpoint-privilege-management-adds-support-approved-elevations/ba-p/4101196)
2. **Reporting**: Under the Endpoint Privilege Manager section in Intune, you can press "Reports" to see reports of how EPM and Administrator rights is used in your org. 
TL;DR: Managed Elevation = An elevation rule facilitated an EPM elevation on an endpoint. Unmanaged Elevation = A local administrator on the device elevated a process, not using EPM.
3. **Secure Virtual Account**: When you elevate a process it is elevated in a virtual account that EPM creates. it goes like so: MEM\<domain>_username_$. This is for security reasons. This basically means it's a seperate userprofile. So consider the impact of this, when you are using apps that are installed in the current-user context or that store settings in the current user context. If you elevate a process with EPM, that could cause issues with some apps.
* The EPM team has also explained this is done because EPM and UAC is 2 different technologies. EPM doesn't integrate or communicate with UAC today, EPM is a seperate functionality. This might change soon though, but don't expect this secure virtual account to go away.
4. **Missing elevation handlers in OS**: The "Run with elevated access" button is only visible currently when you right click .exe files in the file explorer. You cannot elevate any other files like .MSIs or use the "Run as administrator" button from the start menu. You can also not uninstall programs using the add/remove programs, or change system settings from the settings apps. Most of these shortcomings we can apply a workaround where we simply run PowerShell.exe as elevated, and then do what we need to do, but this is not really flexible or user-friendly.
Microsoft is aware of this issue, and it's multi-faceted, and has let us know that the EPM team is working closely with the Windows team to improve this during 2024 to become more user friendly.
5. **Windows Store Apps requiring elevation doesn't work**: It's currently not possible to install most Windows Store apps that requires local admin right. Granted, it's not a lot of apps that require this, but it's something that the EPM team is aware of.
6. **"Run with elevated access" button is sometimes missing on new installs**: If the button is missing and you have verified EPM is installed and running, first try and reboot the device. If it's still not present, navigate to this path: C:\Program Files\Microsoft EPM Agent\EPMShellExtension
* Run the file EpmShellExtension.msix as the current user, not as an administrator. Once opened, it should say that it's already installed. Simply hit the reinstall button to reinstall the shell extension. That should resolve the issue.

![EPM](/assets/images/2024-04-01-GettingStarted-With-EPM/EPM-MSIXShellExtension.png?raw=true "EPM Shell Extension")
