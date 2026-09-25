# LAB 05 — DHCP

## 🎯 Objective

Understand the fundamentals of Dynamic Host Configuration Protocol (DHCP) and learn how DHCP automatically provides network configuration to client devices.

The lab focuses on:

- DHCP Server vs DHCP Client
- Dynamic IP Address Assignment
- DHCP Address Pool
- DHCP Lease
- DHCP Discover
- DHCP Offer
- DHCP Request
- DHCP Acknowledgement (ACK)
- Default Gateway Distribution
- DHCP and Broadcast Traffic
- DHCP Troubleshooting
- Intentional DHCP Service Failure
- Evidence-Based Troubleshooting
- Root Cause Identification
- Fix and Recovery
- Final Verification

The lab was implemented and tested using Cisco Packet Tracer.

---

## 🧩 Topology

```text
PC0 ───┐
       │
PC1 ───┼── SW1 ─── R1
       │
DHCP-SRV
```

### Devices

```text
PC0
PC1

SW1 — Cisco 2960

R1 — Cisco 2911

DHCP-SRV — Packet Tracer Server
```

### Connections

```text
PC0 FastEthernet0 → SW1 FastEthernet0/1

PC1 FastEthernet0 → SW1 FastEthernet0/2

DHCP-SRV FastEthernet0 → SW1 FastEthernet0/24

R1 GigabitEthernet0/0 → SW1 FastEthernet0/3
```

---

## 🌐 Network Configuration

The lab uses:

```text
Network:
192.168.10.0/24

Subnet Mask:
255.255.255.0

Usable Hosts:
192.168.10.1 → 192.168.10.254

Broadcast:
192.168.10.255
```

### Device Addressing

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| DHCP-SRV | FastEthernet0 | 192.168.10.2 | 255.255.255.0 | — |
| R1 | GigabitEthernet0/0 | 192.168.10.1 | 255.255.255.0 | — |
| PC0 | FastEthernet0 | DHCP | 255.255.255.0 | DHCP |
| PC1 | FastEthernet0 | DHCP | 255.255.255.0 | DHCP |

---

# 📚 Core Concepts

## 1. What is DHCP?

DHCP stands for:

```text
Dynamic Host Configuration Protocol
```

DHCP automatically provides network configuration to client devices.

Instead of configuring each client manually, DHCP can provide:

```text
IP Address
Subnet Mask
Default Gateway
DNS Server
Lease Information
```

This becomes particularly useful when a network contains many devices.

---

## 2. DHCP Server vs DHCP Client

### DHCP Server

The DHCP Server provides network configuration to clients.

```text
DHCP Server
↓
Provides IP Configuration
```

### DHCP Client

The DHCP Client is the device requesting network configuration.

```text
DHCP Client
↓
Requests IP Configuration
```

In this lab:

```text
DHCP-SRV
→ DHCP Server

PC0
→ DHCP Client

PC1
→ DHCP Client
```

---

## 3. DHCP Address Pool

A DHCP Server needs a range of addresses that it can allocate to clients.

The pool used in this lab was:

```text
Pool Name:
serverPool

Start IP Address:
192.168.10.100

Subnet Mask:
255.255.255.0

Maximum Number of Users:
100
```

The pool therefore provides client addresses beginning at:

```text
192.168.10.100
```

with the configured capacity of 100 clients.

The DHCP Server itself uses:

```text
192.168.10.2
```

so its address is outside the DHCP client allocation range.

---

## 4. Default Gateway Through DHCP

After introducing R1 into the topology, the DHCP pool was configured to distribute:

```text
Default Gateway:
192.168.10.1
```

R1 GigabitEthernet0/0 was configured as:

```text
192.168.10.1/24
```

This allowed DHCP clients to automatically receive the gateway configuration.

The final client configuration therefore included:

```text
IP Address:
DHCP Assigned

Subnet Mask:
255.255.255.0

Default Gateway:
192.168.10.1
```

The DNS Server option remained:

```text
0.0.0.0
```

because DNS was not configured as part of this lab.

---

# 🔄 DHCP DORA Process

The DHCP client normally uses four main messages:

```text
Discover
↓
Offer
↓
Request
↓
ACK
```

This process is commonly referred to as:

```text
DORA
```

---

## 5. DHCP Discover

The client initially does not have a normal IPv4 configuration.

The DHCP Discover uses:

