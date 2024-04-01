---
title: "Getting started with EPM - Endpooint Privilege Manager"
date: 2024-04-01
categories:
  - Intune Suite
tags:
  - EPM
  - Endpoint Privilege Manager
  - PAM Solution
  - Local Administrator
---

# What is EPM?
EPM, short for {Endpoint Privilege Management](https://learn.microsoft.com/en-us/mem/intune/protect/epm-overview), is Microsofts own tool to control, audit and manage elevated rights on windows endpoints (mac soon to come as well!). This is also under a category of what we refer to as a [PAM Solution for Endpoints](https://www.microsoft.com/en/security/business/security-101/what-is-privileged-access-management-pam#:~:text=Privileged%20access%20management%20(PAM)%20is,privileged%20access%20to%20critical%20resources). Having a PAM Solution for endpoints is absolutely vital to control and audit the usage of elevating processes.

Some organizations have chosen to remove local admin rights altogether, but there can be certain times where users needs these admin rights to do their job, i.e: Changing system settings for development purposes, installing apps not present in the existing app catalogs or just general supportability, the list is can be endless. The point is: If we don't have a good and secure way to facilitate this on behalf of the end-user, guess what? They are going to log a ticket to ServiceDesk! That's where the good PAM Solution comes in. Not only will it eliminate the need to log a ticket to ServiceDesk, it will also still be able to facilitate that elevation without compromising security!

At it's core, EPM will give you the following functionalities:
- Allow standard users to elevate processes in a guarded enviroment
- 
