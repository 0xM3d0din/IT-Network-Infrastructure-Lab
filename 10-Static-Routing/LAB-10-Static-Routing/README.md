# LAB 10 — Static Routing

## Objective

The objective of this lab is to understand and implement Static Routing between two separate networks connected through two routers.

The lab demonstrates how routers can use manually configured static routes to reach remote networks through a next-hop router.

The lab also reinforces routing-table verification, point-to-point connectivity, and end-to-end connectivity testing.

---

## Scenario

A small company network contains two separate LANs connected through two routers.

- LAN A → 192.168.10.0/24
- Transit Network → 10.0.0.0/30
- LAN B → 192.168.20.0/24

R1 connects LAN A to the transit network.

R2 connects the transit network to LAN B.

Because each router only knows about its directly connected networks initially, static routes are manually configured so that both routers know how to reach the remote LAN.

---

## Topology

    PC-A
    192.168.10.10/24
         |
        SW1
         |
    R1 G0/0
    192.168.10.1/24
         |
    R1 G0/1
    10.0.0.1/30
         |
    Transit Network
    10.0.0.0/30
         |
    R2 G0/0
    10.0.0.2/30
         |
    R2 G0/1
    192.168.20.1/24
         |
        SW2
         |
    PC-B
    192.168.20.10/24

---

## Devices

- 2 × Cisco 2911 Routers
- 2 × Cisco 2960 Switches
- 2 × PCs

---

## IP Addressing

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|--------|-----------|------------|-------------|-----------------|
| PC-A | NIC | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| R1 | G0/0 | 192.168.10.1 | 255.255.255.0 | — |
| R1 | G0/1 | 10.0.0.1 | 255.255.255.252 | — |
| R2 | G0/0 | 10.0.0.2 | 255.255.255.252 | — |
| R2 | G0/1 | 192.168.20.1 | 255.255.255.0 | — |
| PC-B | NIC | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |

---

## Network Design

### LAN A

    Network: 192.168.10.0/24
    R1 G0/0: 192.168.10.1
    PC-A: 192.168.10.10
    Gateway: 192.168.10.1

### Transit Network

    Network: 10.0.0.0/30
    R1 G0/1: 10.0.0.1
    R2 G0/0: 10.0.0.2

### LAN B

    Network: 192.168.20.0/24
    R2 G0/1: 192.168.20.1
    PC-B: 192.168.20.10
    Gateway: 192.168.20.1

---

## Why 10.0.0.0/30 Was Used

The R1-to-R2 link is a point-to-point connection that requires only two usable IP addresses.

The /30 network provides:

    10.0.0.0 → Network Address
    10.0.0.1 → R1
    10.0.0.2 → R2
    10.0.0.3 → Broadcast Address

The /30 network therefore provides exactly the two usable addresses needed for the router-to-router link.

---

# Configuration

## Step 1 — Configure R1 Interfaces

    enable
    configure terminal

    interface g0/0
    ip address 192.168.10.1 255.255.255.0
    no shutdown
    exit

    interface g0/1
    ip address 10.0.0.1 255.255.255.252
    no shutdown
    exit

    end

Verification:

    show ip interface brief

Expected state:

    G0/0 → 192.168.10.1 → up/up
    G0/1 → 10.0.0.1 → up/up

---

## Step 2 — Configure R2 Interfaces

    enable
    configure terminal

    hostname R2

    interface g0/0
    ip address 10.0.0.2 255.255.255.252
    no shutdown
    exit

    interface g0/1
    ip address 192.168.20.1 255.255.255.0
    no shutdown
    exit

    end

Verification:

    show ip interface brief

Observed:

    G0/0 → 10.0.0.2 → up/up
    G0/1 → 192.168.20.1 → up/up

---

## Step 3 — Verify the Transit Link

From R1:

    ping 10.0.0.2

The first test returned:

    Success rate: 80 percent (4/5)

The initial loss occurred during address resolution, after which the remaining packets succeeded.

A repeated connectivity test confirmed successful communication between R1 and R2.

The transit link was therefore verified as operational.

---

## Step 4 — Configure Static Route on R1

R1 is directly connected to:

    192.168.10.0/24
    10.0.0.0/30

R1 does not automatically know the remote LAN:

    192.168.20.0/24

A static route was therefore configured:

    enable
    configure terminal
    ip route 192.168.20.0 255.255.255.0 10.0.0.2
    end

