---
title: "WebView2 and Microsoft Edge and Autopilot issues"
date: 2025-08-19
categories:
  - Intune
tags:
  - Microsoft Edge Outdated
  - WebView2 outdated
  - Teams WebView issue
  - Outlook webview issue
---

If you are using Autopilot and re-using the image from your OEM, you are already doing it right. It doesn't mean all your problems will go away, it's just different kind of problems.

If you suddenly face an issue where your users are complaining they cannot launch Teams or the new outlook client because of a Webview2 error then read on.

## What is WebView2

WebView2 is a web control from Microsoft that lets developers embed web content (HTML, CSS, JavaScript) into their desktop applications. It’s built on the same Microsoft Edge (Chromium) rendering engine, so apps can display modern web experiences without relying on the old Internet Explorer–based WebBrowser control. WebView2 is installed and updated with Microsoft Edge.

The new outlook and teams clients relies on this component to work correctly. No webview or extremely outdated webview component? Then you will probably face the below error

![Webview error](/assets/images/2025-08-19-Webview2-Autopilot-issue/Webview2-teams-error.png?raw=true "Webview2 missing")

## Why is it happening

I'm working with 1 customer where Microsoft says it's an OEM issue and the OEM says it's a Microsoft issue. In the meantime, if you download the latest Windows 11 24H2 ISO (As of this date it's updated July 2025), mount the ISO and open the install.wim with 7zip we can take a peak at the Edge version by navigating to ..\1\Program Files (x86)\Microsoft\Edge\Application: 122.0.2365.106

![Webview error](/assets/images/2025-08-19-Webview2-Autopilot-issue/EdgeVersion-MountedWim.png?raw=true "Edge version in WIM file")

If we look up the date of Edge version 122.0.2365.106 it's basically from the stoneage: Version 122.0.2365.106: March 21, 2024 ([Source](https://learn.microsoft.com/en-us/deployedge/microsoft-edge-relnote-archive-stable-channel))

And that's the cause of our issue, the Edge version is simply too old and WebView2 is built-in to Edge. So preferably we need to get it updated before the user lands on the desktop and opens teams or outlook.

## The workaround (Solution?)

We can trigger an Edge update by using the following PowerShell command ([source](https://learn.microsoft.com/en-us/deployedge/deploy-edge-with-windows-10-updates)): 

```PowerShell
Start-Process -FilePath "C:\Program Files (x86)\Microsoft\EdgeUpdate\MicrosoftEdgeUpdate.exe" -argumentlist "/silent /install appguid={56EB18F8-B008-4CBD-B6D2-8C97FE7E9062}&appname=Microsoft%20Edge&needsadmin=True"
```

We have a couple of options for deploying this with Intune, but I have found the easiest and fastest way is to deploy it as a PowerShell script from Intune (Platform script). PowerShell scripts run before Win32 apps during the autopilot enrollment process. I created a PowerShell script to check for the Edge version and it's below a certain version, we will update it, otherwise no actions is performed. You can find the script in my github [here](InsertLink)
