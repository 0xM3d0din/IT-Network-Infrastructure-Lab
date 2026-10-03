# LAB 13 — Multi-Area OSPF

## Objective

The objective of this lab is to understand and implement Multi-Area OSPF using Cisco routers.

The lab demonstrates:

- OSPF Dynamic Routing
- OSPF Neighbor Adjacencies
- Link-State Routing
- OSPF Areas
- Area 0 Backbone
- Internal Routers
- Area Border Routers (ABRs)
- OSPF Link-State Database concepts
- SPF / Dijkstra concepts
- OSPF Cost
- OSPF Inter-Area Routes
- Passive Interfaces
- Routing Table Verification
- End-to-End Connectivity

No intentional OSPF link failure was performed in this lab. The lab was concluded after successful routing, connectivity, and passive-interface verification.

---

## Scenario

A small enterprise network is divided into three OSPF areas:

    Area 1 → Area 0 → Area 2

Area 0 is the OSPF backbone.

The network contains four routers:

    R1 → Internal Router — Area 1
    R2 → ABR — Area 1 + Area 0
    R3 → ABR — Area 0 + Area 2
    R4 → Internal Router — Area 2

The final logical structure is:

    PC-A
      |
     SW1
      |
     R1
      |
    Area 1
      |
     R2
      |
    Area 0
      |
     R3
      |
    Area 2
      |
     R4
      |
     SW2
      |
    PC-B

---

## OSPF Area Design

### Area 1

Contains:

    R1
    R2
    LAN A

### Area 0

The OSPF backbone.

Contains:

    R2
    R3

### Area 2

Contains:

    R3
    R4
    LAN B

---

## Devices

- 4 × Cisco 2911 Routers
- 2 × Cisco 2960 Switches
- 2 × PCs

Devices:

    R1
    R2
    R3
    R4

    SW1
    SW2

    PC-A
    PC-B

---

## IP Addressing

### PC-A

    IP Address:      192.168.10.10
    Subnet Mask:     255.255.255.0
    Default Gateway: 192.168.10.1
    DNS Server:      0.0.0.0

### PC-B

    IP Address:      192.168.20.10
    Subnet Mask:     255.255.255.0
    Default Gateway: 192.168.20.1
    DNS Server:      0.0.0.0

### R1

    G0/0 → 192.168.10.1/24
    G0/1 → 10.0.12.1/30

### R2

    G0/0 → 10.0.12.2/30
    G0/1 → 10.0.23.1/30

### R3

    G0/0 → 10.0.23.2/30
    G0/1 → 10.0.34.1/30

### R4

    G0/0 → 10.0.34.2/30
    G0/1 → 192.168.20.1/24

---

## Network Summary

    LAN A:
    192.168.10.0/24

    Transit 1:
    10.0.12.0/30

    Transit 2:
    10.0.23.0/30

    Transit 3:
    10.0.34.0/30

    LAN B:
    192.168.20.0/24

---

# OSPF Concepts Practiced

## Dynamic Routing

OSPF is a Dynamic Routing Protocol.

Unlike Static Routing, where routes are manually configured, OSPF allows routers to exchange routing information and dynamically learn remote networks.

Static Routing:

    Administrator
          ↓
    Manual Route
          ↓
    Remote Network

OSPF:

    Router
      ↓
    Discover Neighbors
      ↓
    Exchange OSPF Information
      ↓
    Build Routing Knowledge
      ↓
    Run SPF
      ↓
    Install Best Routes

---

## OSPF Neighbors

Routers running OSPF on directly connected links can establish neighbor relationships.

In this lab:

    R1 ↔ R2
    R2 ↔ R3
    R3 ↔ R4

The OSPF adjacencies reached FULL state.

R2 neighbor verification showed:

    Neighbor ID     State
    192.168.10.1    FULL/DR
    10.0.34.1       FULL/BDR

This confirmed complete OSPF adjacencies with both directly connected OSPF neighbors.

---

## Link-State Database

OSPF is a link-state routing protocol.

Routers build a Link-State Database (LSDB) containing information about the topology, links, and associated routing information learned through OSPF.

This information is used to construct a view of the OSPF topology.

---

## SPF Algorithm

OSPF uses the Shortest Path First algorithm based on Dijkstra's algorithm.

