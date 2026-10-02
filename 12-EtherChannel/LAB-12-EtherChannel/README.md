# LAB 12 — EtherChannel

## Objective

The objective of this lab is to understand and implement EtherChannel using LACP.

The lab demonstrates how multiple physical links between two switches can be combined into one logical Port-Channel, how LACP negotiation works, and how EtherChannel changes the way STP views the links.

The lab also compares the main EtherChannel methods: PAgP, LACP, and Static/On.

---

## Scenario

A small company network contains two switches connected using two physical links.

Without EtherChannel, STP sees the two physical links as separate Layer 2 paths and may place one link into a blocking state to prevent a switching loop.

EtherChannel combines the physical links into a single logical interface called a Port-Channel.

The lab uses LACP to create:

    SW1 Fa0/23 + Fa0/24
            ↓
        Port-Channel 1
            ↓
    SW2 Fa0/23 + Fa0/24

This allows both physical links to participate in the logical EtherChannel while STP treats the bundle as one logical path.

---

## Topology

    PC-A
    192.168.10.10/24
         |
       SW1
      /   \
     /     \
Fa0/23     Fa0/24
     \     /
      \   /
       \ /
       / \
      /   \
Fa0/23     Fa0/24
     \     /
       SW2
         |
       PC-B
    192.168.10.20/24

Logical connection:

    SW1
    Fa0/23 ─┐
            ├── Port-Channel 1 ── SW2
    Fa0/24 ─┘

Protocol:

    LACP

---

## Devices

- 2 × Cisco 2960 Switches
- 2 × PCs

Devices:

    SW1
    SW2
    PC-A
    PC-B

---

## IP Addressing

Both PCs were placed in the same IPv4 subnet because the lab focuses on Layer 2 switching, STP, and EtherChannel rather than routing.

### PC-A

    IP Address:      192.168.10.10
    Subnet Mask:     255.255.255.0
    Default Gateway: 0.0.0.0

### PC-B

    IP Address:      192.168.10.20
    Subnet Mask:     255.255.255.0
    Default Gateway: 0.0.0.0

Network:

    192.168.10.0/24

No default gateway was required because both PCs are in the same subnet.

---

# EtherChannel Concepts

## EtherChannel

EtherChannel combines multiple physical interfaces into one logical interface.

    Multiple Physical Links
             ↓
         EtherChannel
             ↓
      One Logical Link
             ↓
       Port-Channel

The physical links remain separate at the hardware level, but they operate together as one logical bundle.

---

## Why EtherChannel Is Useful

EtherChannel provides:

- Increased aggregate bandwidth
- Link redundancy
- Better utilization of multiple physical links
- A logical interface that STP can treat as a single path

---

## EtherChannel and STP

Before EtherChannel, STP saw the physical links independently.

Example:

    Fa0/23 → Forwarding
    Fa0/24 → Blocking

After EtherChannel:

    Fa0/23 ─┐
            ├── Port-Channel 1
    Fa0/24 ─┘

STP sees the Port-Channel as a logical link instead of treating the member links as independent redundant paths.

EtherChannel does not eliminate STP.

Instead:

    Multiple Physical Links
             ↓
        EtherChannel
             ↓
      One Logical Link
             ↓
            STP

---

# EtherChannel Methods

## PAgP

PAgP stands for Port Aggregation Protocol.

It is a Cisco proprietary negotiation protocol.

Modes:

    Desirable → actively attempts negotiation
    Auto      → waits for negotiation

Examples:

    Desirable + Auto
    → EtherChannel can form

    Desirable + Desirable
    → EtherChannel can form

    Auto + Auto
    → EtherChannel does not form

---

## LACP

LACP stands for Link Aggregation Control Protocol.

It is an open standard used to negotiate EtherChannel formation.

Modes:

    Active   → actively attempts negotiation
    Passive  → waits for negotiation

Examples:

    Active + Active
    → EtherChannel can form

    Active + Passive
    → EtherChannel can form

    Passive + Passive
    → EtherChannel does not form

The practical lab used:

    SW1 → LACP Active
    SW2 → LACP Passive

---

## Static / On

The On mode creates EtherChannel without using a negotiation protocol.

There is no:

    PAgP
    LACP
    Negotiation

Both sides are configured manually.

Example:

    SW1 → On
    SW2 → On

The physical interfaces must be configured consistently because there is no negotiation protocol to establish or validate the bundle.

---

# Baseline Verification

Before configuring EtherChannel, connectivity was tested.

From PC-A:

    ping 192.168.10.20

Result:

    4/4 replies
    0% loss

This established a working baseline.

---

# STP Before EtherChannel

Before creating the EtherChannel, STP was checked on SW1.

Command:

    show spanning-tree

Observed behavior:

    Fa0/23 → Root Port → Forwarding
    Fa0/24 → Alternate → Blocking
    Fa0/1  → Designated → Forwarding

This demonstrated that STP treated the two physical links as separate Layer 2 paths and blocked one of them to prevent a loop.

---

# LACP Configuration

## Step 1 — Configure LACP on SW1

The two physical interfaces were added to Port-Channel 1 using LACP Active mode.

    enable
    configure terminal

    interface range fa0/23 - 24
    channel-group 1 mode active
    exit

    end

Verification:

    show etherchannel summary

Initial result on SW1 showed:

    Po1(SD)
    Protocol: LACP
    Fa0/23(I)
    Fa0/24(I)

At this stage, the physical interfaces were not yet bundled because SW2 had not been configured.

The member interfaces were therefore shown as stand-alone.

---

## Step 2 — Configure LACP on SW2

The same physical interfaces were added to Port-Channel 1 using LACP Passive mode.

    enable
    configure terminal

    interface range fa0/23 - 24
    channel-group 1 mode passive
    exit

    end

