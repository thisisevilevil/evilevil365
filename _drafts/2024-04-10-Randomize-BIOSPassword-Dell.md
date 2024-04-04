---
title: "Use Intune to randomize BIOS Passwords and set BIOS Settings on Dell devices"
date: 2024-04-03
categories:
  - Dell
  - Getting Started
tags:
  - EPM
  - Endpoint Privilege Manager
  - PAM Solution
  - Local Administrator
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