Conceptually:

    Topology Information
            ↓
    Link-State Database
            ↓
       SPF Calculation
            ↓
        Best Path
            ↓
       Routing Table

---

## OSPF Cost

The primary OSPF metric is:

    Cost

The path with the lower total OSPF cost is preferred.

Interface bandwidth and OSPF reference bandwidth are involved in determining interface cost according to the OSPF configuration.

Conceptually:

    Interface Characteristics
            ↓
        OSPF Cost
            ↓
      Total Path Cost
            ↓
        Best Path

---

# OSPF Areas

## Area 0

Area 0 is the OSPF backbone.

In this lab:

    R2 ↔ R3

forms the backbone connection.

---

## Area 1

Area 1 contains:

    R1
    R2
    LAN A

R1 is an Internal Router in Area 1.

R2 connects Area 1 to Area 0.

---

## Area 2

Area 2 contains:

    R3
    R4
    LAN B

R4 is an Internal Router in Area 2.

R3 connects Area 2 to Area 0.

---

# Area Border Routers

An Area Border Router (ABR) participates in more than one OSPF area.

In this lab:

    R2 → Area 1 + Area 0
    R3 → Area 0 + Area 2

Therefore:

    R2 = ABR
    R3 = ABR

The final structure is:

    Area 1
       |
      R2
      ABR
       |
    Area 0
       |
      R3
      ABR
       |
    Area 2

---

# Initial Connectivity Verification

Before OSPF was configured, the directly connected R1-to-R2 transit link was tested.

From R1:

    ping 10.0.12.2

The initial test produced:

    Success rate: 80 percent (4/5)

The directly connected transit link was operational.

A second test was performed:

    ping 10.0.23.2

At that stage the result was:

    Success rate: 0 percent (0/5)

This was expected because R1 did not yet have a route to the remote 10.0.23.0/30 network.

This demonstrated the difference between:

    Directly Connected Network
    vs.
    Remote Network

before dynamic routing was enabled.

---

# Router Interface Configuration

## R1

    enable
    configure terminal

    hostname R1

    interface g0/0
    ip address 192.168.10.1 255.255.255.0
    no shutdown
    exit

    interface g0/1
    ip address 10.0.12.1 255.255.255.252
    no shutdown
    exit

    end

Verification:

    show ip interface brief

Observed:

    G0/0 → 192.168.10.1 → up/up
    G0/1 → 10.0.12.1 → up/up

---

## R2

    enable
    configure terminal

    hostname R2

    interface g0/0
    ip address 10.0.12.2 255.255.255.252
    no shutdown
    exit

    interface g0/1
    ip address 10.0.23.1 255.255.255.252
    no shutdown
    exit

    end

Verification:

    show ip interface brief

Observed:

    G0/0 → 10.0.12.2 → up/up
    G0/1 → 10.0.23.1 → up/up

---

## R3

    enable
    configure terminal

    hostname R3

    interface g0/0
    ip address 10.0.23.2 255.255.255.252
    no shutdown
    exit

    interface g0/1
    ip address 10.0.34.1 255.255.255.252
    no shutdown
    exit

    end

Verification:

    show ip interface brief

Observed:

    G0/0 → 10.0.23.2 → up/up
    G0/1 → 10.0.34.1 → up/up

---

## R4

    enable
    configure terminal

    hostname R4

    interface g0/0
    ip address 10.0.34.2 255.255.255.252
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

    G0/0 → 10.0.34.2 → up/up
    G0/1 → 192.168.20.1 → up/up

---

# OSPF Configuration

## R1 — Area 1

    enable
    configure terminal

    router ospf 1
    network 192.168.10.0 0.0.0.255 area 1
    network 10.0.12.0 0.0.0.3 area 1

    end

Verification:

    show ip protocols

Observed:

    Routing Protocol is "ospf 1"

    192.168.10.0 0.0.0.255 area 1
    10.0.12.0 0.0.0.3 area 1

R1 was confirmed as an Area 1 router.

---

## R2 — Area 1 + Area 0

    enable
    configure terminal

    router ospf 1
    network 10.0.12.0 0.0.0.3 area 1
    network 10.0.23.0 0.0.0.3 area 0

    end

Verification:

    show ip protocols

Observed:

    Number of areas in this router is 2

    10.0.12.0 0.0.0.3 area 1
    10.0.23.0 0.0.0.3 area 0

