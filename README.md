# Azure Monitor Alerts & Log Analytics Lab

> **Disclaimer:** All Azure resources created in this lab were deleted after completion. No services were left running. This lab was built purely for learning and documentation purposes.

---

## Description

This lab focuses on Azure Monitor, Log Analytics, and alert configuration. It covers deploying a Windows VM, connecting it to a Log Analytics Workspace via a Data Collection Rule, writing KQL queries against live performance data, and configuring an alert rule that fires a real email notification when CPU thresholds are breached.

---

## What I Covered in this Lab

- Deploying a Windows VM and connecting it to Azure Monitor via a Data Collection Rule
- Setting up a Log Analytics Workspace to ingest performance and heartbeat data
- Writing KQL queries to analyse CPU metrics and agent connectivity
- Configuring an Action Group to deliver email alert notifications
- Creating a metric alert rule with a defined CPU threshold
- Triggering a live alert by stress-testing the VM and confirming the notification

---

## Future Planned Objectives to Cover

- <!-- e.g. Log-based alerts using KQL queries rather than metric thresholds -->
- <!-- e.g. Azure Monitor Workbooks for custom dashboards -->
- <!-- e.g. Diagnostic settings on additional resource types -->

---

## Lab Structure

```
Azure-Monitor-Alerts-and-Log-Analytics-lab/
├── README.md
├── commands-reference.md
└── screenshots/
    ├── part1-resource-group/
    ├── part2-log-analytics-workspace/
    ├── part3-windows-vm/
    ├── part4-data-collection-rule/
    ├── part5-action-group/
    ├── part6-alert-rule/
    ├── part7-cpu-stress-test/
    └── part8-kql-queries/
```

---

## Part 1 — Resource Group

I created the resource group `WakaHuia` in the `Australia East` region to house all lab resources.

**Steps:**

1. Navigate to **Resource groups** → **+ Create**
2. Name: `WakaHuia` | Region: `Australia East`
3. Click **Review + create** → **Create**

<img src="https://imgur.com/GeQ7Fyj.png" height="80%" width="80%" alt=""/>

---

## Part 2 — Log Analytics Workspace

I created the Log Analytics Workspace `Angitu-Log` as the central destination for all VM telemetry and performance data collected during the lab.

**Steps:**

1. Search **Log Analytics workspaces** → **+ Create**
2. Resource group: `WakaHuia`
3. Name: `Angitu-Log` | Region: `Australia East`
4. Click **Review + create** → **Create**
5. After deployment, noted the **Workspace ID** under **Agents management**

<img src="https://imgur.com/Iokk5gB.png" height="80%" width="80%" alt=""/>

---

## Part 3 — Windows VM (Manutaki)

I deployed a Windows Server VM named `Manutaki` to act as the monitored resource for the lab.

**Steps:**

1. Search **Virtual Machines** → **+ Create** → **Azure Virtual Machine**
2. Resource group: `WakaHuia`
3. VM name: `Manutaki` | Region: `Australia East`
4. Image: `Windows Server 2022 Datacenter` | Size: `Standard_B1s`
5. Configured admin credentials and allowed inbound **RDP (3389)**
6. Click **Review + create** → **Create**

<img src="https://imgur.com/49P5tPJ.png" height="80%" width="80%" alt=""/>

---

## Part 4 — Data Collection Rule (Ope-Raraunga)

I created a Data Collection Rule to connect `Manutaki` to `Angitu-Log`, enabling the Azure Monitor Agent to forward performance counters and heartbeat data to the workspace.

**Steps:**

1. Navigate to **Monitor** → **Settings** → **Data Collection Rules** → **+ Create**
2. Name: `Ope-Raraunga` | Resource group: `WakaHuia` | Region: `Australia East`
3. Platform type: **Windows**
4. Under **Resources** → **+ Add resources** → selected `Manutaki`
5. Under **Collect and deliver** → **+ Add data source**
   - Type: **Performance Counters** (CPU, Memory, Disk, Network)
   - Destination: **Azure Monitor Logs** → `Angitu-Log`
