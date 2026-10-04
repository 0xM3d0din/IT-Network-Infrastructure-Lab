# LAB 14 — NAT / PAT

## Objective

Build and configure a small enterprise network to understand and practice:

- Static NAT
- Dynamic NAT
- PAT / NAT Overload
- Inside Local / Inside Global
- NAT Inside / Outside interfaces
- NAT translation tables
- NAT statistics
- Routing requirements for NAT
- NAT verification and troubleshooting

The lab demonstrates how private internal hosts can communicate with an outside network using NAT and PAT.

---

# Company Scenario

A small company has two internal PCs that need to communicate with an external server.

The internal network uses private IPv4 addressing.

R1 acts as the main NAT router.

R2 represents the outside / ISP-side router.

The lab demonstrates three NAT approaches:

1. Static NAT for a fixed 1:1 mapping
2. Dynamic NAT using a public IP pool
3. PAT / NAT Overload allowing multiple internal hosts to share one outside interface address

---

# Topology

```text
PC1 --------\
             \
              SW1 -------- R1 -------- R2 -------- SW2 -------- Server
             /             NAT Router     ISP
PC2 --------/                |
                          Inside / Outside
```

Detailed connections:

```text
PC1 Fa0      → SW1 Fa0/1
PC2 Fa0      → SW1 Fa0/2
SW1 Fa0/24   → R1 G0/0
R1 G0/1      → R2 G0/0
R2 G0/1      → SW2 Fa0/24
SW2 Fa0/1    → Server Fa0
```

---

# Cable Types

```text
PC ↔ Switch       = Copper Straight-Through
Switch ↔ Router  = Copper Straight-Through
Router ↔ Router   = Copper Cross-Over
Switch ↔ Server   = Copper Straight-Through
```

---

# Devices

- 2 × Cisco 2911 Routers
- 2 × Cisco 2960 Switches
- 2 × PCs
- 1 × Server

---

# IP Addressing

## Inside LAN

Network:

```text
192.168.10.0/24
```

### PC1

```text
IP Address:
192.168.10.10

Subnet Mask:
255.255.255.0

Default Gateway:
192.168.10.1
```

### PC2

```text
IP Address:
192.168.10.20

Subnet Mask:
255.255.255.0

Default Gateway:
192.168.10.1
```

### R1 G0/0 — Inside

```text
IP Address:
192.168.10.1

Subnet Mask:
255.255.255.0
```

### R1 G0/1 — Outside

```text
IP Address:
10.0.0.1

Subnet Mask:
255.255.255.0
```

### R2 G0/0

```text
IP Address:
10.0.0.2

Subnet Mask:
255.255.255.0
```

### R2 G0/1

```text
IP Address:
198.51.100.1

Subnet Mask:
255.255.255.0
```

### Server

```text
IP Address:
198.51.100.10

Subnet Mask:
255.255.255.0

Default Gateway:
198.51.100.1
```

---

# Network Summary

```text
Inside LAN:
192.168.10.0/24

R1 ↔ R2 Transit:
10.0.0.0/24

Outside Server Network:
198.51.100.0/24
```

Static NAT public address:

```text
203.0.113.10
```

Dynamic NAT Pool:

```text
203.0.113.20 - 203.0.113.21
```

---

# NAT Concepts

## NAT

NAT = Network Address Translation

NAT translates IP addresses between inside and outside networks.

Example:

```text
192.168.10.10
      ↓ NAT
203.0.113.10
```

NAT is not the same as routing.

Routing determines where a packet should go.

NAT changes or translates the address used by the packet.

---

# Types of NAT

## 1. Static NAT

Static NAT provides a fixed 1:1 mapping between an inside local address and an inside global address.

Example:

```text
192.168.10.10 ↔ 203.0.113.10
```

The mapping remains fixed.

---

## 2. Dynamic NAT

Dynamic NAT uses a pool of public addresses.

Example:

```text
Public Pool:

203.0.113.20
203.0.113.21
```

A device from the inside network receives an available public address from the pool.

If the pool is exhausted, another internal host may not receive a translation.

---

## 3. PAT

PAT = Port Address Translation

Also called:

```text
NAT Overload
```

PAT allows multiple internal devices to share one outside address by using session/port information to distinguish different connections.

Example concept:

```text
PC1 ─┐
     ├──→ Same Outside Address
PC2 ─┘
```

The router keeps track of the individual translations so responses can be sent back to the correct internal host.

---

# NAT Terminology

## Inside Local

The private address of an internal device.

Example:

```text
192.168.10.10
```

## Inside Global

The public address representing an internal device on the outside network.

Example:

```text
203.0.113.10
```

## Outside Global

The actual address of the external host.