R2 was therefore operating in Area 1 and Area 0 and acting as an ABR.

An OSPF adjacency with R1 was established and reached FULL state.

---

## R3 — Area 0 + Area 2

    enable
    configure terminal

    router ospf 1
    network 10.0.23.0 0.0.0.3 area 0
    network 10.0.34.0 0.0.0.3 area 2

    end

Verification:

    show ip protocols

Observed:

    Number of areas in this router is 2

    10.0.23.0 0.0.0.3 area 0
    10.0.34.0 0.0.0.3 area 2

R3 was therefore operating in Area 0 and Area 2 and acting as an ABR.

An OSPF adjacency with R2 was established and reached FULL state.

---

## R4 — Area 2

    enable
    configure terminal

    router ospf 1
    network 10.0.34.0 0.0.0.3 area 2
    network 192.168.20.0 0.0.0.255 area 2

    end

Verification:

    show ip protocols

Observed:

    Number of areas in this router is 1

    10.0.34.0 0.0.0.3 area 2
    192.168.20.0 0.0.0.255 area 2

R4 was confirmed as an Area 2 router.

The OSPF adjacency with R3 reached FULL state.

---

# OSPF Neighbor Verification

On R2:

    show ip ospf neighbor

Observed:

    Neighbor ID     State      Address
    192.168.10.1    FULL/DR    10.0.12.1
    10.0.34.1       FULL/BDR   10.0.23.2

This confirmed that R2 had complete OSPF adjacencies with:

    R1
    R3

The OSPF neighbor chain was:

    R1 ↔ R2 ↔ R3 ↔ R4

---

# Routing Table Verification

## R2

Command:

    show ip route

Observed connected networks:

    C 10.0.12.0/30
    C 10.0.23.0/30

Observed OSPF routes:

    O 192.168.10.0/24

Observed Inter-Area OSPF routes:

    O IA 10.0.34.0/30
    O IA 192.168.20.0/24

Example:

    O IA 192.168.20.0/24 [110/3] via 10.0.23.2

This confirmed that R2 learned LAN B through another OSPF area.

---

## R4

Command:

    show ip route

Observed connected networks:

    C 10.0.34.0/30
    C 192.168.20.0/24

Observed Inter-Area OSPF routes:

    O IA 10.0.12.0/30
    O IA 10.0.23.0/30
    O IA 192.168.10.0/24

Example:

    O IA 192.168.10.0/24 [110/4] via 10.0.34.1

This confirmed that R4 learned LAN A from Area 1 through the OSPF backbone.

---

## R1

Command:

    show ip route

Observed connected networks:

    C 10.0.12.0/30
    C 192.168.10.0/24

Observed Inter-Area OSPF routes:

    O IA 10.0.23.0/30 via 10.0.12.2
    O IA 10.0.34.0/30 via 10.0.12.2
    O IA 192.168.20.0/24 via 10.0.12.2

Most importantly:

    O IA 192.168.20.0/24 [110/4] via 10.0.12.2

This confirmed that R1 learned LAN B dynamically through R2, Area 0, and the Area 2 side of the OSPF topology.

---

# End-to-End Connectivity

From PC-A:

    ping 192.168.20.10

The initial test showed:

    3 successful replies
    1 lost packet
    25% loss

The test was repeated after the initial address-resolution activity.

Final connectivity was successfully verified between:

    PC-A
    192.168.10.10

and:

    PC-B
    192.168.20.10

The communication path crossed the OSPF topology:

    PC-A
      ↓
    SW1
      ↓
    R1
      ↓
    R2
      ↓
    R3
      ↓
    R4
      ↓
    SW2
      ↓
    PC-B

This confirmed successful end-to-end communication across multiple OSPF areas.

---

# Passive Interface

Passive interfaces were configured on LAN-facing interfaces where no OSPF neighbor relationship was required.

The purpose is to:

- Prevent OSPF neighbor formation on the LAN-facing interface
- Continue advertising the connected network into OSPF

---

## R1 Passive Interface

R1 G0/0 connects to the LAN.

Configuration:

    enable
    configure terminal

    router ospf 1
    passive-interface g0/0

    end

Verification:

    show ip protocols

Observed:

    Passive Interface(s):
    GigabitEthernet0/0

