# LAB 20 — Windows Support Environment

## Overview

This lab establishes a baseline for Windows IT Support by collecting system information, identifying the operating system and hardware configuration, and documenting the initial state of a Windows workstation.

Before troubleshooting a user-reported issue, IT Support technicians need to understand the device, gather relevant information, and establish a clear starting point for investigation.

This lab introduces the initial assessment and documentation process used in a practical IT support environment.

## Objectives

By completing this lab, you will learn how to:

- Identify Windows operating system and device specifications.
- Navigate Windows Settings to inspect system information.
- Use the built-in System Information utility (`msinfo32`).
- Export a system information report.
- Create a structured workstation baseline record.
- Understand the importance of collecting evidence before troubleshooting.
- Follow a systematic IT support workflow.

## Scenario

A company employee contacts the IT Support team to request assistance with a workstation.

Before investigating the reported issue, the technician must identify the device, record its operating system and hardware specifications, and gather the information required for future troubleshooting.

The objective is to establish a documented baseline without making unnecessary changes to the workstation.

## Lab Environment

**Environment Type:** Simulated Windows workstation  
**Operating System:** Windows 11 Pro  
**Tools:** Windows Settings, System Information (`msinfo32`), Notepad

### Sample Workstation Information — Mock Data

The following information is fictional and is provided for documentation and training purposes only.

| Property | Mock Value |
|---|---|
| Device Name | CLIENT-WS01 |
| Manufacturer | Dell |
| System Model | Latitude 5440 |
| Operating System | Windows 11 Pro |
| Windows Version | 24H2 |
| OS Build | 26100.x |
| Processor | Intel Core i5-1345U |
| Installed RAM | 16 GB |
| System Type | 64-bit operating system, x64-based processor |
| BIOS Mode | UEFI |
| Secure Boot State | On |
| Virtualization | Supported, subject to firmware and Windows configuration |

> **Note:** These are illustrative values, not information collected from an actual company workstation. Actual System Information values depend on the device and its configuration.

## Tools Used

### 1. Windows Settings

Windows Settings provides an accessible way to inspect basic device and operating system information.

Navigation:

`Settings → System → About`

Relevant information includes:

- Device name
- Processor
- Installed RAM
- System type
- Windows edition
- Windows version

### 2. System Information (`msinfo32`)

System Information provides a more detailed overview of the Windows environment, hardware resources, and firmware configuration.

To open it:

1. Press `Win + R`.
2. Enter `msinfo32`.
3. Press Enter.
4. Select **System Summary**.

Relevant fields include:

- OS Name
- Version and OS Build
- System Manufacturer
- System Model
- Processor
- BIOS Version/Date
- BIOS Mode
- Secure Boot State

The fields displayed may vary depending on the hardware, firmware, and Windows configuration.

## Procedure

### Step 1 — Inspect Basic System Information

1. Open Windows Settings using `Win + I`.
2. Navigate to **System**.
3. Select **About**.
4. Review the device specifications and Windows specifications.
5. Record the relevant information in the workstation inventory.

**Expected Result:** The basic operating system and hardware information is identified and documented.

### Step 2 — Inspect System Information

1. Open the Run dialog using `Win + R`.
2. Enter `msinfo32`.
3. Open **System Summary**.
4. Review the operating system, processor, manufacturer, model, BIOS mode, and Secure Boot state.
5. Compare these details with the basic information shown in Windows Settings.

**Expected Result:** Additional system and firmware details are available for the baseline record.

### Step 3 — Export the System Information Report

1. In the System Information window, select **File**.
2. Select **Export**.
3. Save the report as `LAB20-System-Baseline.txt`.
4. Open the exported file.
5. Confirm that the report contains readable system information.

**Expected Result:** A text report is created and can be reviewed when needed.

### Step 4 — Create the Support Baseline Record

Document the relevant device information in a structured record.

The record should include:

- Device identification
- Operating system edition and version
- Hardware specifications
- Firmware configuration
- Initial observations
- Notes relevant to future troubleshooting

A baseline helps technicians understand the starting condition of a device and compare future observations against it.

## Sample Support Baseline Record

**Record ID:** LAB20-BASELINE-001  
**Device:** CLIENT-WS01  
**Record Type:** Initial Workstation Assessment  
**Data Classification:** Training / Mock Data

### Device Inventory

| Field | Recorded Value |
|---|---|
| Device Name | CLIENT-WS01 |
| Manufacturer | Dell |
| Model | Latitude 5440 |
| Processor | Intel Core i5-1345U |
| Installed RAM | 16 GB |
| System Architecture | 64-bit |

### Operating System

| Field | Recorded Value |
|---|---|
| Edition | Windows 11 Pro |
| Version | 24H2 |
| OS Build | 26100.x |

### Firmware

| Field | Recorded Value |
|---|---|
| BIOS Mode | UEFI |
| Secure Boot | Enabled |
| BIOS Version | To be verified on the target workstation |

### Initial Assessment

- Basic workstation information has been documented using the Windows Settings interface.
- Detailed system information can be inspected through `msinfo32`.
- An exported system information report provides a reference for future support activities.
- No hardware or firmware changes are required as part of this baseline exercise.
- This assessment does not establish that the workstation is completely free of problems.

## IT Support Workflow

A structured support workflow helps prevent unnecessary changes and improves troubleshooting accuracy.

1. **Identify** — Determine which device and user are affected.
2. **Understand** — Gather the symptoms, scope, and impact of the reported issue.
3. **Collect** — Obtain relevant system information and supporting evidence.
4. **Establish a Baseline** — Record the initial device state.
5. **Investigate** — Select diagnostic tools appropriate to the symptoms.
6. **Resolve** — Apply a justified fix when the cause is sufficiently understood.
7. **Verify** — Confirm that the original issue has been resolved.
8. **Document** — Record the findings, actions, and outcome.

> Collecting system information is an initial assessment step. It does not replace symptom analysis or targeted troubleshooting.

## Safety and Data Handling

- Use fictional information in publicly shared examples.
- Do not publish serial numbers, product IDs, license keys, personal usernames, or other unnecessary identifying information.
- Keep full system reports local unless there is an approved reason to share them.
- Avoid changing BIOS, Secure Boot, or other firmware settings during baseline collection.
- Collect only the information required for the support task.

## Verification Checklist

- [x] Basic Windows system information reviewed.
- [x] System Information (`msinfo32`) opened.
- [x] System Summary reviewed.
- [x] System information report exported.
- [x] Initial assessment and support workflow reviewed.
- [x] Mock workstation record prepared for documentation.

**Note:** The mock workstation values illustrate the documentation format. A successful lab record must distinguish between verified observations and illustrative sample data.

## Skills Practiced

- Windows workstation identification
- Operating system and hardware inventory
- System Information inspection
- Report export and documentation
- Initial IT support assessment
- Evidence collection and data handling
- Structured troubleshooting workflow

## Key Takeaways

An effective IT support investigation begins with accurate information gathering and a clear understanding of the reported issue.

System information tools help technicians establish a workstation baseline, but their output must be interpreted alongside the user's symptoms and other diagnostic evidence.

A consistent documentation process makes troubleshooting easier to reproduce, review, and communicate.

## Next Lab

**LAB 21 — Windows Command-Line Fundamentals**

The next lab introduces Windows command-line tools for system identification and basic administrative diagnostics.

The goal is to build on the initial system assessment skills developed here and gradually introduce command-line workflows used in practical IT support.

---

**Project:** IT Network & Infrastructure Lab  
**Journey:** 100 Days of IT Infrastructure  
**Lab:** LAB 20 — Windows Support Environment  
**Focus:** Windows Administration, System Information, and Support Workflow