Example:

```text
198.51.100.10
```

## Outside Local

The outside host address as it appears from the perspective of the inside network.

---

# NAT Inside / Outside Interfaces

On R1:

```text
G0/0 = NAT Inside
G0/1 = NAT Outside
```

Commands:

```cisco
interface g0/0
ip nat inside

interface g0/1
ip nat outside
```

---

# Basic Routing Configuration

Before NAT, routing was configured to prove that the underlying network works independently.

## R1

```cisco
ip route 198.51.100.0 255.255.255.0 10.0.0.2
```

## R2

```cisco
ip route 192.168.10.0 255.255.255.0 10.0.0.1
```

The baseline connectivity test from the inside PCs to the outside server succeeded.

---

# Baseline Connectivity

Before configuring NAT:

```text
PC1 → Server
4/4 replies
0% loss

PC2 → Server
4/4 replies
0% loss
```

This proved that the underlying routing and addressing were working before NAT was introduced.

---

# Static NAT Configuration

On R1:

```cisco
interface g0/0
ip nat inside
exit

interface g0/1
ip nat outside
exit

ip nat inside source static 192.168.10.10 203.0.113.10
```

Verification:

```cisco
show ip nat translations
```

Expected mapping:

```text
Inside Local     Inside Global
192.168.10.10    203.0.113.10
```

---

# Static NAT External Connectivity

To test the static mapping, the outside Server attempted to reach:

```text
203.0.113.10
```

The first attempt failed because R2 did not have a route to the public NAT address.

A host route was then added on R2:

```cisco
ip route 203.0.113.10 255.255.255.255 10.0.0.1
```

After adding the route:

```text
Server → 203.0.113.10
4/4 replies
0% loss
```

This demonstrated that NAT and routing must work together.

---

# Static NAT Translation Table

The translation table showed:

```text
Inside Global     Inside Local
203.0.113.10      192.168.10.10
```

This represents the fixed 1:1 mapping created by Static NAT.

---

# Dynamic NAT Configuration

PC2 was selected for Dynamic NAT.

An ACL was created to identify the internal host:

```cisco
access-list 2 permit host 192.168.10.20
```

A public address pool was created:

```cisco
ip nat pool DYNAMIC-POOL 203.0.113.20 203.0.113.21 netmask 255.255.255.0
```

The ACL was linked to the pool:

```cisco
ip nat inside source list 2 pool DYNAMIC-POOL
```

R2 was also configured with a route to the public NAT pool:

```cisco
ip route 203.0.113.0 255.255.255.0 10.0.0.1
```

---

# Dynamic NAT Verification

PC2 tested connectivity to the outside Server.

Result:

```text
4/4 replies
0% loss
```

The NAT translation table showed:

```text
Inside Global     Inside Local
203.0.113.20      192.168.10.20
```

This demonstrated that PC2 received a public address dynamically from the configured pool.

---

# Static NAT + Dynamic NAT Comparison

At this stage:

```text
PC1
192.168.10.10
      ↓
Static NAT
      ↓
203.0.113.10
```

```text
PC2
192.168.10.20
      ↓
Dynamic NAT
      ↓
203.0.113.20
```

This allowed both NAT types to be observed on the same router.

---

# PAT / NAT Overload

For the PAT section, the Dynamic NAT configuration was removed:

```cisco
no ip nat inside source list 2 pool DYNAMIC-POOL
```

The Static NAT mapping was also removed temporarily so both PCs could use PAT:

```cisco
no ip nat inside source static 192.168.10.10 203.0.113.10
```

Existing translations were cleared:

```cisco
clear ip nat translation *
```

An ACL was created for the inside network:

```cisco
access-list 10 permit 192.168.10.0 0.0.0.255
```

PAT was configured using R1's outside interface address:

```cisco
ip nat inside source list 10 interface g0/1 overload
```

Because G0/1 on R1 uses:

```text
10.0.0.1
```

PAT used that interface address as the Inside Global address.

---

# PAT Verification

PC1 and PC2 both accessed the outside Server.

The NAT translation table showed multiple internal hosts sharing the same Inside Global address.

Example:

```text
Inside Global    Inside Local
10.0.0.1         192.168.10.10
10.0.0.1         192.168.10.20
```

The table also displayed different ICMP identifiers for separate ICMP sessions.

This demonstrated the core PAT concept:

```text
Multiple Inside Local Hosts
            ↓
      One Inside Global
            ↓
     Session Identification
```

Important:

ICMP does not use TCP/UDP ports.

The Packet Tracer NAT table displayed ICMP identifiers for the ping traffic.

---

# PAT NAT Statistics

Verification command:

```cisco
show ip nat statistics
```

