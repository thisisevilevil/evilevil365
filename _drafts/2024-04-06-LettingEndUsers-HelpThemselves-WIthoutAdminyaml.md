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
instead of you having to spending time on preparing and assigning Office suite with extra language packs for different users, in different countries, it is much easier to let end-users self-service and do it themselves.

You can allow users who are not local administrator to add language packs in office. Open up Intune and create a new settings catalog. Do a search for "allow users who aren" - That should give you 1 search result: MIcrosoft Office 2016\Language Preferences\Display Language. Press it, and select "Allow users who aren’t admins to install language accessory packs (User)". Finally enable this setting

## Visual Studio Code Updates
You can allow end-users to patch their own Visual Studio code, if it's installed in System context.