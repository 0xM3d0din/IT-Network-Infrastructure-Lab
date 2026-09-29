# LAB 09 — Inter-VLAN Routing

## Objective

The objective of this lab is to understand and implement Inter-VLAN Routing using a Cisco router, 802.1Q trunking, and router subinterfaces.

This lab demonstrates how devices in different VLANs and different IP subnets can communicate through a Layer 3 router.

The lab also includes an intentional configuration failure followed by troubleshooting, correction, and final verification.

---

## Scenario

A company has two departments connected to the same Layer 2 switch:

- Administration — VLAN 10
- HR — VLAN 20

Each department is placed in a separate VLAN and IP subnet.

A single physical link connects the switch to the router using an 802.1Q trunk.

The router uses subinterfaces to provide a default gateway for each VLAN and perform Inter-VLAN Routing.

---

## Topology

R1 G0/0
    |
    | 802.1Q Trunk
    |
SW1 Fa0/24
   |       |
Fa0/1   Fa0/2
   |       |
Admin-PC  HR-PC

R1:
G0/0.10 → 192.168.10.1/24 → VLAN 10
G0/0.20 → 192.168.20.1/24 → VLAN 20

---

## Devices

- 1 × Cisco 2911 Router
- 1 × Cisco 2960 Switch
- 2 × PCs

---

## VLAN and IP Addressing

| Device | Interface | VLAN | IP Address | Subnet Mask | Default Gateway |
|--------|-----------|------|------------|-------------|-----------------|
| Admin-PC | NIC | 10 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| HR-PC | NIC | 20 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |
| R1 | G0/0.10 | 10 | 192.168.10.1 | 255.255.255.0 | — |
| R1 | G0/0.20 | 20 | 192.168.20.1 | 255.255.255.0 | — |

---

## Switch Port Assignment

| SW1 Port | Connected Device | Mode | VLAN |
|-----------|-------------------|------|------|
| Fa0/1 | Admin-PC | Access | VLAN 10 |
| Fa0/2 | HR-PC | Access | VLAN 20 |
| Fa0/24 | R1 G0/0 | Trunk | VLAN 10, VLAN 20 |

---

## Step 1 — Create VLANs

Commands used on SW1:

    enable
    configure terminal

    vlan 10
    name Administration
    exit

    vlan 20
    name HR
    exit

    end

Verification:

    show vlan brief

Observed:

    VLAN 10  ADMINISTRATION  active
    VLAN 20  HR              active

---

## Step 2 — Assign Access Ports

Administration:

    configure terminal
    interface fa0/1
    switchport mode access
    switchport access vlan 10
    end

HR:

    configure terminal
    interface fa0/2
    switchport mode access
    switchport access vlan 20
    end

Verification:

    show vlan brief

Observed:

    VLAN 10 → Fa0/1
    VLAN 20 → Fa0/2

---

## Step 3 — Configure the Trunk

SW1 Fa0/24 connects to R1 G0/0.

    configure terminal
    interface fa0/24
    switchport mode trunk
    end

Verification:

    show interfaces trunk

Observed:

    Fa0/24
    Status: trunking
    Encapsulation: 802.1Q
    Native VLAN: 1

Observed VLAN information:

    VLANs allowed and active:
    1, 10, 20

    VLANs forwarding:
    1, 10, 20

---

## Step 4 — Enable R1 G0/0

    enable
    configure terminal
    interface g0/0
    no shutdown
    end

The physical interface became operational.

---

## Step 5 — Configure VLAN 10 Subinterface

    enable
    configure terminal
    interface g0/0.10
    encapsulation dot1Q 10
    ip address 192.168.10.1 255.255.255.0
    no shutdown
    end

Verification:

    show ip interface brief

Observed:

    GigabitEthernet0/0.10
    IP Address: 192.168.10.1
    Status: up
    Protocol: up

---

## Step 6 — Configure VLAN 20 Subinterface

    configure terminal
    interface g0/0.20
    encapsulation dot1Q 20
    ip address 192.168.20.1 255.255.255.0
    no shutdown
    end

Verification:

    show ip interface brief

Observed:

    GigabitEthernet0/0.20
    IP Address: 192.168.20.1
    Status: up
    Protocol: up

Final subinterface state:

    G0/0.10 → 192.168.10.1 → up/up
    G0/0.20 → 192.168.20.1 → up/up

---

## Gateway Connectivity Verification

### Admin-PC → VLAN 10 Gateway

    ping 192.168.10.1

Result:

    4/4 replies
    0% loss

### HR-PC → VLAN 20 Gateway

    ping 192.168.20.1

Result:

    4/4 replies
    0% loss

These tests confirmed that both PCs could reach their respective router subinterfaces.

---

## Inter-VLAN Connectivity Test

From Admin-PC:

    ping 192.168.20.10

Result:

    4/4 replies
    0% loss

This confirmed successful communication from VLAN 10 to VLAN 20 through R1.

Traffic path:

    Admin-PC
    192.168.10.10
          ↓
        SW1
          ↓
    802.1Q Trunk
          ↓
      R1 G0/0.10
          ↓
    Layer 3 Routing
          ↓
      R1 G0/0.20
          ↓
        SW1
          ↓
       HR-PC
    192.168.20.10

---

## Routing Table Verification

On R1:

    show ip route

Observed connected networks:

    C 192.168.10.0/24 → GigabitEthernet0/0.10
    C 192.168.20.0/24 → GigabitEthernet0/0.20

Observed local routes:

    L 192.168.10.1/32 → GigabitEthernet0/0.10
    L 192.168.20.1/32 → GigabitEthernet0/0.20

