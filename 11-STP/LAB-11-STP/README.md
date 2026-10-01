# LAB 11 — STP (Spanning Tree Protocol)

## Objective

The objective of this lab is to understand how Spanning Tree Protocol (STP) prevents Layer 2 switching loops while maintaining redundant paths.

The lab focuses on observing default STP behavior in a redundant switch topology, identifying the Root Bridge and port roles, testing automatic failover after a link failure, and verifying recovery after the failed link is restored.

No manual STP configuration was performed during the main lab. The purpose was to observe how STP operates automatically and how it reacts to topology changes.

---

## Scenario

A small company network contains three Cisco switches connected in a triangle.

The triangle provides redundancy because there is more than one path between switches. However, the redundant topology can also create a Layer 2 loop.

STP automatically creates a loop-free logical topology by:

- Selecting a Root Bridge
- Selecting the best path toward the Root Bridge
- Assigning port roles
- Blocking a redundant path
- Activating the alternate path when the primary path fails

---

## Topology

    PC-A → SW1 Fa0/1
    PC-B → SW2 Fa0/1

    SW1 Fa0/23 ↔ SW3
    SW1 Fa0/24 ↔ SW2
    SW2 Fa0/23 ↔ SW3

The physical topology forms a triangle:

                SW1
               /   \
              /     \
            SW2─────SW3
             │
           PC-B

    PC-A → SW1

---

## Devices

- 3 × Cisco 2960 Switches
- 2 × PCs

Devices:

    SW1
    SW2
    SW3
    PC-A
    PC-B

---

## IP Addressing

Because this lab focuses on Layer 2 switching and STP, both PCs were placed in the same IPv4 subnet.

### PC-A

    IP Address:      192.168.10.10
    Subnet Mask:     255.255.255.0
    Default Gateway: 0.0.0.0

### PC-B

    IP Address:      192.168.10.20
    Subnet Mask:     255.255.255.0
    Default Gateway: 0.0.0.0

No default gateway was required because both PCs are in the same subnet and no router is used in this lab.

---

## Key STP Concepts

### Layer 2 Loop

The three switches create multiple Layer 2 paths.

Without loop prevention, Ethernet frames such as broadcasts could circulate through the redundant paths and cause:

- Broadcast storms
- Duplicate frames
- MAC address table instability
- Excessive Layer 2 traffic

### Spanning Tree Protocol

STP allows redundant links to physically exist while preventing a Layer 2 forwarding loop.

The basic process is:

    Redundant Topology
            ↓
      Root Election
            ↓
      Path Calculation
            ↓
      Port Roles
            ↓
    Redundant Path Blocked
            ↓
      Loop-Free Topology

---

## Root Bridge

The Root Bridge is the reference point used by STP when building the spanning tree.

The switch with the lowest Bridge ID is selected as the Root Bridge.

In this lab:

    SW3 = Root Bridge

This was confirmed by:

    This bridge is the root

The Root Bridge information observed on SW3 was:

    Priority: 32769
    MAC Address: 000B.BE53.B0CD

---

## Root Port

A Root Port is the best path from a non-Root switch toward the Root Bridge.

Initially:

    SW1 Fa0/24 → Root Port → Forwarding
    SW2 Fa0/24 → Root Port → Forwarding

The Root Bridge itself does not have a Root Port.

---

## Designated Port

A Designated Port is the forwarding port selected for a Layer 2 segment.

On the Root Bridge, the inter-switch ports were observed as Designated and Forwarding.

SW3:

    Fa0/23 → Designated → Forwarding
    Fa0/24 → Designated → Forwarding

---

## Alternate / Blocking Port

SW2 initially had:

    Fa0/23 → Alternate → Blocking

This was the redundant path.

The physical link remained available, but STP prevented normal forwarding through that path to avoid creating a Layer 2 loop.

Blocking a port does not mean that the physical cable is disconnected.

---

# STP Verification

## Step 1 — Verify STP on SW1

Command:

    show spanning-tree

Observed:

    SW1 = Non-Root Switch
    Fa0/24 → Root → Forwarding
    Fa0/23 → Designated → Forwarding
    Fa0/1  → Designated → Forwarding

The Root ID shown on SW1 matched the Bridge ID of SW3.

This confirmed that SW1 was a non-Root switch and that Fa0/24 was its best path toward the Root Bridge.

---

## Step 2 — Verify STP on SW2

Command:

    show spanning-tree

Observed:

    SW2 = Non-Root Switch

    Fa0/24 → Root → Forwarding
    Fa0/1  → Designated → Forwarding
    Fa0/23 → Alternate → Blocking

This confirmed that STP had blocked the redundant path through Fa0/23.

---

## Step 3 — Verify STP on SW3

Command:

    show spanning-tree

Observed:

    This bridge is the root

SW3 was therefore confirmed as the Root Bridge.

Observed ports:

    Fa0/23 → Designated → Forwarding
    Fa0/24 → Designated → Forwarding

---

## Step 4 — Detailed STP Verification on SW3

Command:

    show spanning-tree detail

Observed:

    We are the root of the spanning tree

Root information:

    Priority: 32769
    MAC Address: 000B.BE53.B0CD

Ports:

    Fa0/23 → Designated → Forwarding
    Fa0/24 → Designated → Forwarding

---

## Step 5 — Detailed STP Verification on SW2

Command:

    show spanning-tree detail

Observed:

    Root Port: Fa0/24
    Root Path Cost: 19

And:

    Fa0/23 → Alternate → Blocking

This confirmed that SW2 had selected Fa0/24 as its best path to the Root Bridge while keeping Fa0/23 as the redundant blocked path.

---

## Step 6 — STP Summary Verification

