# LAB 15 — Access Control Lists (ACLs)

## 📌 Overview

This lab focuses on configuring and troubleshooting Cisco Access Control Lists (ACLs) to control network traffic based on:

- Source IP
- Destination IP
- Protocol
- Port
- Direction

The lab demonstrates the difference between Standard ACLs and Extended ACLs using an enterprise-style network.

Learning approach:

**Learn → Build → Test → Troubleshoot → Verify → Document**

---

# 🎯 Objectives

By completing this lab, I practiced:

- Understanding Access Control Lists
- Understanding Permit and Deny logic
- Configuring Standard ACLs
- Configuring Extended ACLs
- Understanding ACL rule order
- Understanding First Match Wins
- Understanding Implicit Deny
- Understanding wildcard masks
- Understanding ACL direction (`in` / `out`)
- Applying ACLs to router interfaces
- Filtering traffic based on source IP
- Filtering traffic based on protocol and destination port
- Verifying ACL configuration
- Testing permitted and denied traffic
- Understanding ACL troubleshooting

---

# 🏢 Company Scenario

A small enterprise network was built with three logical network segments:

### User LAN

Used by regular users.

### Admin / HR LAN

Used by administrative and HR systems.

### Server LAN

Contains an internal server.

Two routers provide Layer 3 connectivity between the three networks.

ACLs are then used to control access between these network segments and to specific services.

---

# 🧱 Topology

```text
                         USER LAN
                  192.168.10.0/24

             PC1               PC2
              |                 |
              +------ SW1 ------+
                        |
                      R1
                        |
                  10.0.0.0/30
                        |
                      R2
                    /     \
                   /       \
                 SW2       SW3
               /    \        |
             PC3    PC4    SERVER
                         SERVER LAN
                        192.168.30.0/24
```

---

# 🔌 Device Inventory

## Routers

- R1 — Cisco 2911
- R2 — Cisco 2911

## Switches

- SW1 — Cisco 2960
- SW2 — Cisco 2960
- SW3 — Cisco 2960

## End Devices

- PC1
- PC2
- PC3
- PC4
- SERVER

---

# 🔌 Interface Connections

| Connection | Interface |
|---|---|
| PC1 → SW1 | PC1 Fa0 → SW1 Fa0/1 |
| PC2 → SW1 | PC2 Fa0 → SW1 Fa0/2 |
| SW1 → R1 | SW1 Fa0/24 → R1 G0/0 |
| R1 → R2 | R1 G0/1 → R2 G0/0 |
| R2 → SW2 | R2 G0/1 → SW2 Fa0/24 |
| PC3 → SW2 | PC3 Fa0 → SW2 Fa0/1 |
| PC4 → SW2 | PC4 Fa0 → SW2 Fa0/2 |
| R2 → SW3 | R2 G0/2 → SW3 Fa0/24 |
| SERVER → SW3 | SERVER Fa0 → SW3 Fa0/1 |

---

# 🌐 IP Addressing Plan

## USER LAN

**Network:** `192.168.10.0/24`

| Device | Interface | IP Address | Default Gateway |
|---|---|---|---|
| PC1 | Fa0 | 192.168.10.10/24 | 192.168.10.1 |
| PC2 | Fa0 | 192.168.10.20/24 | 192.168.10.1 |
| R1 | G0/0 | 192.168.10.1/24 | — |

## TRANSIT NETWORK

**Network:** `10.0.0.0/30`

| Device | Interface | IP Address |
|---|---|---|
| R1 | G0/1 | 10.0.0.1/30 |
| R2 | G0/0 | 10.0.0.2/30 |

## ADMIN / HR LAN

**Network:** `192.168.20.0/24`

| Device | Interface | IP Address | Default Gateway |
|---|---|---|---|
| PC3 | Fa0 | 192.168.20.10/24 | 192.168.20.1 |
| PC4 | Fa0 | 192.168.20.20/24 | 192.168.20.1 |
| R2 | G0/1 | 192.168.20.1/24 | — |

## SERVER LAN

**Network:** `192.168.30.0/24`

| Device | Interface | IP Address | Default Gateway |
|---|---|---|---|
| SERVER | Fa0 | 192.168.30.10/24 | 192.168.30.1 |
| R2 | G0/2 | 192.168.30.1/24 | — |

---

# 🧠 ACL Theory

## What is an ACL?

ACL stands for:

**Access Control List**

An ACL is a collection of rules used to control network traffic.

Basic logic:

```text
Packet
   ↓
ACL
   ↓
Permit / Deny
   ↓
Forward / Drop
```

---

# 🔹 Standard ACL

A Standard ACL primarily evaluates the:

**Source IP Address**

Main question:

> Who is sending the traffic?

Example:

`access-list 10 deny host 192.168.10.10`

---

# 🔹 Extended ACL

An Extended ACL can evaluate:

- Source IP
- Destination IP
- Protocol
- TCP
- UDP
- Ports

This provides more specific traffic filtering.

Example:

`access-list 100 deny tcp host 192.168.10.10 host 192.168.30.10 eq 80`

This rule matches:

- Source: `192.168.10.10`
- Destination: `192.168.30.10`
- Protocol: TCP
- Destination Port: 80 / HTTP

---

# 🧠 ACL Rule Order

ACLs are processed from:

**Top → Bottom**

The first matching rule wins.

Example:

`10 deny host 192.168.10.10`

`20 permit 192.168.10.0 0.0.0.255`

Traffic from `192.168.10.10` is denied because it matches the first rule.

---

# 🚫 Implicit Deny

ACL processing includes an implicit deny at the end.

If traffic does not match an explicit rule, it is denied.

For this reason, the lab used explicit permit rules such as:

`permit any`

and:

`permit ip any any`

after specific deny rules when other traffic needed to remain permitted.

---

# 🎯 Wildcard Masks

| Network | Wildcard Mask |
|---|---|
| /24 | `0.0.0.255` |
| /16 | `0.0.255.255` |
| Host | `0.0.0.0` |
| Any | `0.0.0.0 255.255.255.255` |

---

# 🔄 ACL Direction

ACLs can be applied:

`in`

or:

`out`

### IN

The packet is evaluated when it enters the router interface.

### OUT

The packet is evaluated when it exits the router interface.

The direction is always considered relative to the router interface.

---

# 🌐 Routing Configuration

Static routing was used to establish baseline Layer 3 connectivity before ACL filtering.

## R1

```cisco
ip route 192.168.20.0 255.255.255.0 10.0.0.2
ip route 192.168.30.0 255.255.255.0 10.0.0.2
```

## R2

```cisco
ip route 192.168.10.0 255.255.255.0 10.0.0.1
```

Verification:

```cisco
show ip route
```

---

# ✅ Baseline Connectivity

Before applying ACLs, connectivity between the network segments was verified.

The baseline confirmed that routing was operational before traffic filtering was introduced.

---

# 🛡️ Standard ACL — ACL 10

## Scenario

PC1 should not be allowed to access the Admin / HR LAN.

PC1:

`192.168.10.10`

Admin / HR LAN:

`192.168.20.0/24`

## Configuration

```cisco
access-list 10 deny host 192.168.10.10
access-list 10 permit any
```

Verification:

```cisco
show access-lists
```

Expected ACL structure:

```text
Standard IP access list 10
    10 deny host 192.168.10.10
    20 permit any
```

---

# 🔗 Applying ACL 10

ACL 10 was applied to:

**R2 G0/1 OUT**

Configuration:

```cisco
interface g0/1
ip access-group 10 out
```

Verification:

```cisco
show ip interface g0/1
```

The interface showed:

`Outgoing access list is 10`

---

# 🧪 Standard ACL Test

## PC1

Tests:

```text
ping 192.168.20.10
ping 192.168.20.20
```

Result:

**Traffic denied**

PC1 was unable to reach the Admin / HR hosts.

## PC2

Tests:

```text
ping 192.168.20.10
ping 192.168.20.20
```

Result:

**4/4 — 0% loss**

PC2 was permitted because it did not match the specific deny rule.

This demonstrated source-based filtering using a Standard ACL.

---

# 🛡️ Additional Standard ACL — ACL 11

A second Standard ACL was added as an additional practical scenario.

## Scenario

PC2 should not be allowed to access the Server LAN.

PC2:

`192.168.10.20`

Server:

`192.168.30.10`

## Configuration

```cisco
access-list 11 deny host 192.168.10.20
access-list 11 permit any
```

Applied to:

**R2 G0/2 OUT**

```cisco
interface g0/2
ip access-group 11 out
```

Verification:

```cisco
show access-lists
```

Expected ACL structure:

```text
Standard IP access list 11
    10 deny host 192.168.10.20
    20 permit any
```

---

# 🧪 ACL 11 Test

From PC2:

```text
ping 192.168.30.10
```

Result:

**100% loss**

This demonstrated another source-based filtering scenario using a separate Standard ACL.

---

# 🔐 Extended ACL — ACL 100

## Scenario

PC1 should be prevented from accessing the Server using HTTP, while ICMP traffic should remain permitted.

Source:

`192.168.10.10`

Destination:

`192.168.30.10`

Protocol:

`TCP`

Port:

`80 / HTTP`

---

# 🧱 Extended ACL Configuration

On R2:

```cisco
access-list 100 deny tcp host 192.168.10.10 host 192.168.30.10 eq 80
access-list 100 permit ip any any
```

Verification:

```cisco
show access-lists
```

Expected structure:

```text
Extended IP access list 100
    10 deny tcp host 192.168.10.10 host 192.168.30.10 eq www
    20 permit ip any any
```

Packet Tracer displays HTTP port 80 as `www`.

---

# 🔗 Applying Extended ACL 100

ACL 100 was applied to:

**R2 G0/0 IN**

Configuration:

```cisco
interface g0/0
ip access-group 100 in
```

Verification:

```cisco
show ip interface g0/0
```

