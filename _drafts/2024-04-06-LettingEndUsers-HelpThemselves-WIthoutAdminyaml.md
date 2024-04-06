---
title: "Letting end users help themselves - without needing local admin rights!"
date: 2024-04-04
categories:
  - Intune Configuration Profiles
tags:
  - Visual Studio code update
  - Office Language pack
  - User self-service
---

Did you know there is some cool settings in Settings catalog to make things much easier for your end users and your self as an IT Admin? There is a few cool settings that let users perform actions on their devices, that usually require administrator rights, but without being local administrator. Sounds too good to be true? Well read here and find out :)


## Office Language Packs
instead of you spending time on preparing and assigning Office suite with extra language packs for different users in different countries, it is much easier to let end-users self-service and do it themselves, rather than maintaining language packs and proofing packs, in IT. Especially in large multi-national orgs, this can be very time consuming.

You should allow users who are not local administrator to add language packs in office. Open up Intune and create a new settings catalog. Do a search for "allow users who aren" - That should give you 1 search result: Microsoft Office 2016\Language Preferences\Display Language. Press it, and select "Allow users who aren’t admins to install language accessory packs (User)". Finally "enable" this setting

![OfficeLP](/assets/images/2024-04-06-AllowUsers-WhoArentAdmin/SettingsCatalog-OfficeAppInstall.png?raw=true "Office Language Pack")

Then deploy using the scope tags and to a group of your choice to make the setting take effect. Once deployed, users can go to the options of any app in the office suite and then go to language -> Add Language. The user should now be able to add language packs themselves. They can also tick "Store my authoring languages in the cloud for my account" to make their choice of language follow them to other devices.

Once you have set this up, the ideal choice for most would be to deploy an english version of office, then create self-help guides for end-users to let them know they can install their own language packs, and voila! You just saved you and your colleagues a bunch of time managing Office suite installations in your org!

![OfficeLP](/assets/images/2024-04-06-AllowUsers-WhoArentAdmin/SettingsCatalog-OfficeAppInstall.png?raw=true "Office Language Pack")

## Visual Studio Code Updates
You can allow end-users to patch their own Visual Studio code, even if it's installed in system context.

Create a new settings catalog policy, then do a search for "visual studio code". Select "Visual Studio Install and update" settings". Then tick "Allow standard users to execute installer operations". Finally enable "allow standard users to execute installer operations" and "Enabled for all installer operations".

Then deploy using the scope tags and to a group of your choice to make the setting take effect. Once deployed, the users can freely update their visual studio code, without needing local admin rights.

![VSCodeUpdate](/assets/images/2024-04-06-AllowUsers-WhoArentAdmin/SettingsCatalog-OfficeAppInstall.png?raw=true "VS Code Updates")

## Other honorable mentions

1. **Let standard users manage their LAN Connections - Consider this for developers/special users**
* "Ability to rename LAN connections (User)"
* "Ability to Enable/Disable a LAN connection (User)"
* "Ability to rename LAN connections or remote access connections available to all users (User)"

2. **Allow standard users to change system time**
* Create Custom profile in Intune, OMA-URI: ./Vendor/MSFT/Policy/Config/UserRights/ChangeSystemTime
* Data type, string: `*S-1-5-19*S-1-5-32-544*S-1-5-32-545`
> **_NOTE:_** **That is the SIDs for LOCAL SERVICE, Administrators and Users - The reason we are using SIDs instead of names, is to add support for Windows devices that is not using english as the base language**
