# LAB 00 — Basic Network

## 🎯 Objective

Build a basic network in Cisco Packet Tracer and understand the fundamentals of device-to-device communication.

## 🧩 Topology

PC0 -------- Switch -------- PC1

## 🌐 Network Configuration

| Device | IP Address | Subnet Mask |
|---|---|---|
| PC0 | 192.168.1.10 | 255.255.255.0 |
| PC1 | 192.168.1.20 | 255.255.255.0 |

No default gateway was required because both devices were on the same local network.

## 📚 Concepts Covered

- Network
- NIC
- MAC Address
- IP Address
- Subnet Mask
- Ethernet
- Layer 2 Switching
- ICMP / Ping
- Basic ARP concept
- Same-subnet communication

## 🧪 Scenario

Two PCs were connected to a Layer 2 switch.

The objective was to establish communication between the hosts using IPv4 addressing within the same subnet.

## ✅ Connectivity Test

From PC0:

ping 192.168.1.20

The ping was successful, confirming connectivity between the two hosts.

## 🔍 Troubleshooting Scenario

PC1 was intentionally changed to:

192.168.2.20/24

while PC0 remained:

192.168.1.10/24

Communication failed because the two hosts were no longer in the same IP network and no Layer 3 device was available to route traffic between them.

## 🧠 Key Takeaways

- IP addresses identify devices logically at Layer 3.
- MAC addresses are used for Layer 2 communication.
- A switch forwards Ethernet frames using MAC addresses.
- Hosts in the same subnet can communicate directly through the local network.
- Communication between different IP networks requires a Layer 3 device such as a router.

## 🛠️ Tool

Cisco Packet Tracer

## 📌 Status

Completed ✅