These entries confirm that R1 has a directly connected Layer 3 interface for each VLAN network.

---

## Intentional Failure

The default gateway on Admin-PC was intentionally changed from:

    192.168.10.1

to:

    192.168.10.254

The IP address and subnet mask remained unchanged.

Incorrect configuration:

    IP Address:      192.168.10.10
    Subnet Mask:     255.255.255.0
    Default Gateway: 192.168.10.254

---

## Failure Test

From Admin-PC:

    ping 192.168.20.10

Result:

    4 packets sent
    0 packets received
    100% loss

The Admin-PC could no longer reach the HR network.

---

## Troubleshooting

The real gateway was tested directly:

    ping 192.168.10.1

Result:

    4/4 replies
    0% loss

This confirmed that Admin-PC could still communicate with the router's VLAN 10 interface.

The Admin-PC configuration was checked using:

    ipconfig

Observed configuration:

    IPv4 Address:    192.168.10.10
    Subnet Mask:     255.255.255.0
    Default Gateway: 192.168.10.254

The actual router gateway was:

    192.168.10.1

Root cause:

    Incorrect Default Gateway configured on Admin-PC.

---

## Fix

The Admin-PC default gateway was corrected to:

    192.168.10.1

Final configuration:

    IP Address:      192.168.10.10
    Subnet Mask:     255.255.255.0
    Default Gateway: 192.168.10.1

No other IP configuration was changed.

---

## Final Recovery Verification

From Admin-PC:

    ping 192.168.20.10

Result:

    Packets: Sent = 4
    Received = 4
    Lost = 0
    0% loss

The failed connection was successfully restored.

---

## Reverse Connectivity Test

From HR-PC:

    ping 192.168.10.10

Result:

    4/4 replies
    0% loss

This confirmed successful bidirectional communication between VLAN 10 and VLAN 20.

---

## Troubleshooting Flow

    Inter-VLAN Ping Failure
            ↓
    Test Local Gateway
            ↓
    Gateway Reachable
            ↓
    Check Host IP Configuration
            ↓
    Verify Default Gateway
            ↓
    Identify Incorrect Gateway
            ↓
    Correct Gateway
            ↓
    Repeat Inter-VLAN Test
            ↓
        4/4 Replies
            ↓
      Problem Resolved

---

## Key Concepts Learned

### VLAN

A VLAN creates a separate Layer 2 broadcast domain on a switch.

In this lab:

    VLAN 10 → Administration
    VLAN 20 → HR

### Different IP Subnets

Each VLAN uses a separate IPv4 network:

    VLAN 10 → 192.168.10.0/24
    VLAN 20 → 192.168.20.0/24

### Trunk

The SW1 Fa0/24 interface carries traffic for multiple VLANs over one physical link using 802.1Q tagging.

    SW1 Fa0/24 ↔ R1 G0/0

### Router Subinterfaces

R1 uses multiple logical subinterfaces on one physical interface:

    G0/0.10 → VLAN 10 → 192.168.10.1/24
    G0/0.20 → VLAN 20 → 192.168.20.1/24

Each subinterface acts as the Layer 3 gateway for its VLAN.

### Inter-VLAN Routing

Devices in different VLANs cannot communicate through Layer 2 switching alone.

R1 provides Layer 3 routing between:

    192.168.10.0/24
            ↕
           R1
            ↕
    192.168.20.0/24

### Default Gateway

A host uses its default gateway when the destination is outside its local subnet.

Admin-PC:

    192.168.10.10/24
    Gateway: 192.168.10.1

HR-PC:

    192.168.20.10/24
    Gateway: 192.168.20.1

An incorrect default gateway can prevent communication with remote networks even when local connectivity is working.

---

## Troubleshooting Method Practiced

The intentional failure demonstrated this troubleshooting workflow:

    1. Reproduce the problem
    2. Test local gateway connectivity
    3. Inspect host IP configuration
    4. Compare the configured gateway with the actual gateway
    5. Identify the root cause
    6. Correct the configuration
    7. Re-test the original failure
    8. Perform reverse-direction verification

---

## Verification Commands Used

### Switch

    show vlan brief
    show interfaces trunk

### Router

    show ip interface brief
    show ip route

### PC

    ipconfig
    ping <destination>

---

## Evidence

The lab evidence is stored in the Screenshots directory.

Key screenshots include:

    Topology.png
    Trunk-Verification.png
    R1-Subinterfaces-Verification.png
    Inter-VLAN-Connectivity-Test.png
    Routing-Table-Verification.png
    Wrong-Gateway-Configuration.png
    Wrong-Gateway-Diagnosis.png
    Gateway-Fix-Configuration.png
    Final-Recovery.png
    Reverse-Inter-VLAN-Connectivity.png

Additional screenshots captured during the lab are also stored in the same directory.

---

## Files

    09-Inter-VLAN-Routing/
    └── LAB-09-Inter-VLAN-Routing/
        ├── LAB-09-Inter-VLAN-Routing.pkt
        ├── README.md
        └── Screenshots/

---

## Final Result

The lab successfully demonstrated:

    VLAN Creation                    ✅
    Access Port Assignment           ✅
    802.1Q Trunk Configuration       ✅
    Router Subinterfaces             ✅
    Inter-VLAN Routing               ✅
    Gateway Verification             ✅
    Routing Table Verification       ✅
    Intentional Failure              ✅
    Troubleshooting                  ✅
    Root Cause Identification        ✅
    Configuration Fix                ✅
    Final Recovery                   ✅
    Bidirectional Connectivity       ✅

**Status: COMPLETED ✅**