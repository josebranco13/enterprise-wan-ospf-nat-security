# Network Topology

## Purpose

This folder provides the visual overview of the complete enterprise network.

The topology was designed to simulate a small company with a central headquarters, two remote branch offices, internal services, network management, and a simulated ISP/public network.

---

## Complete Topology

<p align="center">
  <img src="topology.png" alt="Complete Cisco Packet Tracer enterprise topology" width="1000">
</p>

<p align="center">
  <em>Complete enterprise topology implemented in Cisco Packet Tracer.</em>
</p>

---

## Main Areas

The topology is divided into four logical areas:

### Headquarters

The headquarters contains the central enterprise router, two switches, user devices, internal services, and the network-administration workstation.

It is also the central point connecting the branch offices and the simulated ISP.

### Branch 1

Branch 1 represents a remote company site connected to headquarters through a WAN link.

It has its own router, switch, client network, and addressing range.

### Branch 2

Branch 2 represents a second remote company site and follows the same general design as Branch 1.

### ISP / Public Network

The ISP router represents the external side of the topology.

A simulated public server allows the project to validate edge routing and NAT behavior without requiring real Internet access.

---

## Logical View

```text
                     Simulated Public Network
                              |
                           RT-ISP
                              |
                           RT-HQ
                          /     \
                         /       \
                    RT-BR1     RT-BR2
                      |           |
                   Branch 1    Branch 2

                   Headquarters LAN
            Users / Servers / Management
```

---

## Why This Topology Was Chosen

The design allows several networking topics to be combined in one environment:

- VLAN segmentation;
- inter-VLAN routing;
- WAN links;
- dynamic routing;
- centralized DHCP;
- NAT/PAT;
- ACL security;
- SSH administration;
- network monitoring;
- IPv6;
- troubleshooting.

Instead of demonstrating these concepts as isolated exercises, the topology requires them to work together as one enterprise network.

---

## Where to Continue

After reviewing the topology, the following folders provide the implementation details:

```text
addressing/      → IPv4 and IPv6 addressing
vlan-trunks/     → VLAN segmentation and trunks
routing/         → OSPF and routing tables
dhcp/            → centralized DHCP
nat/             → NAT/PAT
security/        → ACLs and SSH
management/      → NTP, Syslog and SNMP
ipv6/            → IPv6 and OSPFv3
connectivity/    → end-to-end validation
troubleshooting/ → deliberate faults and diagnosis
```