```text
Source IP:
0.0.0.0

Destination IP:
255.255.255.255
```

The Ethernet destination is the broadcast address:

```text
FFFF.FFFF.FFFF
```

The client is effectively asking:

```text
Is there a DHCP Server available?
```

The Discover is broadcast through the local Layer 2 network.

---

## 6. DHCP Offer

The DHCP Server responds with an Offer.

In this lab:

```text
DHCP-SRV:
192.168.10.2
```

The Offer was observed in Packet Tracer Simulation Mode.

The Offer uses:

```text
UDP:
67 → 68
```

The server provides an available configuration from its DHCP pool.

---

## 7. DHCP Request

After receiving an Offer, the client requests the offered configuration.

The Request uses the DHCP client/server UDP ports:

```text
UDP:
68 → 67
```

The DHCP Request indicates that the client wants to use the offered configuration.

---

## 8. DHCP Acknowledgement

The server then sends a DHCP Acknowledgement.

The ACK was observed in Packet Tracer Simulation Mode.

The server used:

```text
UDP:
67 → 68
```

The client then applies the received IP configuration.

The complete process is:

```text
DHCP Discover
↓
DHCP Offer
↓
DHCP Request
↓
DHCP ACK
↓
Client Configured
```

---

# 🧪 Experiment 1 — Initial DHCP Configuration

The DHCP Server was configured with a static address:

```text
IP Address:
192.168.10.2

Subnet Mask:
255.255.255.0
```

The DHCP service was then enabled.

The DHCP pool was configured as:

```text
Start IP:
192.168.10.100

Subnet Mask:
255.255.255.0

Maximum Users:
100
```

At the beginning of the lab, no Default Gateway or DNS Server was configured in the pool.

---

# 🧪 Experiment 2 — DHCP Client Lease

PC0 was switched from manual addressing to DHCP.

The DHCP request succeeded and PC0 received:

```text
IP Address:
192.168.10.100

Subnet Mask:
255.255.255.0

Default Gateway:
0.0.0.0

DNS Server:
0.0.0.0
```

PC1 was also switched to DHCP and received:

```text
IP Address:
192.168.10.101

Subnet Mask:
255.255.255.0

Default Gateway:
0.0.0.0

DNS Server:
0.0.0.0
```

This demonstrated that the DHCP Server could automatically assign client IP addresses.

---

# 🧪 Experiment 3 — DHCP Client Connectivity

Communication between the DHCP clients was tested.

The clients successfully communicated on the same `/24` network.

This demonstrated that DHCP-assigned addresses could be used for normal Layer 2 network communication.

The DHCP process therefore provided the clients with usable IPv4 addressing without manual configuration.

---

# 🧪 Experiment 4 — Adding a Default Gateway

R1 was added to provide a real Layer 3 gateway.

R1 GigabitEthernet0/0 was configured as:

```text
IP Address:
192.168.10.1

Subnet Mask:
255.255.255.0
```

The interface was enabled using:

```text
no shutdown
```

The interface state was verified as operational.

The DHCP pool was then updated to distribute:

```text
Default Gateway:
192.168.10.1
```

---

# 🧪 Experiment 5 — DHCP Distributed Gateway

After updating the DHCP pool, the clients renewed their DHCP leases.

PC0 received:

```text
IP Address:
192.168.10.100

Subnet Mask:
255.255.255.0

Default Gateway:
192.168.10.1
```

PC1 received:

```text
IP Address:
192.168.10.101

Subnet Mask:
255.255.255.0

Default Gateway:
192.168.10.1
```

This demonstrated that DHCP can distribute the Default Gateway in addition to the client IP address and subnet mask.

---

# 🧪 Experiment 6 — DHCP Simulation and DORA

Packet Tracer Simulation Mode was used to observe DHCP traffic.

The process included:

```text
PC0
↓
DHCP Discover
↓
SW1
↓
DHCP-SRV
↓
DHCP Offer
↓
SW1
↓
PC0
↓
DHCP Request
↓
DHCP-SRV
↓
DHCP ACK
↓
PC0
```

The Discover was observed as a broadcast message using:

```text
Source IP:
0.0.0.0

Destination IP:
255.255.255.255
```

The DHCP Server responded using:

```text
UDP:
67 → 68
```

The client request used:

```text
UDP:
68 → 67
```

The DHCP Acknowledgement used:

```text
UDP:
67 → 68
```