Verification:

    show ip route

Observed:

    S 192.168.20.0/24 [1/0] via 10.0.0.2

This tells R1 to send traffic destined for 192.168.20.0/24 to R2 at 10.0.0.2.

---

## Step 5 — Configure Static Route on R2

R2 is directly connected to:

    192.168.20.0/24
    10.0.0.0/30

R2 does not automatically know the remote LAN:

    192.168.10.0/24

A return static route was therefore configured:

    enable
    configure terminal
    ip route 192.168.10.0 255.255.255.0 10.0.0.1
    end

Verification:

    show ip route

Observed:

    S 192.168.10.0/24 [1/0] via 10.0.0.1

This tells R2 to send return traffic destined for 192.168.10.0/24 to R1 at 10.0.0.1.

---

# Routing Logic

The routing path from PC-A to PC-B is:

    PC-A
    192.168.10.10
        ↓
    Default Gateway
    192.168.10.1
        ↓
    R1
        ↓
    Static Route
    192.168.20.0/24 via 10.0.0.2
        ↓
    R2
        ↓
    192.168.20.1
        ↓
    PC-B
    192.168.20.10

The return path is:

    PC-B
        ↓
    R2
        ↓
    Static Route
    192.168.10.0/24 via 10.0.0.1
        ↓
    R1
        ↓
    PC-A

Both directions require routing information.

---

# End-to-End Connectivity Verification

From PC-A:

    ping 192.168.20.10

The initial test showed:

    3 successful replies
    1 lost packet
    25% loss

The test was then repeated after address resolution completed.

Final connectivity was successfully verified between PC-A and PC-B.

The path crossed both routers:

    PC-A → R1 → R2 → PC-B

The observed TTL also reflected that the packet passed through routers.

---

# Important Concepts Learned

## Connected Routes

A router automatically knows networks directly connected to its active interfaces.

These appear in the routing table with:

    C

Example:

    C 192.168.10.0/24
    C 10.0.0.0/30

---

## Static Routes

A static route is manually configured by the administrator.

Static routes appear in the routing table with:

    S

Example:

    S 192.168.20.0/24 [1/0] via 10.0.0.2

---

## Next Hop

The next-hop address is the router that should receive the packet next.

For R1:

    Destination: 192.168.20.0/24
    Next Hop: 10.0.0.2

For R2:

    Destination: 192.168.10.0/24
    Next Hop: 10.0.0.1

---

## Bidirectional Routing

A route in only one direction is not sufficient for normal end-to-end communication.

R1 needs a route to LAN B.

R2 needs a return route to LAN A.

Therefore:

    R1 → 192.168.20.0/24 via R2
    R2 → 192.168.10.0/24 via R1

---

## Default Gateway

PC-A uses:

    192.168.10.1

as its default gateway.

PC-B uses:

    192.168.20.1

as its default gateway.

The default gateway is used when the destination is outside the local subnet.

---

# Verification Commands Used

### Router Interfaces

    show ip interface brief

### Routing Table

    show ip route

### Connectivity

    ping <destination>

---

# Troubleshooting Concepts Practiced

This lab reinforced a structured approach to routing verification:

    1. Verify interface status
    2. Verify IP addressing
    3. Verify router-to-router connectivity
    4. Check the routing table
    5. Confirm the destination network exists
    6. Confirm the correct next hop
    7. Test end-to-end connectivity

---

# Evidence

The lab evidence is stored in the Screenshots directory.

Key screenshots include:

    Topology.png
    R1-Interface-Configuration.png
    R1-Interface-Verification.png
    R2-Interface-Configuration.png
    R2-Interface-Verification.png
    Transit-Link-Connectivity.png
    Static-Route-R1-Verification.png
    Static-Route-R2-Verification.png
    End-to-End-Connectivity.png

---

# Files

    10-Static-Routing/
    └── LAB-10-Static-Routing/
        ├── LAB-10-Static-Routing.pkt
        ├── README.md
        └── Screenshots/

---

# Final Result

The lab successfully demonstrated:

    Network Addressing                 ✅
    Router Interface Configuration    ✅
    Point-to-Point Transit Network    ✅
    Router-to-Router Connectivity     ✅
    Static Route on R1                ✅
    Static Route on R2                ✅
    Routing Table Verification        ✅
    End-to-End Connectivity           ✅
    Bidirectional Routing             ✅

**Status: COMPLETED ✅**