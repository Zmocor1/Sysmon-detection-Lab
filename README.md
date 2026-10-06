# Lab 2 – Sysmon Process Monitoring with Wazuh

## Objective

The goal of this lab was to improve Windows endpoint visibility by using **Microsoft Sysmon** with **Wazuh**. I configured Sysmon process-creation events to be collected by the Wazuh agent, forwarded to the Wazuh manager, and displayed as alerts for investigation.

## Tools Used

- Windows 11 VM
- Ubuntu Linux VM
- Wazuh SIEM
- Wazuh Agent
- Microsoft Sysmon
- PowerShell
- VirtualBox

## What is Sysmon?

**Sysmon (System Monitor)** is a Microsoft Sysinternals tool that provides detailed visibility into activity occurring on a Windows system. It can log events such as process creation, network connections, file activity, and other system behavior.

These events can then be forwarded to a SIEM such as Wazuh for monitoring and investigation.

## Lab Process

I first verified that Sysmon was successfully collecting **Event ID 1 – Process Creation** events on the Windows endpoint.

I then configured the Wazuh agent to monitor the Sysmon Operational event channel:

```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

After restarting the Wazuh agent, I confirmed that Sysmon events were successfully traveling through the following pipeline:

**Windows Endpoint → Sysmon → Wazuh Agent → Wazuh Manager**

To make Sysmon process-creation events visible as alerts in Wazuh Threat Hunting, I configured the Sysmon Event ID 1 rule to generate a level 3 alert.

I then generated a PowerShell process on the Windows endpoint:

```powershell
Start-Process powershell.exe
```

## Detection Results

Wazuh successfully detected the PowerShell process as a **Sysmon Event ID 1 – Process Creation** event.

The alert included:

- **Rule ID:** 61603
- **Rule Description:** Sysmon - Event 1: Process creation
- PowerShell process activity
- Event timestamp
- Windows endpoint information
- Sysmon event data
- Parent process information

### Threat Hunting Detection

<img width="1920" height="1032" alt="Screenshot 2026-10-06 021434" src="https://github.com/user-attachments/assets/4b8cab9b-dc7b-4a0f-a6c9-2330fd1e6be4" />


### Event Details

<img width="1531" height="394" alt="Screenshot 2026-10-06 021000" src="https://github.com/user-attachments/assets/fdd721c4-35fa-4ce1-b61f-adff567d0589" />


## Troubleshooting

During the lab, Sysmon events were initially being generated locally but were not appearing in Wazuh.

I verified the event locally, checked the Wazuh log collection configuration, corrected the Sysmon event-channel configuration, and confirmed that events were reaching the Wazuh manager.

I also verified the raw event logs before enabling the Sysmon process-creation alert in Threat Hunting.

## What I Learned

This lab helped me understand how endpoint telemetry moves from a Windows system into a SIEM.

I gained hands-on experience with:

- Sysmon process monitoring
- Windows Event Logs
- Wazuh agent configuration
- SIEM log ingestion
- Wazuh custom rule configuration
- Threat Hunting
- Process creation analysis
- Troubleshooting missing security events

One of the biggest takeaways from this lab was learning that successfully generating an event on an endpoint does not automatically mean it will appear as an alert in the SIEM. I had to verify each stage of the logging pipeline and ensure the appropriate Wazuh rule was configured to surface the event.

## Outcome

The lab successfully demonstrated the collection and detection of Windows process-creation activity using **Sysmon and Wazuh**, including the detection of a PowerShell process in the Wazuh Threat Hunting dashboard.
