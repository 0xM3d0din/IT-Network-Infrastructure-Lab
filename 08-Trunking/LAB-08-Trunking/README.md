# LAB 08 — Trunking

## 🎯 Objective

Understand how trunk links allow multiple VLANs to travel between switches over a single physical connection.

This lab focuses on:

- Trunking Fundamentals
- Access Port vs Trunk Port
- IEEE 802.1Q
- VLAN Tagging
- Native VLAN
- Allowed VLANs
- Inter-Switch Trunk Links
- VLAN Extension Across Multiple Switches
- Trunk Verification
- VLAN Communication Across a Trunk
- Intentional Trunk Failure
- Evidence-Based Troubleshooting
- Root Cause Identification
- Recovery and Final Verification

The lab was implemented and tested using Cisco Packet Tracer.

---

## 🏢 Company Scenario

The company network is distributed across two switches.

The same departmental VLANs exist on both switches, allowing devices belonging to the same VLAN to communicate even when they are physically connected to different switches.

This lab uses two departments:

| Department | VLAN |
|---|---:|
| Administration | 10 |
| HR | 20 |

The objective is to use a single trunk link between the two switches to carry traffic for both VLANs.

---

## 🧩 Topology

```text
                         Trunk
              SW1 ================= SW2
             /   \                  /   \
            /     \                /     \
     Admin-PC01   HR-PC01   Admin-PC02   HR-PC02
```

### Devices

```text
SW1 — Cisco 2960
SW2 — Cisco 2960

Admin-PC01
HR-PC01

Admin-PC02
HR-PC02
```

### Port Mapping

```text
SW1
Fa0/1  → Admin-PC01
Fa0/2  → HR-PC01
Fa0/24 → Trunk to SW2

SW2
Fa0/1  → Admin-PC02
Fa0/2  → HR-PC02
Fa0/24 → Trunk to SW1
```

---

## 🌐 IP Addressing

All endpoint devices use the same IPv4 network for this lab:

```text
Network:
192.168.10.0/24

Subnet Mask:
255.255.255.0
```

### Endpoint Addressing

```text
Administration

Admin-PC01 → 192.168.10.10
Admin-PC02 → 192.168.10.11


HR

HR-PC01 → 192.168.10.20
HR-PC02 → 192.168.10.21
```

No Default Gateway was configured because the lab focuses on Layer 2 trunking and all endpoints are in the same IPv4 subnet.

---

# 📚 Core Concepts

## 1. What is Trunking?

A trunk link is a Layer 2 connection that can carry traffic belonging to multiple VLANs over a single physical link.

Example:

```text
SW1
  │
  │ Trunk
  │
SW2
```

Instead of requiring a separate physical connection for each VLAN, one trunk can carry traffic for multiple VLANs.

---

## 2. Access Port vs Trunk Port

### Access Port

An access port is normally used for endpoint devices and is assigned to a single VLAN.

Example:

```text
PC
 ↓
Access Port
 ↓
VLAN 10
```

### Trunk Port

A trunk port carries traffic for multiple VLANs.

Example:

```text
SW1
 ↓
Trunk
 ↓
SW2

VLAN 10
VLAN 20
```

Basic distinction:

```text
Access → One VLAN
Trunk  → Multiple VLANs
```

---

## 3. Trunking is Layer 2

Trunking does not perform routing.

Its purpose is to transport VLAN traffic between Layer 2 devices.

It does not provide communication such as:

```text
VLAN 10 → VLAN 20
```

Inter-VLAN communication requires a Layer 3 device and will be covered later in:

```text
LAB 09 — Inter-VLAN Routing
```

---

## 4. IEEE 802.1Q

The trunk in this lab uses IEEE 802.1Q.

802.1Q provides a mechanism for identifying VLAN membership on Ethernet traffic carried across a trunk.

Conceptually:

```text
Ethernet Frame
      +
VLAN Tag
      ↓
802.1Q Trunk
```

This allows the receiving switch to identify the VLAN associated with the traffic.

---

## 5. VLAN Tagging

When traffic from multiple VLANs travels over the same trunk, VLAN identification is required so the receiving switch can associate the traffic with the correct VLAN.

Conceptually:

```text
VLAN 10 Traffic
      ↓
VLAN Tag
      ↓
Trunk
      ↓
SW2
```

The receiving switch can then process the traffic within VLAN 10.

---

## 6. Native VLAN

802.1Q supports a Native VLAN for traffic carried without a VLAN tag.

In this lab, the observed Native VLAN was:

```text
VLAN 1
```

The Native VLAN was not changed during the lab.

---

## 7. Allowed VLANs

A trunk can be configured to allow specific VLANs to cross the link.

Example:

```text
Allowed VLANs:
10,20
```

This means:

```text
VLAN 10 → Allowed
VLAN 20 → Allowed
Other VLANs → Not Allowed
```

Allowed VLAN configuration is useful for both network design and troubleshooting.

---

# 🧪 Experiment 1 — Baseline Connectivity

Before applying the VLAN and trunk configuration, endpoint devices were able to communicate using the default switch configuration.

From Admin-PC01, connectivity was tested against:

```text
Admin-PC02
HR-PC01
HR-PC02
```

The tests succeeded with:

```text
4 Sent
4 Received
0% Loss
```

This established the baseline behavior before VLAN segmentation and trunking were applied.

---

# 🧪 Experiment 2 — VLAN 10 Creation on SW1

VLAN 10 was created on SW1:

```text
enable
configure terminal
vlan 10
name ADMINISTRATION
end
```

The VLAN was verified using:

```text
show vlan brief
```

The switch showed:

```text
10   ADMINISTRATION   active
```

---

# 🧪 Experiment 3 — VLAN 10 Creation on SW2

The same VLAN was created on SW2:

```text
enable
configure terminal
vlan 10
name ADMINISTRATION
end
```

The VLAN was verified using:

```text
show vlan brief
```

The switch showed:

```text
10   ADMINISTRATION   active
```

Creating the same VLAN on both switches allows VLAN 10 to exist on both sides of the trunk.

---

# 🧪 Experiment 4 — Access Port Configuration

The Administration endpoint ports were configured as access ports.

On SW1:

```text
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
```

On SW2:

```text
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
```

The HR endpoint ports were assigned to VLAN 20.

The resulting design was:

```text
SW1 Fa0/1 → VLAN 10
SW1 Fa0/2 → VLAN 20

SW2 Fa0/1 → VLAN 10
SW2 Fa0/2 → VLAN 20
```

---

# 🧪 Experiment 5 — Trunk Configuration

The inter-switch link uses:

```text
SW1 Fa0/24
        ║
        ║ Trunk
        ║
SW2 Fa0/24
```

The trunk was configured using:

```text
enable
configure terminal
interface fastEthernet 0/24
switchport mode trunk
end
```

The configuration was verified using:

```text
show interfaces trunk
```

The verification showed:

```text
Encapsulation:
802.1Q

Status:
trunking

Native VLAN:
1
```

The trunk was operational and capable of carrying the required VLAN traffic.

---

# 🧪 Experiment 6 — VLAN 10 Communication Across the Trunk

Administration communication was tested between devices connected to different switches:

```text
Admin-PC01
     ↓
SW1
     ↓
Trunk
     ↓
SW2
     ↓
Admin-PC02
```

The communication succeeded with:

```text
4 Sent
4 Received
0% Loss
```

This demonstrated that VLAN 10 was successfully extended across the trunk.

---

# 🧪 Experiment 7 — VLAN 20 Communication Across the Trunk

HR communication was also tested between devices connected to different switches:

```text
HR-PC01
    ↓
SW1
    ↓
Trunk
    ↓
SW2
    ↓
HR-PC02
```

The communication succeeded with:

```text
4 Sent
4 Received
0% Loss
```

This demonstrated that VLAN 20 was also successfully carried across the trunk.

---

# 🧠 Trunking Result

Both VLANs used the same physical inter-switch link:

```text
                Trunk
SW1 ================================= SW2
         VLAN 10 + VLAN 20
```

Therefore:

```text
VLAN 10
SW1 → Trunk → SW2
✅

VLAN 20
SW1 → Trunk → SW2
✅
```

At the same time, the VLAN separation remained intact:

```text
VLAN 10 ↔ VLAN 20
❌
```

---

# 💥 Experiment 8 — Intentional Failure: Allowed VLAN Restriction

A deliberate failure was introduced by restricting the allowed VLANs on the SW1 trunk.

The original configuration allowed both VLANs:

```text
VLAN 10
VLAN 20
```

The trunk was intentionally changed to allow only:

```text
VLAN 20
```

The configuration used was:

```text
enable
configure terminal
interface fastEthernet 0/24
switchport trunk allowed vlan 20
end
```

The trunk remained operational, but VLAN 10 was no longer allowed across the link.

---

# 🔍 Failure Verification

The trunk was checked using:

```text
show interfaces trunk
```

The verification showed:

```text
Fa0/24
Status:
trunking

Encapsulation:
802.1Q

Allowed VLANs:
20
```

Therefore:

```text
VLAN 10 → Not Allowed ❌
VLAN 20 → Allowed ✅
```

---

# 🧪 Experiment 9 — VLAN 10 Failure

After VLAN 10 was removed from the allowed VLAN list, the Administration connectivity test was repeated:

```text
Admin-PC01
     ↓
SW1
     ↓
Trunk
     ↓
SW2
     ↓
Admin-PC02
```

The communication failed:

```text
4 Sent
0 Received
100% Loss
```

This demonstrated that a VLAN can exist on both switches while still failing to communicate when that VLAN is not allowed across the trunk.

---

# 🔍 Failure Diagnosis

The trunk itself was still operational:

```text
Fa0/24
Status:
trunking
```

However, the allowed VLAN configuration showed:

```text
VLAN 20
```

and did not include:

```text
VLAN 10
```

The evidence therefore showed:

```text
Trunk Status
✅ Working

VLAN 10
✅ Exists

VLAN 10 Allowed on Trunk
❌ No

VLAN 10 Communication
❌ Failed
```

---

# 🔍 Root Cause

```text
VLAN 10 was not included in the allowed VLAN list on the trunk.
```

The failure path was:

```text
Admin-PC01
      ↓
SW1
      ↓
Trunk
      ↓
VLAN 10 Not Allowed
      ↓
SW2
      ↓
Admin-PC02 Unreachable
```

---

# 🔧 Fix

The trunk configuration on SW1 was restored to allow both VLANs:

```text
enable
configure terminal
interface fastEthernet 0/24
switchport trunk allowed vlan 10,20
end
```

The trunk was then verified again using:

```text
show interfaces trunk
```

The allowed VLAN list showed:

```text
10,20
```

---

# ✅ Final Recovery

After restoring VLAN 10 to the allowed VLAN list, connectivity was tested again:

```text
Admin-PC01
     ↓
SW1
     ↓
Trunk
     ↓
SW2
     ↓
Admin-PC02
```

The final test showed successful communication:

```text
4 Sent
4 Received
0% Loss
```

This confirmed that restoring VLAN 10 to the trunk's allowed VLAN list recovered communication.

---

# 🧠 Key Takeaways

- Trunking allows multiple VLANs to use a single physical inter-switch link.
- Access ports are normally assigned to a single VLAN.
- Trunk ports can carry traffic for multiple VLANs.
- Trunking operates at Layer 2.
- Trunking does not perform inter-VLAN routing.
- IEEE 802.1Q is used for VLAN identification on trunk links.
- VLAN tagging allows the receiving switch to identify the VLAN associated with trunk traffic.
- The Native VLAN is used for traffic carried without a VLAN tag.
- The Native VLAN observed in this lab was VLAN 1.
- Allowed VLANs control which VLANs are permitted to cross a trunk.
- The same VLAN must exist on both switches when that VLAN is expected to extend across the trunk.
- VLAN 10 successfully communicated across the trunk.
- VLAN 20 successfully communicated across the trunk.
- Restricting the trunk to VLAN 20 caused VLAN 10 communication to fail.
- The trunk can remain operational even when a specific VLAN is not allowed.
- `show interfaces trunk` is an important command for trunk troubleshooting.
- Troubleshooting should verify the trunk state, VLAN existence, and allowed VLAN configuration.
- Restoring the required VLAN to the allowed list recovered communication.

---

# 🛠️ Commands Used

## VLAN Creation

```text
enable

configure terminal

vlan 10
name ADMINISTRATION

vlan 20
name HR

end
```

## Access Port Configuration

```text
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
```

```text
interface fastEthernet 0/2
switchport mode access
switchport access vlan 20
```

## Trunk Configuration

```text
interface fastEthernet 0/24
switchport mode trunk
```

## Allowed VLAN Configuration

```text
interface fastEthernet 0/24
switchport trunk allowed vlan 20
```

Restore:

```text
interface fastEthernet 0/24
switchport trunk allowed vlan 10,20
```

## Verification

```text
show vlan brief

show interfaces trunk

show interfaces status
```

## Connectivity Testing

```text
ping 192.168.10.11

ping 192.168.10.20

ping 192.168.10.21
```

---

# 📸 Evidence

The lab contains screenshots documenting:

- Company topology
- Baseline connectivity
- VLAN 10 creation on SW1
- VLAN 10 creation on SW2
- SW1 trunk verification
- SW2 trunk verification
- VLAN communication across the trunk
- HR communication across the trunk
- Intentional allowed VLAN restriction
- VLAN 10 communication failure
- Trunk failure diagnosis
- Trunk configuration fix
- Final recovery

---

# 📁 Lab Structure

```text
LAB-08-Trunking/
│
├── LAB-08-Trunking.pkt
├── README.md
└── screenshots/
```



---

## ✅ Status

**Completed**

**100-Day IT Infrastructure Journey — LAB 08**

Next:

**LAB 09 — Inter-VLAN Routing**