The interface showed:

`Inbound access list is 100`

---

# 🌐 HTTP Filtering Test

From PC1:

`http://192.168.30.10`

Result:

**Request Timeout**

HTTP access was successfully denied.

The rule specifically matched:

- Source IP
- Destination IP
- TCP
- Destination port 80

---

# 🧪 ICMP Verification

After blocking HTTP, ICMP traffic was tested.

From PC1:

```text
ping 192.168.30.10
```

Result:

**4 Sent — 4 Received — 0% Loss**

This confirmed that HTTP/TCP port 80 was blocked while other IP traffic was still permitted through:

`permit ip any any`

---

# 🔍 Verification Commands

The following Cisco IOS commands were used during the lab:

```cisco
show ip route
show access-lists
show ip interface g0/0
show ip interface g0/1
show running-config
```

---

# 🔧 Troubleshooting Approach

When ACL-related connectivity problems occur, check:

1. Does the ACL exist?
2. Is the ACL applied?
3. Which interface is it applied to?
4. Is it applied `in` or `out`?
5. Is the rule order correct?
6. Is the wildcard mask correct?
7. Does the traffic match the expected rule?
8. Is implicit deny causing the problem?
9. Is routing correct?
10. Is the problem caused by ACL filtering or routing?

The lab reinforced an evidence-based troubleshooting process instead of guessing.

---

# 🧠 Key Lessons

### Standard ACL

Primarily focuses on:

**Source IP**

### Extended ACL

Provides more specific control using:

**Source + Destination + Protocol + Port**

### First Match Wins

ACL rules are evaluated from top to bottom.

### Implicit Deny

Traffic that does not match an allowed rule is denied.

### Direction Matters

`in` and `out` are always relative to the router interface.

### Routing vs ACL

Routing determines where traffic can go.

ACLs determine whether traffic is permitted or denied.

---

# 📊 Final ACL Design

```text
ACL 10
R2 G0/1 OUT
Deny PC1 → Admin/HR
Permit other traffic

ACL 11
R2 G0/2 OUT
Deny PC2 → Server
Permit other traffic

ACL 100
R2 G0/0 IN
Deny PC1 → Server HTTP/TCP 80
Permit other IP traffic
```

---

# ✅ Lab Results

The lab successfully demonstrated:

- Working Layer 3 connectivity before filtering
- Source-based traffic filtering using Standard ACLs
- Protocol and port filtering using Extended ACLs
- Denied traffic testing
- Permitted traffic testing
- ACL placement and direction
- ACL verification
- Practical ACL troubleshooting concepts

---

# 📸 Screenshots

The `Screenshots` directory contains evidence for:

- Topology
- R1 static route verification
- R2 static route verification
- Baseline connectivity
- Standard ACL configuration
- Standard ACL application
- PC1 denied traffic
- PC2 permitted traffic
- Additional Standard ACL configuration
- Additional Standard ACL denied traffic
- Extended ACL configuration
- Extended ACL application
- HTTP denied traffic
- ICMP permitted traffic

Recommended filenames:

```text
01-Topology.png
02-R1-Static-Route-Verification.png
03-R2-Static-Route-Verification.png
04-Baseline-Connectivity-Verification.png
05-Standard-ACL-Configuration.png
06-Standard-ACL-Application.png
07-Standard-ACL-PC1-Denied.png
08-Standard-ACL-PC2-Permitted.png
10-Second-Standard-ACL-Configuration.png
11-Second-Standard-ACL-PC2-Denied.png
12-Extended-ACL-Configuration.png
13-Extended-ACL-Application.png
14-Extended-ACL-HTTP-Denied.png
15-Extended-ACL-Ping-Permitted.png
```

Note: A dedicated final match-counter screenshot was not included because the final `show access-lists` counter verification was not completed as a separate evidence step.

---

# 📁 Repository Structure

```text
15-ACLs/
└── LAB-15-ACLs/
    ├── Screenshots/
    ├── LAB-15-ACLs.pkt
    └── README.md
```

---

# 🎯 Lab Workflow

```text
Build
  ↓
Configure IPs
  ↓
Configure Static Routing
  ↓
Verify Baseline Connectivity
  ↓
Configure Standard ACL
  ↓
Test Source Filtering
  ↓
Configure Additional Standard ACL
  ↓
Test Source Filtering
  ↓
Configure Extended ACL
  ↓
Filter HTTP Traffic
  ↓
Verify ICMP Connectivity
  ↓
Document Results
```

---

# 🚀 Conclusion

LAB 15 extended the networking foundation from routing and NAT/PAT into practical traffic control and network access filtering.

The lab demonstrated that network connectivity alone is not enough in an enterprise environment. Traffic must also be controlled according to operational and security requirements.

This lab reinforced the troubleshooting mindset:

**Learn → Build → Test → Troubleshoot → Verify → Document**

---

# 🔥 Day 15 Completed

**LAB 15 — Access Control Lists ✅**

Next:

**LAB 16 — Switch Port Security**
```