R1 G0/0 remained part of the OSPF network advertisement while no OSPF adjacency was required on that LAN interface.

---

## R4 Passive Interface

R4 G0/1 connects to the LAN.

Configuration:

    enable
    configure terminal

    router ospf 1
    passive-interface g0/1

    end

Verification:

    show ip protocols

Observed:

    Passive Interface(s):
    GigabitEthernet0/1

R4 G0/1 remained part of the OSPF network advertisement while no OSPF adjacency was required on that LAN interface.

---

# OSPF Area Structure

The completed Multi-Area design was:

    Area 1
    192.168.10.0/24
          |
         R1
          |
         R2
         ABR
          |
       Area 0
      Backbone
          |
         R3
         ABR
          |
       Area 2
    192.168.20.0/24
          |
         R4

Roles:

    R1 → Internal Router — Area 1
    R2 → ABR — Area 1 + Area 0
    R3 → ABR — Area 0 + Area 2
    R4 → Internal Router — Area 2

---

# Important Concepts Learned

## OSPF vs Static Routing

Static Routing:

    Administrator manually defines the route.

OSPF:

    Routers dynamically exchange routing information
    and calculate paths.

---

## OSPF Route Codes

    O
    → OSPF route within the OSPF routing domain/context

    O IA
    → OSPF Inter-Area route

The lab provided practical examples of both.

---

## Area 0

Area 0 is the OSPF backbone and provides the backbone path between non-backbone areas in the Multi-Area design.

---

## ABR

An ABR connects different OSPF areas.

In this lab:

    R2 → Area 1 + Area 0
    R3 → Area 0 + Area 2

---

## Passive Interface

A passive interface:

    Does not form OSPF neighbor adjacencies

but:

    Can still advertise the connected network into OSPF

This is useful on interfaces connected to end-user LANs where OSPF neighbor formation is unnecessary.

---

# Verification Commands Used

### Router Interfaces

    show ip interface brief

### OSPF Protocol Configuration

    show ip protocols

### OSPF Neighbors

    show ip ospf neighbor

### Routing Table

    show ip route

### Connectivity

    ping <destination>

---

# Troubleshooting / Verification Method

The lab followed a structured workflow:

    1. Build the topology
    2. Configure IP addressing
    3. Verify router interfaces
    4. Verify directly connected connectivity
    5. Configure OSPF by area
    6. Verify OSPF process configuration
    7. Verify neighbor adjacencies
    8. Verify learned routes
    9. Test end-to-end connectivity
    10. Configure passive interfaces
    11. Verify the final OSPF configuration

---

# Evidence

The following screenshots were captured during the lab:

    Topology.png

    R1-Interface-Verification.png
    R2-Interface-Verification.png
    R3-Interface-Verification.png
    R4-Interface-Verification.png

    Basic-Connectivity-Verification.png

    R1-OSPF-Protocol-Verification.png
    R2-OSPF-ABR-Verification.png
    R3-OSPF-ABR-Verification.png
    R4-OSPF-Protocol-Verification.png

    R2-OSPF-Neighbors.png

    R2-OSPF-Routing-Table.png
    R4-OSPF-Routing-Table.png
    R1-OSPF-Routing-Table.png

    End-to-End-Connectivity.png

    R1-OSPF-Passive-Interface.png
    R4-OSPF-Passive-Interface.png

Additional screenshots captured during the lab may also be stored in the Screenshots directory.

---

# Files

    13-OSPF/
    └── LAB-13-OSPF/
        ├── LAB-13-OSPF.pkt
        ├── README.md
        └── Screenshots/

---

# Final Result

The lab successfully demonstrated:

    Multi-Area OSPF                    ✅
    Area 1                             ✅
    Area 0 Backbone                    ✅
    Area 2                             ✅
    Internal Router Roles              ✅
    ABR Roles                          ✅
    OSPF Neighbor Formation            ✅
    FULL Adjacencies                   ✅
    OSPF Route Learning                ✅
    OSPF Inter-Area Routes             ✅
    O / O IA Verification              ✅
    Routing Table Verification         ✅
    End-to-End Connectivity            ✅
    Passive Interface                  ✅
    OSPF Protocol Verification         ✅

The lab was concluded after successful Multi-Area OSPF operation, route learning, end-to-end connectivity, and passive-interface verification.

**Status: COMPLETED ✅**