This demonstrated the DORA process at packet level.

---

# 💥 Experiment 7 — Intentional Failure: DHCP Service Failure

A deliberate failure was introduced by disabling the DHCP service on the server.

The DHCP service was changed to:

```text
OFF
```

The DHCP pool configuration itself remained present.

PC0 then released its lease and attempted to obtain a new one.

The following commands were used:

```text
ipconfig /release

ipconfig /renew
```

The renewal failed because the DHCP service was not operating.

---

## Failure Result

After the failed renewal, PC0 received an Automatic Private IP Address:

```text
169.254.x.x
```

The observed configuration included:

```text
IP Address:
169.254.74.37

Subnet Mask:
255.255.0.0

Default Gateway:
0.0.0.0
```

The DHCP renewal therefore did not obtain an address from the configured DHCP pool.

---

# 🔍 Failure Analysis

The DHCP Server was inspected.

The DHCP service state was:

```text
OFF
```

At the same time, the DHCP pool remained configured with:

```text
Start IP:
192.168.10.100

Subnet Mask:
255.255.255.0

Default Gateway:
192.168.10.1

Maximum Users:
100
```

This demonstrated that the failure was not caused by the pool configuration.

The DHCP service itself was disabled.

The evidence therefore pointed to:

```text
DHCP Service = OFF
```

as the root cause.

---

# 🔍 Root Cause

```text
DHCP Service was disabled on DHCP-SRV
```

The DHCP Server and its pool were still present, but the DHCP service was not running.

As a result:

```text
DHCP Client
↓
DHCP Renew
↓
No DHCP Service Response
↓
Renewal Failed
↓
169.254.x.x
```

---

# 🔧 Fix

The DHCP service was restored to:

```text
ON
```

No other DHCP pool configuration was changed.

PC0 then renewed its DHCP lease.

---

# ✅ Final Recovery

After restoring the DHCP service, PC0 successfully obtained a DHCP configuration again.

The final configuration included:

```text
IP Address:
192.168.10.102

Subnet Mask:
255.255.255.0

Default Gateway:
192.168.10.1

DNS Server:
0.0.0.0
```

Connectivity testing was then performed against another DHCP client.

The communication succeeded with:

```text
4 Sent
4 Received
0% Loss
```

This confirmed that DHCP service recovery restored normal client configuration and connectivity.

---

# 🧠 Key Takeaways

1. DHCP automatically provides network configuration to clients.
2. DHCP reduces the need for manual IP configuration.
3. DHCP is especially useful in networks with many devices.
4. A DHCP Server provides configuration to DHCP Clients.
5. DHCP can provide IP Address, Subnet Mask, Default Gateway, and DNS Server information.
6. DHCP addresses are allocated from a configured address pool.
7. DHCP commonly follows the DORA process.
8. DHCP Discover is used by the client to locate a DHCP Server.
9. DHCP Offer provides an available configuration to the client.
10. DHCP Request asks to use the offered configuration.
11. DHCP ACK confirms the lease and configuration.
12. DHCP Discover uses broadcast communication when the client does not yet have a normal IPv4 configuration.
13. DHCP uses UDP port 67 on the server side and UDP port 68 on the client side.
14. A DHCP client can receive its Default Gateway automatically.
15. DHCP clients can communicate using dynamically assigned addresses.
16. An active DHCP service is required for clients to obtain or renew DHCP leases.
17. When DHCP service fails, a client may end up with an Automatic Private IP Address such as `169.254.x.x`.
18. Troubleshooting should use evidence instead of guessing.
19. DHCP troubleshooting can include checking the client configuration, renewal results, and DHCP server service state.
20. Restoring the DHCP service and renewing the lease can recover client connectivity.

---

# 🛠️ Commands Used

## Packet Tracer PCs

```text
ipconfig

ipconfig /release

ipconfig /renew

ping 192.168.10.1

ping 192.168.10.2
```

## Cisco Router

```text
enable

configure terminal

hostname R1

interface gigabitEthernet 0/0

ip address 192.168.10.1 255.255.255.0

no shutdown

show ip interface brief
```

---

# 📁 Lab Files

```text
LAB-05-DHCP/
│
├── LAB-05-DHCP.pkt
├── README.md
└── screenshots/
```

---

## ✅ Status

**Completed**

**100-Day IT Infrastructure Journey — LAB 05**
