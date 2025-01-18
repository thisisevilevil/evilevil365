---
title: "Windows 11 24H2 Feature Update issues"
date: 2025-01-18
categories:
  - Intune
  - Feature Updates
tags:
  - Feature Update issue
  - Windows 11 24H2
  - Known Issues
---

Windows 11 24H2 is a major feature update with lots of new features. But such a major feature update is also bound to cause some hiccups. As of this blog posts date there is a total of [13 known issues](https://learn.microsoft.com/en-us/windows/release-health/status-windows-11-24h2) logged on Microsofts website, where arguably the most impactful is the [Easy Anti-Cheat issues](https://learn.microsoft.com/en-us/windows/release-health/status-windows-11-24h2#263msgdesc) which lots of games use.

In this blog post I will highlight a known (but unknown?) issue, since it's not listed on Microsoft's website yet. I actually asked about this particular issue on Microsoft's CCP teams channel all the way back in October last year, as we started to see a strange issue where the Windows 11 24H2 Feature update would fail in a peculiar way, where the subsequent rollback that is supposed to roll the device back to a known good state also failed, leaving something known as a "leaked OS Entry".

So what's this all about? Read on to find out.

![Feature Update issue](/assets/images/2025-18-01-24H2FeatureUpdate-Issue/RASTTools_Thumbnail.jpeg?raw=true "Thumbnail")

## Applying the feature update: Analyzing the issue

Applying the fature update shows no issues, but it's at the subsequent reboot when it's goes into a Phase 2 of sorts to finalize the process, where it locates an issue and attempts to perform a rollback

![Feature Update issue](/assets/images/2025-18-01-24H2FeatureUpdate-Issue/Rollback-1.png?raw=true "Rolling back")
![Feature Update issue](/assets/images/2025-18-01-24H2FeatureUpdate-Issue/Rollback-2.png?raw=true "Rolling back")

### Running setupdiag after the rollback

After the rollback is complete, we can run setupdiag to find out what has gone wrong. In this particular instance, we can see it's having some issues verifying a filehash on a package known as `Microsoft-Windows-FailoverCluster-Management-Tools-FOD-Package~31bf3856ad364e35~amd64~~.cab` - This package is part of the RSAT tools.

See below excerpt from a [SetupDiag](https://learn.microsoft.com/en-us/windows/deployment/upgrade/setupdiag) XML Output:

```XML
 </SystemInfo>
  <LogErrorLine>2025-01-18 13:58:29, Error                 CBS    Failed to process single phase execution. [HRESULT = 0x800736cc - ERROR_SXS_FILE_HASH_MISMATCH]</LogErrorLine>
  <FailureData>0x800736cc-0x20003 Error: SetupDiag reports rollback failure found.Last Phase = Safe OSLast Operation = Add [6] package C:\$WINDOWS.~BT\DUImageSandbox\Microsoft-Windows-FailoverCluster-Management-Tools-FOD-Package~31bf3856ad364e35~amd64~~.cab to C:\$WINDOWS.~BT\NewOSError = 0x800736CC-0x20003</FailureData>
  <FailureData>LogEntry: 2025-01-18 13:58:29, Error                 CBS    Failed to process single phase execution. [HRESULT = 0x800736cc - ERROR_SXS_FILE_HASH_MISMATCH]</FailureData>
  <FailureData>Refer to "https://docs.microsoft.com/en-us/windows/desktop/Debug/system-error-codes" for error information.</FailureData>
  <FailureDetails>RollbackErrorCode = 0x800736CC, ExtendedCode = 0x20003, LastOperation = Add [6] package C:\$WINDOWS.~BT\DUImageSandbox\Microsoft-Windows-FailoverCluster-Management-Tools-FOD-Package~31bf3856ad364e35~amd64~~.cab to C:\$WINDOWS.~BT\NewOS, LastPhase = Safe OS</FailureDetails>
  <Setup360Result>0x800736cc</Setup360Result>
  <Setup360Extended>0x20003</Setup360Extended>
  <SetupPhaseInfo>
    <PhaseName>Safe OS</PhaseName>
    <PhaseStartTime>01/18/2025 13:55:27</PhaseStartTime>
    <PhaseEndTime>01/01/0001 00:00:00</PhaseEndTime>
    <PhaseTimeDelta>0:00:00:00.0000000</PhaseTimeDelta>
    <CompletedSuccessfully>false</CompletedSuccessfully>
  </SetupPhaseInfo>
  <SetupOperationInfo>
    <OperationName>Add [6] package C:\$WINDOWS.~BT\DUImageSandbox\Microsoft-Windows-FailoverCluster-Management-Tools-FOD-Package~31bf3856ad364e35~amd64~~.cab to C:\$WINDOWS.~BT\NewOS</OperationName>
    <OperationStartTime>01/18/2025 13:58:26</OperationStartTime>
    <OperationEndTime>01/01/0001 00:00:00</OperationEndTime>
    <OperationTimeDelta>0:00:00:00.0000000</OperationTimeDelta>
    <CompletedSuccessfully>false</CompletedSuccessfully>
```

## Workaround

To workaround this issue for now, simply remove the 2 mentioned RSAT Components which is the following: `Rsat.FailoverCluster.Management.Tools~~~~0.0.1.0` `Rsat.FileServices.Tools~~~~0.0.1.0`

```PowerShell
Remove-WindowsCapability -Name 'Rsat.FailoverCluster.Management.Tools~~~~0.0.1.0' -Online
Remove-WindowsCapability -Name 'Rsat.FileServices.Tools~~~~0.0.1.0' -Online
```

>NOTE: If you receive an error saying something like "Can't remove permanent" package, you can just ignore it.

Once removed, also ensure to manually remove the bad boot entries using bcdedit or msconfig:
![msconfig](/assets/images/2025-18-01-24H2FeatureUpdate-Issue/RemoveBootEntry.png?raw=true "msconfig remove bad boot entry")
![msconfig](/assets/images/2025-18-01-24H2FeatureUpdate-Issue/RemoveBootEntry-1.png?raw=true "msconfig remove bad boot entry")
![msconfig](/assets/images/2025-18-01-24H2FeatureUpdate-Issue/RemoveBootEntry-2.png?raw=true "msconfig remove bad boot entry")

>NOTE: Ignore the warning about bitlocker, it should not trigger.

Finally reboot the device, and retry the update. This time it should complete without any issues. Once the update has been applied, you can go ahead and reinstall the RSAT tools we removed. And now you can proceed to test your Windows 11 24H2 rollout.

## Wrapping up

As of this date, there is currently not ETA of when this will be fixed, but the Product Group responsible is working on a fix, as pr. a Microsoft support ticket, I have submitted on behalf of one of my customers.

![Feature Update issue](/assets/images/2025-18-01-24H2FeatureUpdate-Issue/FeatureUpdate_Issue_PG.png?raw=true "Feature Update issue")

The RSAT tools have a very long history of breaking during feature updates, it's a very recurring theme. This time it just broke a feature update in a different way I guess. In the meantime, this should not affect the average end-user, annoyingly this should only affect IT Staff as they are normally the ones using these RSAT Tools.

That's it for now, have an awesome day :)
