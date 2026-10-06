# LAB 16 — Switch Port Security

## 📌 Overview

This lab focuses on securing Cisco switch access ports using Port Security and controlling which MAC addresses are allowed to use a specific switch port.

The lab demonstrates how Port Security can be used to:

- Control MAC addresses on access ports
- Limit the number of allowed MAC addresses
- Configure Static Secure MAC addresses
- Configure Sticky Secure MAC addresses
- Detect unauthorized MAC addresses
- Trigger Security Violations
- Understand Port Security Violation Modes
- Recover a port after a Security Violation
- Verify Port Security behavior
- Troubleshoot access-port security issues

Learning approach:

**Learn → Build → Test → Break → Troubleshoot → Fix → Verify → Document**

---

# 🎯 Objectives

By completing this lab, I practiced:

- Understanding Switch Port Security
- Understanding MAC-based access control
- Understanding Static Secure MAC addresses
- Understanding Sticky Secure MAC addresses
- Configuring maximum allowed MAC addresses
- Understanding Port Security Violation Modes
- Understanding `shutdown`
- Understanding `restrict`
- Understanding `protect`
- Verifying secure MAC addresses
- Verifying Port Security status
- Detecting unauthorized MAC addresses
- Troubleshooting `err-disabled` access ports
- Recovering a secure port
- Verifying authorized device connectivity

---

# 🏢 Company Scenario

A small company uses an access switch to connect employee devices.

Each access port should only be used by the device assigned to that port.

For this lab:

- PC1 is the authorized employee device.
- PC2 represents an unauthorized device.
- SW1 is the access switch.
- Fa0/1 is protected using Port Security.

The objective is to ensure that an unauthorized device cannot use the protected access port.

---

# 🧱 Topology

```text
PC1 ───────────── SW1
                   |
                 Fa0/1
```

An additional PC was introduced during the violation scenario:

```text
PC1 ─────────────┐
                 │
                SW1
                 │
PC2 ─────────────┘
```

PC2 was temporarily connected to the protected Fa0/1 port to simulate an unauthorized device.

---

# 🔌 Device Inventory

## Switch

- SW1 — Cisco 2960

## End Devices

- PC1 — Authorized Device
- PC2 — Unauthorized Device

---

# 🔌 Connections

| Device | Interface | Switch Interface |
|---|---|---|
| PC1 | Fa0 | SW1 Fa0/1 |
| PC2 | Fa0 | SW1 Fa0/2 |

During the Security Violation scenario, PC2 was temporarily moved from Fa0/2 to Fa0/1 while PC1 was disconnected.

---

# 🌐 IP Addressing

## PC1

- IPv4 Address: `192.168.10.10`
- Subnet Mask: `255.255.255.0`
- Default Gateway: `0.0.0.0`

## PC2

- IPv4 Address: `192.168.10.20`
- Subnet Mask: `255.255.255.0`
- Default Gateway: `0.0.0.0`

No Layer 3 gateway was required because the lab uses a single Layer 2 network.

---

# 🔑 MAC Address Information

## PC1

Physical MAC Address:

`0005.5EE8.BD58`

PC1 was initially learned by SW1 on:

`Fa0/1`

## PC2

Physical MAC Address:

`0030.A339.5D1D`

PC2 was initially learned by SW1 on:

`Fa0/2`

---

# 🧠 Port Security Theory

## What is Port Security?

Port Security is a Layer 2 switch security feature used to control which MAC addresses are allowed to use an access port.

Basic concept:

```text
Device
   ↓
MAC Address
   ↓
Switch Port Security
   ↓
Allowed / Violation
```

Unlike an ACL, which filters network traffic, Port Security controls which MAC addresses are allowed on a specific switch port.

---

# 🔹 Static Secure MAC

A Static Secure MAC address is manually configured on the switch.

Example:

```text
Fa0/1
   ↓
Secure MAC
   ↓
0005.5EE8.BD58
```

The switch will allow the configured MAC address on the protected port.

---

# 🔹 Sticky Secure MAC

Sticky MAC allows the switch to learn the MAC address dynamically and use the learned MAC as a secure MAC address.

The learning process is:

```text
PC connects
   ↓
Switch learns MAC
   ↓
MAC becomes SecureSticky
```

This reduces the need to manually enter MAC addresses.

---

# 🔢 Maximum MAC Addresses

Port Security can limit the number of secure MAC addresses allowed on a port.

In this lab:

`Maximum MAC Addresses = 1`

Therefore, only one secure MAC address is allowed on Fa0/1.

---

# 🚨 Port Security Violation

A Security Violation occurs when an unauthorized MAC address attempts to use the protected port.

For this lab:

```text
Authorized MAC:
0005.5EE8.BD58

Unauthorized MAC:
0030.A339.5D1D
```

The unauthorized MAC was detected when PC2 attempted to use Fa0/1.

---

# ⚙️ Violation Modes

Three important Port Security violation modes were reviewed:

## Protect

- Drops traffic from unauthorized MAC addresses
- Port remains operational

## Restrict

- Drops traffic from unauthorized MAC addresses
- Records/counts violations
- Port remains operational

## Shutdown

- Drops unauthorized traffic
- Places the affected port into a shutdown / err-disabled state

The practical violation scenario in this lab used:

**Shutdown Mode**

---

# 🔧 Verification Commands

The following commands were used during the lab:

```cisco
show interfaces fa0/1
show interfaces fa0/1 status
show mac address-table
show mac address-table dynamic
show port-security
show port-security interface fa0/1
show port-security address
show running-config
```

---

# 🧪 Baseline MAC Learning

Before Port Security was enabled, SW1 learned PC1 dynamically.

Example:

```text
MAC Address
0005.5EE8.BD58

Type
DYNAMIC

Port
Fa0/1
```

This established the normal MAC learning behavior before securing the port.

---

# 🛡️ Static Port Security Configuration

Fa0/1 was configured as an access port and protected using a manually configured secure MAC address.

Configuration:

```cisco
interface fa0/1
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address 0005.5ee8.bd58
```

Verification:

```cisco
show port-security interface fa0/1
```

Expected state:

```text
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Shutdown
Maximum MAC Addresses      : 1
Total MAC Addresses        : 1
Configured MAC Addresses   : 1
Security Violation Count   : 0
```

The secure MAC was:

`0005.5EE8.BD58`

---

# 🧪 Authorized Device Test

PC1 was connected to the protected Fa0/1 port.

Connectivity was verified against PC2.

Result:

```text
4 Sent
4 Received
0% Loss
```

The authorized device was able to communicate normally while using the protected port.

---

# 💥 Unauthorized MAC Scenario

PC1 was disconnected from Fa0/1.

PC2 was then connected to Fa0/1.

PC2 had a different MAC address:

`0030.A339.5D1D`

Because Fa0/1 was protected for PC1's secure MAC address, PC2 was treated as an unauthorized device.

A connectivity test was performed from PC2.

Result:

```text
100% loss
```

---

# 🚨 Security Violation Verification

After PC2 attempted to use Fa0/1, SW1 reported:

```text
Port Status               : Secure-shutdown
Violation Mode            : Shutdown
Sticky / Secure MAC       : 1 secure MAC
Last Source Address       : 0030.A339.5D1D
Security Violation Count  : 1
```

The interface status was:

```text
Fa0/1
err-disabled
```

This demonstrated that the switch detected the unauthorized MAC and automatically disabled the protected port.

---

# 🔧 Troubleshooting Workflow

The troubleshooting process used for the Security Violation was:

```text
Connectivity Failure
        ↓
Check Interface Status
        ↓
Identify err-disabled State
        ↓
Check Port Security
        ↓
Identify Security Violation
        ↓
Check Last Source MAC
        ↓
Identify Unauthorized Device
        ↓
Remove the Cause
        ↓
Recover the Port
        ↓
Verify
```

The key evidence included:

- `err-disabled`
- `Secure-shutdown`
- Unauthorized source MAC
- Security Violation Count

---

# 🔄 Port Recovery

After identifying the unauthorized device, the topology was restored:

```text
PC1 → SW1 Fa0/1
PC2 → SW1 Fa0/2
```

The affected interface was then recovered using:

```cisco
configure terminal
interface fa0/1
shutdown
no shutdown
end
```

Verification:

```cisco
show interfaces fa0/1 status
show port-security interface fa0/1
```

Final state:

```text
Fa0/1                 : connected
Port Security         : Enabled
Port Status           : Secure-up
Maximum MAC Addresses : 1
```

---

# 🔐 Sticky MAC Configuration

The manually configured secure MAC address was removed and Sticky MAC was enabled.

Configuration:

```cisco
interface fa0/1
no switchport port-security mac-address 0005.5ee8.bd58
switchport port-security mac-address sticky
```

After traffic was generated from PC1, SW1 learned the MAC as a Sticky Secure MAC.

Verification:

```cisco
show port-security address
```

Result:

```text
MAC Address     Type           Port
0005.5EE8.BD58  SecureSticky   Fa0/1
```

---

# 💥 Sticky MAC Violation

After configuring Sticky MAC, PC1 was disconnected from Fa0/1.

PC2 was connected to the same protected port.

Because the secure Sticky MAC belonged to PC1, PC2's MAC address was unauthorized.

PC2:

`0030.A339.5D1D`

A connectivity test was performed.

The switch detected the violation and entered:

```text
Port Status              : Secure-shutdown
Violation Mode           : Shutdown
Sticky MAC Addresses     : 1
Security Violation Count : 1
```

The interface entered:

```text
err-disabled
```

---

# 🔄 Sticky MAC Recovery

The topology was restored:

```text
PC1 → SW1 Fa0/1
PC2 → SW1 Fa0/2
```

The Fa0/1 interface was recovered using:

```cisco
configure terminal
interface fa0/1
shutdown
no shutdown
end
```

Final verification showed:

```text
Port Status              : Secure-up
Port Security            : Enabled
Sticky MAC Addresses     : 1
Security Violation Count : 0
```

The Sticky Secure MAC remained associated with Fa0/1.

---

# ✅ Final Authorized Device Test

After recovery, PC1 successfully communicated with PC2.

Result:

```text
4 Sent
4 Received
0% Loss
```

This confirmed that the authorized device could use the protected port normally after recovery.

---

# 🧠 Static vs Sticky MAC

| Feature | Static Secure MAC | Sticky Secure MAC |
|---|---|---|
| MAC configuration | Manually configured | Learned dynamically |
| Secure MAC type | SecureConfigured | SecureSticky |
| Manual MAC entry | Required | Not required |
| Main advantage | Precise manual control | Easier deployment |

---

# 🧠 Port Security vs ACL

## Port Security

Controls:

**Which MAC addresses can use a switch port**

## ACL

Controls:

**Which network traffic is permitted or denied**

Conceptually:

```text
Port Security
Device / MAC
      ↓
Access Port Control
```

```text
ACL
Traffic
   ↓
Permit / Deny
```

---

# 🔍 Important Observations

During the lab, the following behavior was observed:

- Normal MAC learning occurs before Port Security is enabled.
- A manually configured secure MAC is shown as `SecureConfigured`.
- A Sticky MAC is shown as `SecureSticky`.
- The maximum number of secure MAC addresses can be restricted.
- An unauthorized MAC can trigger a Security Violation.
- Shutdown mode can place the interface into `err-disabled`.
- The last source MAC helps identify the device that caused the violation.
- Port recovery should be performed after identifying and addressing the cause.
- Verification is required after recovery to confirm the port is operational.

---

# 📊 Final Port Security Design

```text
SW1 Fa0/1

Port Security:
Enabled

Maximum Secure MACs:
1

Violation Mode:
Shutdown

Authorized MAC:
0005.5EE8.BD58

Authorized Device:
PC1

Unauthorized Device:
PC2
MAC: 0030.A339.5D1D
```

---

# 📸 Screenshots

The `Screenshots` directory contains evidence documenting the topology, IP/MAC information, baseline MAC learning, Static Port Security, Security Violation, recovery, Sticky MAC configuration, Sticky MAC violation, and final verification.

Recommended filenames:

```text
01-Topology.png
02-PC1-IP-MAC-Verification.png
03-SW1-Baseline-MAC-Verification.png
04-Static-Port-Security-Verification.png
05-Secure-MAC-Table.png
06-PC2-IP-MAC-Verification.png
07-Baseline-Connectivity.png
08-MAC-Table-Baseline.png
09-Unauthorized-MAC-Violation.png
10-Port-Security-Violation-Verification.png
11-Port-Security-Recovery-Verification.png
12-Authorized-Device-Verification.png
13-Sticky-MAC-Verification.png
14-Sticky-MAC-Violation-Verification.png
15-Sticky-MAC-Recovery-Verification.png
16-Sticky-MAC-Authorized-Device-Verification.png
17-Final-Port-Security-Verification.png
```

---

# 📁 Repository Structure

```text
16-Port-Security/
└── LAB-16-Port-Security/
    ├── Screenshots/
    ├── LAB-16-Port-Security.pkt
    └── README.md
```

---

# 🎯 Lab Workflow

```text
Build Topology
      ↓
Configure PC IP Addressing
      ↓
Identify MAC Addresses
      ↓
Verify Normal MAC Learning
      ↓
Configure Static Port Security
      ↓
Verify Secure MAC
      ↓
Test Authorized Device
      ↓
Introduce Unauthorized Device
      ↓
Trigger Security Violation
      ↓
Diagnose err-disabled Port
      ↓
Recover Port
      ↓
Verify Connectivity
      ↓
Configure Sticky MAC
      ↓
Verify SecureSticky MAC
      ↓
Introduce Unauthorized Device Again
      ↓
Trigger Sticky MAC Violation
      ↓
Recover Port
      ↓
Final Verification
      ↓
Document Results
```

---

# 🚀 Conclusion

LAB 16 demonstrated how Switch Port Security can be used to control access to an Ethernet access port using MAC addresses.

The lab progressed from normal MAC learning to Static Secure MAC configuration, unauthorized device detection, Security Violation handling, port recovery, Sticky MAC configuration, and final verification.

The most important troubleshooting lesson was:

**Do not immediately reset a failed port. Identify the symptom, gather evidence, determine the root cause, fix the cause, recover the interface, and verify the result.**

This lab reinforced:

**Learn → Build → Test → Break → Troubleshoot → Fix → Verify → Document**

---

# 🔥 Day 16 Completed

**LAB 16 — Switch Port Security ✅**

Next:

**LAB 17 — IPv6 Fundamentals**
```