Verification:

    show etherchannel summary

Observed:

    Po1(SU)
    Protocol: LACP
    Fa0/23(P)
    Fa0/24(P)

---

## Step 3 — Verify EtherChannel on SW1

Command:

    show etherchannel summary

Observed:

    Po1(SU)
    Protocol: LACP
    Fa0/23(P)
    Fa0/24(P)

Interpretation:

    S → Layer 2 EtherChannel
    U → In Use / Up
    P → Port is bundled in the Port-Channel

The EtherChannel was therefore successfully established from the SW1 side.

---

## Step 4 — Verify EtherChannel on SW2

Command:

    show etherchannel summary

Observed:

    Po1(SU)
    Protocol: LACP
    Fa0/23(P)
    Fa0/24(P)

This confirmed that both physical links were successfully bundled into Port-Channel 1.

---

# EtherChannel Structure

The final logical design was:

    SW1
    Fa0/23 ─┐
            │
            ├── Po1
            │
    Fa0/24 ─┘
               │
               │
             SW2
    Fa0/23 ─┐
            │
            ├── Po1
            │
    Fa0/24 ─┘

The two physical links now operated as one logical EtherChannel.

---

# STP After EtherChannel

After LACP successfully formed Port-Channel 1, STP was checked again.

On SW1:

    show spanning-tree

Observed:

    Port-channel 1 → Root → Forwarding

The physical member interfaces were no longer treated as independent STP paths.

Instead, STP viewed:

    Po1

as the logical connection between the switches.

The observed Port-Channel cost was:

    12

---

## STP Verification on SW2

On SW2:

    show spanning-tree

Observed:

    This bridge is the root

Therefore:

    SW2 = Root Bridge

The Port-Channel was:

    Po1 → Designated → Forwarding

The logical path was therefore:

    SW2
     ↓
    Po1
     ↓
    SW1

---

# Important Observations

## Before EtherChannel

    SW1 Fa0/23 → Root / Forwarding
    SW1 Fa0/24 → Alternate / Blocking

STP saw two separate physical links.

---

## After EtherChannel

    Fa0/23 ─┐
            ├── Po1 → Root / Forwarding
    Fa0/24 ─┘

STP saw one logical Port-Channel.

This allowed both physical links to participate in the EtherChannel instead of having one of the physical paths independently blocked by STP.

---

# Bandwidth Concept

EtherChannel provides aggregate bandwidth based on the combined physical links.

For example:

    4 × 100 Mbps
    = 400 Mbps aggregate capacity

However, this does not mean that one individual traffic flow must use all four physical links simultaneously.

Traffic distribution is performed using load-balancing/hash mechanisms.

A simplified example:

    Flow A → Link 1
    Flow B → Link 2
    Flow C → Link 3
    Flow D → Link 4

The exact distribution depends on the hashing and platform configuration.

---

# Compatibility Requirements

Member interfaces intended for the same EtherChannel should have compatible configuration.

Important settings can include:

    Speed
    Duplex
    Access/Trunk Mode
    VLAN Configuration
    Native VLAN
    Allowed VLANs

Configuration mismatches can prevent the EtherChannel from forming correctly or cause member interfaces to remain outside the bundle.

---

# Verification Commands Used

## EtherChannel

    show etherchannel summary

## Port-Channel

    show interfaces port-channel 1

## Interfaces

    show interfaces status

## STP

    show spanning-tree

## Connectivity

    ping 192.168.10.20

---

# Troubleshooting Method Practiced

The lab followed this workflow:

    1. Establish baseline connectivity
    2. Verify STP before EtherChannel
    3. Configure LACP on SW1
    4. Observe the initial unbundled state
    5. Configure LACP on SW2
    6. Verify successful EtherChannel formation
    7. Verify member interfaces
    8. Verify STP after EtherChannel
    9. Confirm Port-Channel operation

---

# Key Concepts Learned

    Multiple Physical Links
            ↓
        EtherChannel
            ↓
      One Logical Link
            ↓
       Port-Channel
            ↓
       STP sees one
       logical path

Important distinctions:

    PAgP
    → Cisco proprietary
    → Desirable / Auto

    LACP
    → Open standard
    → Active / Passive

    On
    → No negotiation
    → Static EtherChannel

LACP configuration used in this lab:

    SW1 → Active
    SW2 → Passive

---

# Evidence

The following screenshots were captured during the lab:

    Topology.png
    Baseline-Connectivity.png
    STP-Baseline-Before-EtherChannel.png
    LACP-SW1-Initial-Configuration.png
    LACP-SW2-Verification.png
    LACP-SW1-Verification.png
    STP-After-EtherChannel.png
    SW2-STP-After-EtherChannel.png

Additional screenshots related to the lab may also be stored in the Screenshots directory.

---

# Files

    12-EtherChannel/
    └── LAB-12-EtherChannel/
        ├── LAB-12-EtherChannel.pkt
        ├── README.md
        └── Screenshots/

---

# Final Result

The lab successfully demonstrated:

    EtherChannel Fundamentals          ✅
    PAgP Overview                     ✅
    LACP Overview                     ✅
    Static / On Overview              ✅
    LACP Active/Passive Modes         ✅
    Baseline Connectivity             ✅
    STP Before EtherChannel           ✅
    LACP Negotiation                  ✅
    EtherChannel Formation            ✅
    Port-Channel Verification         ✅
    Member Port Verification          ✅
    STP After EtherChannel            ✅
    Logical Link Concept              ✅

The lab was completed using LACP with:

    SW1 → Active
    SW2 → Passive

Fa0/23 and Fa0/24 successfully formed:

    Port-Channel 1

**Status: COMPLETED ✅**