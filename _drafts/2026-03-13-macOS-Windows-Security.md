---
title: "Windows Locked Down, macOS Wide Open: A Security Leadership Problem"
date: 2026-03-13
categories:
  - Security
tags:
  - macOS
  - Windows
  - Endpoint Management
  - Security Best Practices
---

I've had many conversations with developers, IT administrators, and decision makers at large organizations about security on Windows and macOS devices—especially how security controls affect developer experience and local administrator access.

What I hear consistently across customers and enterprises is not surprising: more new hires—especially developers—are choosing macOS over Windows when they start a job.

## Why developers prefer macOS

Common feedback from developers is straightforward: "macOS is easier and faster. Everything just works and I can do what I need without restrictions." That perception is driving platform choice for many technical hires.

Many organizations apply heavy security controls on Windows devices, which can push employees toward macOS. At the same time, those same organizations often apply far fewer controls to macOS devices. This creates an uneven security posture across the estate.

I see a few root causes:

- Tooling gaps: IT teams often lack out-of-the-box tools to manage features such as application control and local administrator rights for both platforms. Third-party solutions are frequently required.
- Process shortcomings: exemption and approval workflows are often slow or poorly defined, which encourages workarounds
- Knowledge: IT Administrators in traditional Windows-based companies often doesn't know a whole lot about mac management, as they come from a traditional windows background. When macOS is introduced, a lot of times coming out of left field, there is no upskilling efforts for managing macOS and IT Admins are just expected to manage it the same as a Windows device. You have to learn how to walk before you can run.

## A real-world example

One of my good friends whose also a developer, switched jobs, and joined a large bank in Denmark recently. He was given a Windows device with no administrator rights. While that policy aligns with security best practices, it created productivity friction: requests for temporary admin access took three to four weeks to process.

The workaround? The team provided a Mac mini with root privileges, that could be accessed remotely, where he could do his "actual" work, while outlook/teams was to be used on his Windows laptop. He could do everything he wanted on the mac mini, since he had root access, while still being able to access the same network and files as on the Windows device, effectively bypassing the controlled Windows environment. In the end, he just chose a macOS Laptop as his main working device.

## Recommendations

To address this imbalance, organizations should consider:

- Raising knowledge and awareness of macOS management+security in the IT team managing devices
- Streamline the developer onboarding experience for Windows, so it's similar to macOS
- Be realistic about things like Security Baselines and CIS Baselines for Windows: User productivity and Security needs to be balanced.
- Consider acquiring a cross-platform PAM Solution for Endpoints that works both for macOS and Windows for managing local administrator/root access.

## Conclusion

The tension between developer productivity and enterprise security on windows is real. Rather than letting platform preference drive shadow IT, organizations should close the management gap between Windows and macOS—so that developers can be productive without creating unmanaged security risks.