6. Click **Review + create** → **Create**
7. Confirmed **Azure Monitor Agent** extension installed automatically on `Manutaki`

<img src="https://imgur.com/TMTG7Po.png" height="80%" width="80%" alt=""/>

| Data Source | Destination |
| ----------- | ----------- |
| Performance Counters (CPU, Memory, Disk, Network) | `Angitu-Log` |
| Heartbeat | `Angitu-Log` |

<img src="https://imgur.com/cTLWniU.png" height="80%" width="80%" alt=""/>

---

## Part 5 — Action Group (Matarae-Action)

I created an Action Group to define how alerts would be delivered — in this case via email notification.

**Steps:**

1. Navigate to **Monitor** → **Alerts** → **Action groups** → **+ Create**
2. Resource group: `WakaHuia`
3. Action group name: `Matarae-Action` | Display name: `Matarae`
4. Under **Notifications**:
   - Type: **Email/SMS/Push/Voice**
   - Name: `email-notify` | entered lab email address
5. Click **Review + create** → **Create**

<img src="https://imgur.com/kju1jGc.png" height="80%" width="80%" alt=""/>

---

## Part 6 — Alert Rule (Karanga-1)

I created a metric alert rule scoped to `Manutaki` that would trigger whenever average CPU exceeded 70% over a 5-minute window.

**Steps:**

1. Navigate to **Monitor** → **Alerts** → **+ Create** → **Alert rule**
2. Scope: `Manutaki`
3. Condition: `Percentage CPU` | Average | Greater than `70%` | over `5 minutes`
4. Action group: `Matarae-Action`
5. Alert rule name: `Karanga-1` | Severity: `Sev 2 - Warning`
6. Resource group: `WakaHuia`
7. Click **Review + create** → **Create**

| Setting | Value |
| ------- | ----- |
| Signal | Percentage CPU |
| Aggregation | Average |
| Operator | Greater than |
| Threshold | 70% |
| Evaluation period | 5 minutes |
| Severity | Sev 2 — Warning |

<img src="https://imgur.com/aMsRPxP.png" height="80%" width="80%" alt=""/>

---

## Part 7 — CPU Stress Test & Alert Trigger

I RDP'd into `Manutaki` and ran a PowerShell loop to spike the CPU above the 70% threshold, triggering `Karanga-1` and delivering a live email notification via `Matarae-Action`.

**Steps:**

1. RDP'd into `Manutaki`
2. Opened **PowerShell as Administrator** and ran:

```powershell
while ($true) { $x = 1; 1..1000000 | % { $x *= $_ } }
```
<img src="https://imgur.com/E3RmTto.png" height="80%" width="80%" alt=""/>

3. Confirmed CPU spike via **Task Manager** on the VM
4. Monitored **Monitor → Alerts** in the portal — `Karanga-1` fired
5. Received email notification from `Matarae-Action`
6. Stopped the script with `Ctrl + C` after alert confirmed

<img src="https://imgur.com/L7LXabu.png" height="80%" width="80%" alt=""/>

<img src="https://imgur.com/z2pKok8.png" height="80%" width="80%" alt=""/>

---

## Part 8 — KQL Queries in Log Analytics

I ran KQL queries against `Angitu-Log` to analyse the CPU spike data and confirm agent connectivity.

**Query 1 — Average CPU over time:**

```kql
Perf
| where ObjectName == "Processor"
| where CounterName == "% Processor Time"
| where Computer == "Manutaki"
| summarize avg(CounterValue) by bin(TimeGenerated, 5m), Computer
| order by TimeGenerated desc
```

**Query 2 — Heartbeat check (agent connectivity):**

```kql
Heartbeat
| where Computer == "Manutaki"
| order by TimeGenerated desc
| take 10
```
<img src="https://imgur.com/LFMZCl7.png" height="80%" width="80%" alt=""/>

<img src="https://imgur.com/ueGF7aQ.png" height="80%" width="80%" alt=""/>

---

## Key Takeaways

---

## Lessons Learned

---

*Lab completed by [@Nako8k](https://github.com/Nako8k)*
