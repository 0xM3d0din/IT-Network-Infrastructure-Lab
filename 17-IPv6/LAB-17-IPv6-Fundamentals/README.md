# LAB 17 — IPv6 Fundamentals

## 📌 Overview

This lab introduces IPv6 fundamentals and demonstrates IPv6 addressing, IPv6 routing, Link-Local addressing, ICMPv6, Neighbor Discovery (NDP), Static IPv6 configuration, and SLAAC.

The lab uses a simple routed IPv6 topology to compare manually configured IPv6 addressing with stateless automatic address configuration.

Learning approach:

**Learn → Build → Configure → Test → Verify → Troubleshoot → Document**

---

# 🎯 Objectives

By completing this lab, I practiced:

- IPv6 addressing
- 128-bit IPv6 addresses
- Hexadecimal notation
- IPv6 prefix length
- Global Unicast addressing
- Link-Local addressing
- IPv6 default gateways
- ICMPv6
- Neighbor Discovery Protocol (NDP)
- Static IPv6 configuration
- SLAAC
- IPv6 routing
- IPv6 connectivity verification
- IPv6 address troubleshooting

---

# 🏢 Company Scenario

A small enterprise network contains two IPv6 LANs connected through a Layer 3 router.

The objective is to establish IPv6 connectivity between the two networks and then demonstrate how IPv6 hosts can operate with:

- Static IPv6 configuration
- Link-Local communication
- Neighbor Discovery
- SLAAC
- Routed IPv6 connectivity

---

# 🧱 Topology

```text
PC1 ─── SW1 ─── R1 ─── SW2 ─── PC2
```

The topology contains:

- PC1
- SW1
- R1
- SW2
- PC2

---

# 🔌 Device Inventory

## Router

- R1 — Cisco 2911

## Switches

- SW1 — Cisco 2960
- SW2 — Cisco 2960

## End Devices

- PC1
- PC2

---

# 🔌 Connections

| Device | Interface | Connected To |
|---|---|---|
| PC1 | Fa0 | SW1 Fa0/1 |
| SW1 | Fa0/24 | R1 G0/0 |
| R1 | G0/1 | SW2 Fa0/24 |
| SW2 | Fa0/1 | PC2 Fa0 |

All Ethernet connections used Copper Straight-Through cables.

---

# 🌐 IPv6 Addressing Plan

## USER LAN

Network:

`2001:DB8:10:1::/64`

### PC1

- IPv6 Address: `2001:DB8:10:1::10/64`
- Default Gateway: `2001:DB8:10:1::1`

### R1 G0/0

- Global Unicast: `2001:DB8:10:1::1/64`
- Link-Local: `FE80::1`

---

## REMOTE LAN

Network:

`2001:DB8:20:1::/64`

### PC2

Initial Static IPv6:

`2001:DB8:20:1::10/64`

Initial Default Gateway:

`2001:DB8:20:1::1`

### R1 G0/1

- Global Unicast: `2001:DB8:20:1::1/64`
- Link-Local: `FE80::2`

---

# 🧠 IPv6 Fundamentals

## IPv6 Address Size

IPv6 uses:

**128-bit addresses**

Example:

`2001:DB8:10:1::10`

IPv6 addresses are written using hexadecimal notation.

---

# 🔹 Prefix Length

IPv6 uses prefix length notation instead of the traditional IPv4 subnet mask format.

Example:

`2001:DB8:10:1::10/64`

The `/64` represents the network prefix length.

---

# 🔹 Global Unicast

Global Unicast addresses are IPv6 addresses used for routed communication across networks.

Example:

`2001:DB8:10:1::10`

---

# 🔹 Link-Local

Link-Local addresses are automatically available for communication on the local link.

The Link-Local range begins with:

`FE80::/10`

In this lab, R1 used:

- `FE80::1` on G0/0
- `FE80::2` on G0/1

---

# 🔹 ICMPv6

IPv6 uses ICMPv6 for important control and diagnostic functions.

Ping testing in this lab used ICMPv6 to verify IPv6 connectivity.

---

# 🔹 Neighbor Discovery Protocol

IPv6 does not use ARP in the same way as IPv4.

Instead, IPv6 uses:

**Neighbor Discovery Protocol (NDP)**

NDP operates using ICMPv6 and helps IPv6 devices discover and maintain information about neighboring devices.

---

# 🔹 SLAAC

SLAAC stands for:

**Stateless Address Autoconfiguration**

