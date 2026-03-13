---
title: "macOS and Windows security"
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

## The platform gap

Many organizations apply heavy security controls on Windows devices, which can push employees toward macOS. At the same time, those same organizations often apply far fewer controls to macOS devices. This creates an uneven security posture across the estate.

I see a few root causes:

- Tooling gaps: IT teams often lack out-of-the-box tools to manage features such as application control and local administrator rights for both platforms. Third-party solutions are frequently required.
- Process shortcomings: exemption and approval workflows are often slow or poorly defined, which encourages workarounds 

## A real-world example

One developer I know joined a large bank/trading firm in Denmark. He was provisioned a Windows device with no administrator rights. While that policy aligns with security best practices, it created productivity friction: requests for temporary admin access took three to four weeks to process.

The workaround? The team provided a Mac mini with full local access and root privileges. The developer could do everything locally and access the same network and files as on the Windows device—effectively bypassing the controlled Windows environment. In the end, he just chose a macOS Laptop as his main working device.

## macOS management maturity

Some organizations are still early in their macOS management journey and underestimate the threats macOS devices face. Windows security has been a primary focus at many companies for years; macOS has often been treated as an escape hatch for developers. 

On the other hand, there are still companies out there that doesn't do mac management at all, they only offer windows-devices to their employees, which is a shame overall.

## Recommendations

To address this imbalance, organizations should consider:

- Investing in cross-platform tooling that supports application control, least-privilege enforcement, and just-in-time elevation workflows.
- Streamlining exemption processes so valid requests can be approved quickly and with proper auditing.
- Raising macOS security maturity: adopt endpoint detection and response (EDR), device management, and configuration baseline practices for macOS just as for Windows.
- Educating hiring managers and teams about the security trade-offs of providing unmanaged or privileged macOS devices.

## Conclusion

The tension between developer productivity and enterprise security is real. Rather than letting platform preference drive shadow IT, organizations should close the management gap between Windows and macOS—so that developers can be productive without creating unmanaged security risks.