Observed:

```text
Outside Interface:
GigabitEthernet0/1

Inside Interfaces:
GigabitEthernet0/0
```

The observed statistics included:

```text
Hits: 16
Misses: 16
Expired translations: 16
```

At the time of the statistics check:

```text
Total translations: 0
```

This did not mean PAT was broken.

The PAT translations were created during the ICMP traffic and then expired after the sessions ended.

---

# Troubleshooting Observations

## Issue 1 — Static NAT External Access Failed

The first attempt to access:

```text
203.0.113.10
```

from the outside network failed.

The NAT mapping itself was already configured correctly.

The problem was that R2 did not have a route to the public NAT address.

Fix:

```cisco
ip route 203.0.113.10 255.255.255.255 10.0.0.1
```

After the route was added, external access succeeded.

---

## Issue 2 — R2 Static Route Syntax Error

A static route command was initially entered incorrectly.

The command was corrected and successfully installed.

Verification:

```cisco
show ip route 203.0.113.10
```

and:

```cisco
show ip route 192.168.10.0
```

This reinforced the importance of checking command syntax and verifying the routing table after configuration.

---

# Verification Commands

## Show NAT Translations

```cisco
show ip nat translations
```

## Show NAT Statistics

```cisco
show ip nat statistics
```

## Show Routing Table

```cisco
show ip route
```

## Show Running Configuration

```cisco
show running-config
```

## Clear NAT Translations

```cisco
clear ip nat translation *
```

---

# Key Concepts Learned

- NAT translates IP addressing between networks.
- Static NAT provides a fixed 1:1 mapping.
- Dynamic NAT uses a public IP pool.
- PAT allows many internal devices to share one outside address.
- PAT relies on session information to distinguish different connections.
- Inside Local represents the private internal address.
- Inside Global represents the translated address used externally.
- NAT inside and outside interfaces must be defined correctly.
- NAT does not replace routing.
- NAT requires proper routing to and from translated addresses.
- NAT is not a firewall.
- ICMP does not use TCP/UDP ports.
- NAT translation entries can expire after traffic sessions end.

---

# Lab Workflow

```text
Build Topology
      ↓
Configure IP Addresses
      ↓
Enable Router Interfaces
      ↓
Configure Routing
      ↓
Verify Baseline Connectivity
      ↓
Configure Static NAT
      ↓
Verify Static Translation
      ↓
Test Outside Access
      ↓
Configure Dynamic NAT
      ↓
Verify Dynamic Translation
      ↓
Test Connectivity
      ↓
Configure PAT
      ↓
Test Multiple Internal Hosts
      ↓
Verify PAT Translation Table
      ↓
Check NAT Statistics
      ↓
Document Results
```

---

# Screenshots

The lab evidence includes:

```text
Topology-and-Notes.png
R1-Static-Route-Verification.png
R2-Static-Route-Verification.png
Baseline-Connectivity-Before-NAT.png
PC2-Baseline-Connectivity.png
Static-NAT-Mapping-Verification.png
Static-NAT-Routing-Diagnosis.png
Static-NAT-External-Access.png
Static-NAT-Translation-Table.png
Dynamic-NAT-Configuration.png
Dynamic-NAT-Connectivity-Test.png
PAT-Translation-Table.png
PAT-NAT-Statistics.png
```

---

# Files

```text
14-NAT-PAT/
└── LAB-14-NAT-PAT/
    ├── Screenshots/
    ├── LAB-14-NAT-PAT.pkt
    └── README.md
```

---

# Final Result

LAB 14 successfully demonstrated three NAT mechanisms:

```text
Static NAT
192.168.10.10 → 203.0.113.10
```

```text
Dynamic NAT
192.168.10.20 → 203.0.113.20
```

```text
PAT
192.168.10.10
192.168.10.20
      ↓
   10.0.0.1
```

The lab successfully verified:

- Static NAT
- Dynamic NAT
- PAT / NAT Overload
- NAT translation tables
- NAT statistics
- Routing requirements
- Outside-to-inside connectivity through Static NAT
- Multiple internal hosts sharing one translated address through PAT

No additional intentional failure scenario was performed in this lab.

---

# Lab Status

```text
LAB 14 — NAT / PAT ✅

Status:
Completed

Focus:
Network Address Translation
Static NAT
Dynamic NAT
PAT / NAT Overload
Routing + NAT Integration
Verification
Troubleshooting
Documentation
```

---

# Next Lab

```text
LAB 15 — ACLs
```

Next focus:

- Standard ACLs
- Extended ACLs
- Traffic filtering
- Source / Destination / Protocol / Port matching
- ACL placement
- Verification
- Troubleshooting
```