SLAAC allows an IPv6 host to automatically configure an address using information advertised by an IPv6 router.

In this lab, PC2 was changed from Static IPv6 configuration to automatic configuration.

---

# 🔧 R1 IPv6 Configuration

IPv6 routing was enabled on R1.

The router interfaces were configured with Global Unicast and Link-Local addresses.

The resulting interface configuration was:

### G0/0

- `2001:DB8:10:1::1/64`
- `FE80::1`

### G0/1

- `2001:DB8:20:1::1/64`
- `FE80::2`

The interfaces were brought up using `no shutdown`.

---

# ✅ IPv6 Interface Verification

The following command was used:

`show ipv6 interface brief`

Final expected state:

- GigabitEthernet0/0 → `up/up`
- GigabitEthernet0/1 → `up/up`

The router showed:

`2001:DB8:10:1::1`

on G0/0 and:

`2001:DB8:20:1::1`

on G0/1.

---

# 🧪 Host IPv6 Verification

PC1 was configured with:

`2001:DB8:10:1::10/64`

PC1 default gateway:

`2001:DB8:10:1::1`

PC2 was initially configured with:

`2001:DB8:20:1::10/64`

PC2 default gateway:

`2001:DB8:20:1::1`

The host configurations were verified with:

`ipconfig`

and:

`ipconfig /all`

---

# 🔍 Addressing Verification and Correction

During setup, the router interface prefixes were verified against the host configurations.

A prefix mismatch was identified between the configured router addresses and the host gateways.

The mismatch was corrected so that:

- R1 G0/0 matched the `2001:DB8:10:1::/64` network
- R1 G0/1 matched the `2001:DB8:20:1::/64` network

After correction, both router interfaces were operational and gateway connectivity was restored.

This reinforced an important troubleshooting concept:

**IPv6 addressing and prefix consistency must be verified before diagnosing higher-layer connectivity problems.**

---

# 🧪 Gateway Connectivity Tests

## PC1 → R1 G0/0

PC1 successfully pinged:

`2001:DB8:10:1::1`

Result:

**4 Sent / 4 Received / 0% Loss**

## PC2 → R1 G0/1

PC2 successfully pinged:

`2001:DB8:20:1::1`

Result:

**4 Sent / 4 Received / 0% Loss**

---

# 🌐 IPv6 Routing Verification

PC1 successfully communicated with PC2 across R1.

Destination:

`2001:DB8:20:1::10`

Result:

**4 Sent / 4 Received / 0% Loss**

This demonstrated routed IPv6 communication between the two IPv6 networks.

The reply TTL was observed as:

`127`

showing that the packet crossed a Layer 3 router.

---

# 🔎 NDP Verification

After IPv6 connectivity was established, R1 was checked for IPv6 neighbor information.

Command:

`show ipv6 neighbors`

R1 displayed reachable neighbor entries for both hosts.

Observed information included:

- IPv6 Address
- Link-Layer Address
- State
- Interface

PC1 was learned through G0/0.

PC2 was learned through G0/1.

This provided practical evidence of IPv6 Neighbor Discovery.

---

# 🌐 SLAAC Verification

R1 G0/1 was verified before moving PC2 to automatic configuration.

Command:

`show ipv6 interface g0/1`

The interface showed the IPv6 prefix:

`2001:DB8:20:1::/64`

and indicated that hosts could use stateless autoconfiguration.

The interface also used:

`FE80::2`

as its Link-Local address.

---

# ⚙️ PC2 SLAAC

PC2 was changed from Static IPv6 configuration to automatic IPv6 configuration.

After SLAAC, PC2 automatically obtained:

### Global IPv6 Address

`2001:DB8:20:1:2E0:F7FF:FEE7:B28D`

### Link-Local Address

`FE80::2E0:F7FF:FEE7:B28D`

### Default Gateway

`FE80::2`

The new Global IPv6 address used the advertised network prefix together with an automatically generated interface identifier.

---

# 🧪 SLAAC Routing Verification

After PC2 moved to SLAAC, PC1 successfully pinged the new IPv6 address of PC2.

Destination:

`2001:DB8:20:1:2E0:F7FF:FEE7:B28D`

Result:

**4 Sent / 4 Received / 0% Loss**

This confirmed that IPv6 routing continued to work after changing PC2 from Static IPv6 configuration to SLAAC.

---

# 🧠 Key Concepts Reinforced

## IPv4 vs IPv6

IPv4:

- 32-bit addressing
- Decimal notation
- ARP
- Subnet Mask

