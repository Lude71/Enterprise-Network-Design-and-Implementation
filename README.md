# Enterprise Network Design and Implementation

A practical enterprise networking project built in **Cisco Packet Tracer** to demonstrate VLAN segmentation, 802.1Q trunking, inter-VLAN routing, wireless access, server connectivity, and Spanning Tree Protocol (STP) redundancy.

## Project Overview

This lab models a small enterprise network with separate Engineering, Finance, Management/WLAN, and Server networks. A Cisco 2911 router provides inter-VLAN routing through router-on-a-stick, while three Cisco 2960 switches provide Layer 2 connectivity. A redundant switch link creates a triangle topology so STP can prevent loops and provide an alternate path during link failure.

## Objectives

- Segment departments and servers using VLANs and IPv4 subnets.
- Configure access ports and 802.1Q trunk links.
- Implement router-on-a-stick inter-VLAN routing.
- Connect a wireless laptop to the Management VLAN.
- Verify access to a server and its HTTP service.
- Observe STP port roles and demonstrate failover and recovery.
- Document verification commands and troubleshooting steps.

## Network Topology

**Devices:** 1 Cisco 2911 router (R0), 3 Cisco 2960 switches (SW0, SW1, SW2), Engineering PC, Finance PC, Management PC, Server PC, dedicated Server, Access Point (AP0), and wireless laptop.

**Key links**
- R0 G0/0 ↔ SW0 Fa0/1
- SW0 Fa0/2 ↔ SW1 Fa0/1
- SW0 Fa0/3 ↔ SW2 Fa0/1
- SW1 Fa0/4 ↔ SW2 Fa0/6 (redundant trunk)


## VLAN and IP Addressing Plan

| VLAN | Name | Network | Default Gateway | Purpose |
|---:|---|---|---|---|
| 10 | ENGINEERING | `192.168.10.0/24` | `192.168.10.1` | Engineering |
| 20 | FINANCE | `192.168.20.0/24` | `192.168.20.1` | Finance |
| 30 | MANAGEMENT | `192.168.30.0/24` | `192.168.30.1` | Management and WLAN |
| 40 | SERVERS | `192.168.40.0/24` | `192.168.40.1` | Server network |

### End-device addressing

| Device | IP address | Mask | Gateway |
|---|---|---|---|
| ENG PC | `192.168.10.10` | `255.255.255.0` | `192.168.10.1` |
| FIN PC | `192.168.20.10` | `255.255.255.0` | `192.168.20.1` |
| MGMT PC | `192.168.30.10` | `255.255.255.0` | `192.168.30.1` |
| Wireless laptop | `192.168.30.20` | `255.255.255.0` | `192.168.30.1` |
| Server PC | `192.168.40.10` | `255.255.255.0` | `192.168.40.1` |
| Dedicated Server | `192.168.40.20` | `255.255.255.0` | `192.168.40.1` |

## Technologies and Concepts

- **VLANs:** Separate departments and servers into different broadcast domains.
- **Access ports:** End-device ports assigned to a single VLAN.
- **802.1Q trunking:** Carries multiple VLANs between switches and between the switch and router.
- **Router-on-a-stick:** Router subinterfaces using `encapsulation dot1Q` route between VLAN subnets.
- **WLAN:** SSID `Lude-Lab` connects the laptop into VLAN 30.
- **HTTP service:** The dedicated server at `192.168.40.20` serves the Packet Tracer default web page.
- **STP:** Prevents Layer 2 loops by placing a redundant port into an alternate/blocking state while preserving a backup path.

## Verification and Results

| Test | Observed result |
|---|---|
| ENG PC → FIN PC (`192.168.20.10`) | 4/4 replies on the repeat test |
| ENG PC → MGMT PC (`192.168.30.10`) | 4/4 replies |
| ENG PC → Server (`192.168.40.20`) | 4/4 replies, 0% packet loss |
| Server PC → Server (`192.168.40.20`) | 4/4 replies |
| Laptop → Management gateway (`192.168.30.1`) | 4/4 replies |
| Laptop → Server (`192.168.40.20`) | 4/4 replies |
| Laptop browser → `http://192.168.40.20` | Packet Tracer welcome page loaded |
| STP failover | SW0 Fa0/3 changed from `Altn BLK` to `Root FWD` after SW0–SW1 link was disconnected |
| STP recovery | After reconnection, SW0 Fa0/2 returned to `Root FWD` and Fa0/3 to `Altn BLK` |

Example verification commands:

```text
show vlan brief
show interfaces trunk
show interfaces fa0/4 switchport
show ip interface brief
show ip route
show spanning-tree vlan 10
ping 192.168.40.20
```

## Repository Contents

```text
Enterprise-Network-Design-and-Implementation/
├── Lude_Enterprise_Network_Design_and_Implementation.pkt
├── Enterprise_Network_Lab_Full_Documentation.docx
├── README.md
└── screenshots/
    ├── topology.png
    ├── successful-ping.png
    ├── router-interfaces.png
    ├── stp-normal.png
    └── stp-failover.png
```


## How to Use

1. Download the `.pkt` file from this repository.
2. Open it in Cisco Packet Tracer.
3. Inspect the VLANs, trunk links, router subinterfaces, wireless connection, and server configuration.
4. Use the commands above to verify the configuration.
5. To repeat the STP demonstration, disconnect SW0 Fa0/2 ↔ SW1 Fa0/1, check `show spanning-tree vlan 10` on SW0, reconnect the link, and verify convergence.

## What I Learned

- How VLAN segmentation and trunk links work together.
- How router subinterfaces provide inter-VLAN routing.
- How to validate addressing, gateways, and end-to-end connectivity.
- How wireless clients reach wired resources through VLANs and routing.
- How STP prevents switching loops and reacts to a failed link.
- How to troubleshoot using switch/router show commands and ping tests.

## Limitations and Notes

This is a simulated learning lab in Cisco Packet Tracer, not a production deployment. Device models and Packet Tracer behavior may differ from physical hardware. Results reflect tests performed in the simulation.

## Author

**Lude71**  
Telecommunication and Information Engineering student | Networking, infrastructure, and cybersecurity learning

---

*Built as a hands-on networking portfolio project.*
