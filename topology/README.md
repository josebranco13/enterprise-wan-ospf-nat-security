# Network Topology

## Purpose

This folder provides the visual overview of the complete enterprise network.

It is the best starting point for anyone reviewing the repository for the first time.

The topology was designed to represent a small company with a central headquarters, two remote branch offices, internal services, a dedicated management segment, and a simulated ISP/public network.

---

## High-Level Design

```text
                         Simulated Public Network
                                  |
                              [RT-ISP]
                                  |
                              [RT-HQ]
                             /       \
                            /         \
                       [RT-BR1]     [RT-BR2]
                          |             |
                     Branch 1       Branch 2

                         |
                    Headquarters LAN
                Users / Servers / Management
```

The real Packet Tracer topology contains the switches, servers, client devices, and individual WAN connections used to implement this logical design.

---

## Headquarters

The headquarters is the central site.

It contains:

- `RT-HQ`;
- `SW1-HQ`;
- `SW2-HQ`;
- HQ user devices;
- `SRV-INTERNAL`;
- `PC-NETADMIN`.

The HQ network is logically separated into user, server, and management segments.

It also contains the connection toward the simulated ISP.

---

## Branch 1

Branch 1 contains:

- `RT-BR1`;
- `SW-BR1`;
- branch client devices.

Its local network is:

```text
10.31.10.0/24
```

Branch 1 connects to the headquarters through a dedicated WAN link.

---

## Branch 2

Branch 2 contains:

- `RT-BR2`;
- `SW-BR2`;
- branch client devices.

Its local network is:

```text
10.32.10.0/24
```

Like Branch 1, it reaches enterprise resources through the headquarters.

---

## ISP and Public Segment

`RT-ISP` simulates an upstream provider.

The ISP side allows the project to test:

- default routing;
- edge connectivity;
- NAT/PAT;
- communication with a simulated public server.

The public server uses:

```text
198.51.100.10
```

---

## Why This Topology Was Chosen

The topology is intentionally larger than a basic single-router lab.

It requires several networking functions to work together:

- LAN segmentation at headquarters;
- inter-VLAN routing;
- multi-site WAN routing;
- centralized services;
- branch-to-HQ communication;
- branch-to-branch communication;
- edge routing;
- traffic-control policies;
- management services;
- IPv6 routing.

This makes the project useful as a portfolio piece because it demonstrates integration rather than isolated commands.

---

## Recommended Evidence

The most important image in this folder is:

```text
full-topology.png
```

It should show the complete Packet Tracer workspace with device names and links visible.

An optional additional image is:

```text
physical-connections.png
```

This can provide a closer view of the interface-level cabling if the full topology image is too dense.

---

## How to Read the Topology

For a non-networking reviewer:

- routers connect different networks and sites;
- switches connect devices inside a local site;
- servers provide centralized services;
- client PCs represent users;
- WAN links connect the branches to headquarters;
- the ISP router represents the external network.

For a networking reviewer, the other folders provide the detailed implementation behind this diagram.