IPv6:

- 128-bit addressing
- Hexadecimal notation
- Neighbor Discovery
- Prefix Length

---

# 🧠 Important IPv6 Relationships

`IPv4 → ARP`

`IPv6 → Neighbor Discovery / ICMPv6`

`IPv4 Subnet Mask → IPv6 Prefix Length`

`Static IPv6 → Manually configured`

`SLAAC → Stateless automatic configuration`

---

# 🔧 Troubleshooting Mindset

When IPv6 connectivity fails, the following checks are useful:

1. Verify physical connectivity.
2. Verify the IPv6 address.
3. Verify the prefix length.
4. Verify the default gateway.
5. Verify Link-Local addressing.
6. Verify Neighbor Discovery.
7. Verify routing.
8. Test connectivity again.

The addressing mismatch encountered during setup demonstrated why configuration verification should happen before assuming a routing problem.

---

# 🔍 Verification Commands

Commands used during the lab included:

`show ipv6 interface brief`

`show ipv6 interface g0/1`

`show ipv6 neighbors`

`ipconfig`

`ipconfig /all`

`ping`

---

# 📊 Final IPv6 Design

```text
USER LAN
2001:DB8:10:1::/64

PC1
2001:DB8:10:1::10/64

R1 G0/0
2001:DB8:10:1::1/64
FE80::1


REMOTE LAN
2001:DB8:20:1::/64

R1 G0/1
2001:DB8:20:1::1/64
FE80::2

PC2 — SLAAC
2001:DB8:20:1:2E0:F7FF:FEE7:B28D
FE80::2E0:F7FF:FEE7:B28D
Gateway: FE80::2
```

---

# ✅ Lab Results

The lab successfully demonstrated:

- IPv6 static addressing
- Global Unicast addressing
- Link-Local addressing
- IPv6 prefix length
- IPv6 default gateway
- ICMPv6 connectivity testing
- IPv6 Neighbor Discovery
- IPv6 neighbor table verification
- IPv6 routing
- SLAAC
- Automatic IPv6 address generation
- IPv6 gateway discovery
- End-to-end IPv6 connectivity
- Addressing troubleshooting

---

# 📸 Screenshots

The `Screenshots` directory contains the following evidence:

`01-Topology.png`

`02-R1-IPv6-Interface-Verification.png`

`03-PC1-IPv6-Verification.png`

`04-PC2-IPv6-Verification.png`

`05-PC1-IPv6-Gateway-Ping.png`

`06-PC2-IPv6-Gateway-Ping.png`

`07-IPv6-Routing-Verification.png`

`08-PC1-IPv6-NDP-Preparation.png`

`09-NDP-Neighbor-Verification.png`

`10-SLAAC-Router-Advertisement-Verification.png`

`11-PC2-SLAAC-Verification.png`

`12-SLAAC-IPv6-Routing-Verification.png`

---

# 📁 Repository Structure

```text
17-IPv6/
└── LAB-17-IPv6-Fundamentals/
    ├── Screenshots/
    ├── LAB-17-IPv6-Fundamentals.pkt
    └── README.md
```

---

# 🎯 Lab Workflow

```text
Build Topology
      ↓
Configure IPv6 Addresses
      ↓
Enable IPv6 Routing
      ↓
Verify Router Interfaces
      ↓
Verify Host IPv6 Configuration
      ↓
Test IPv6 Gateways
      ↓
Verify End-to-End IPv6 Routing
      ↓
Verify NDP Neighbors
      ↓
Verify Router Advertisements
      ↓
Configure PC2 for SLAAC
      ↓
Verify Automatically Generated IPv6 Address
      ↓
Verify SLAAC Gateway
      ↓
Test End-to-End Connectivity Again
      ↓
Document Results
```

---

# 🚀 Conclusion

LAB 17 introduced IPv6 as a practical network infrastructure technology rather than only a theoretical addressing topic.

The lab demonstrated how IPv6 addresses are structured, how Link-Local addressing works, how Neighbor Discovery replaces traditional ARP-based behavior, how routers provide IPv6 connectivity between networks, and how SLAAC can automatically configure IPv6 hosts.

The lab also reinforced an important troubleshooting principle:

**Verify the addressing and prefix first, then investigate gateway, NDP, and routing behavior.**

---

# 🔥 Day 17 Completed

**LAB 17 — IPv6 Fundamentals ✅**

**Next:**  
**DAY 18 — Networking Review & Knowledge Gaps 🧠**
```