Command:

    show spanning-tree summary

Observed:

    Switch is in pvst mode

Port state summary:

    Blocking:   1
    Listening:  0
    Learning:   0
    Forwarding: 2
    STP Active: 3

This confirmed that SW2 had two forwarding ports and one blocking port.

---

# Baseline Connectivity

Before introducing a failure, connectivity between the two PCs was tested.

From PC-A:

    ping 192.168.10.20

Result:

    4/4 replies
    0% loss

This established a working baseline.

---

# Intentional Failure

To test STP failover, the primary Root Port on SW2 was intentionally disabled.

The interface was:

    SW2 Fa0/24

Configuration:

    enable
    configure terminal
    interface fa0/24
    shutdown
    end

This removed the primary forwarding path toward the Root Bridge.

---

## Primary Link Failure

The SW2 Fa0/24 link was intentionally shut down.

Observed:

    SW2 Fa0/24 → Down

---

# STP Failover

After the primary path failed, STP recalculated the topology.

Command:

    show spanning-tree

Observed on SW2:

    Fa0/23 → Root → Forwarding

Before the failure:

    Fa0/24 → Root → Forwarding
    Fa0/23 → Alternate → Blocking

After the failure:

    Fa0/24 → Down
    Fa0/23 → Root → Forwarding

The Root Path Cost became:

    38

The new path to the Root Bridge became:

    SW2
      ↓
    SW1
      ↓
    SW3

This demonstrated that STP automatically activated the previously blocked redundant path.

---

# Failover Connectivity Verification

After the failover, connectivity was tested again.

From PC-A:

    ping 192.168.10.20

Result:

    4/4 replies
    0% loss

The network remained operational even though the original path had failed.

---

# Primary Link Recovery

The original link was restored.

On SW2:

    enable
    configure terminal
    interface fa0/24
    no shutdown
    end

After STP reconvergence, the original topology was restored.

---

# STP Recovery Verification

Command:

    show spanning-tree

Observed:

    Fa0/24 → Root → Forwarding
    Fa0/23 → Alternate → Blocking

This matched the original STP state.

The primary path was restored and the redundant path returned to the blocking state.

---

# Final Connectivity Verification

After the topology recovered, connectivity was tested again.

From PC-A:

    ping 192.168.10.20

Result:

    4/4 replies
    0% loss

This confirmed that the network remained operational after both failure and recovery.

---

# STP State Changes

## Normal State

    SW3 = Root Bridge

    SW1:
    Fa0/24 → Root → Forwarding
    Fa0/23 → Designated → Forwarding

    SW2:
    Fa0/24 → Root → Forwarding
    Fa0/23 → Alternate → Blocking

---

## During Failure

    SW2 Fa0/24 → Down

    SW2 Fa0/23:
    Alternate → Root
    Blocking → Forwarding

---

## After Recovery

    SW2 Fa0/24:
    Root → Forwarding

    SW2 Fa0/23:
    Root → Alternate
    Forwarding → Blocking

---

# Important Observations

## STP Operated Automatically

No manual STP configuration was required during the main lab.

After connecting the redundant switches, STP automatically:

    Detected the redundant topology
            ↓
    Selected the Root Bridge
            ↓
    Selected Root Ports
            ↓
    Selected Designated Ports
            ↓
    Blocked the redundant path

---

## Redundancy vs Loop Prevention

STP does not remove the physical redundancy.

Instead:

    Redundant Links → Kept physically available
    Layer 2 Loop    → Prevented logically

When the primary path failed:

    Blocked Backup Path
            ↓
        STP Recalculation
            ↓
     Backup Path Forwarding

When the primary path returned:

    Primary Path Restored
            ↓
     Backup Path Blocking

---

## STP vs Routing

STP operates at:

    Layer 2

Its main purpose is:

    Prevent Layer 2 Switching Loops

Routing operates at:

    Layer 3

Its main purpose is:

    Forward traffic between different IP networks.

---

# Verification Commands Used

### STP

    show spanning-tree
    show spanning-tree detail
    show spanning-tree summary

### Connectivity

    ping 192.168.10.20

---

# Troubleshooting / Failover Method

The failure scenario followed this workflow:

    1. Establish a working baseline
    2. Identify the current Root Port
    3. Disable the primary link intentionally
    4. Observe the topology change
    5. Check STP state
    6. Identify the new Root Port
    7. Verify connectivity
    8. Restore the failed link
    9. Verify STP recovery
    10. Verify final connectivity

---

# Evidence

Screenshots captured during the lab:

    Topology.png
    SW1-STP-Verification.png
    SW2-STP-Verification.png
    SW3-STP-Verification.png
    SW3-STP-Detail.png
    SW2-STP-Detail.png
    STP-SW2-Summary.png
    Baseline-Connectivity.png
    Primary-Link-Failure.png
    STP-Failover-Verification.png
    STP-Failover-Connectivity.png
    STP-Recovery-Verification.png
    Final-Recovery-Connectivity.png

---

# Files

    11-STP/
    └── LAB-11-STP/
        ├── LAB-11-STP.pkt
        ├── README.md
        └── Screenshots/

---

# Final Result

The lab successfully demonstrated:

    STP Operation                    ✅
    Root Bridge Election             ✅
    Root Port Identification         ✅
    Designated Port Identification   ✅
    Alternate/Blocking Port          ✅
    Layer 2 Loop Prevention          ✅
    Baseline Connectivity            ✅
    Intentional Link Failure         ✅
    Automatic STP Failover           ✅
    Failover Connectivity            ✅
    Primary Link Recovery            ✅
    STP Recovery Verification        ✅
    Final Connectivity Verification  ✅

**Status: COMPLETED ✅**