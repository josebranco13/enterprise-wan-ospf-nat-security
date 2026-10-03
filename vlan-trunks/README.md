# VLANs and Trunking

## Purpose

This folder documents the Layer 2 segmentation used at the headquarters.

Instead of placing users, servers, and network-management devices in one large LAN, the headquarters separates them into different VLANs.

This improves organization and allows the router and security policies to treat each group independently.

---

## VLAN Design

The headquarters uses:

| VLAN | Name | IPv4 Network |
|---|---|---|
| 10 | HQ-USERS | `10.30.10.0/24` |
| 20 | HQ-SERVERS | `10.30.20.0/24` |
| 99 | HQ-MGMT | `10.30.99.0/24` |

A native/dead VLAN is also used for trunk configuration:

```text
VLAN 999
```

The exact purpose is to avoid using a production VLAN as the trunk native VLAN.

---

## Access Ports

An access port belongs to one VLAN and normally connects to an end device.

Examples in the topology include:

```text
User PC       → VLAN 10
Internal Server → VLAN 20
PC-NETADMIN   → VLAN 99
```

This means broadcasts from one VLAN do not automatically become broadcasts in another VLAN.

---

## Trunk Links

A trunk carries traffic for multiple VLANs over a single physical connection.

The headquarters uses trunk links between:

```text
RT-HQ ↔ SW1-HQ
SW1-HQ ↔ SW2-HQ
```

802.1Q tags identify which VLAN each Ethernet frame belongs to while it crosses the trunk.

---

## Router-on-a-Stick

`RT-HQ` performs inter-VLAN routing using subinterfaces.

Conceptually:

```text
Physical interface G0/0
        |
        +-- G0/0.10 → VLAN 10 → 10.30.10.1
        +-- G0/0.20 → VLAN 20 → 10.30.20.1
        +-- G0/0.99 → VLAN 99 → 10.30.99.1
```

Each subinterface acts as the default gateway for its VLAN.

This design allows one physical router interface to route between several VLANs.

---

## Switch Management

The headquarters switches use management addresses in VLAN 99.

Examples:

```text
SW1-HQ → 10.30.99.11
SW2-HQ → 10.30.99.12
```

This keeps infrastructure management separate from the ordinary user network.

---

## Verification

Useful commands include:

```text
show vlan brief
show interfaces trunk
show ip interface brief
```

### `show vlan brief`

Used to confirm:

- VLAN existence;
- VLAN names;
- access-port membership.

### `show interfaces trunk`

Used to confirm:

- which links are trunking;
- native VLAN;
- allowed VLANs.

---

## Recommended Evidence

```text
sw1-hq-vlans.png
sw1-hq-trunks.png
sw2-hq-vlans.png
sw2-hq-trunks.png
```

These screenshots should show both VLAN membership and trunk operation.

---

## Common Problems

Typical VLAN/trunk issues include:

- VLAN not created;
- port assigned to the wrong VLAN;
- access port accidentally configured as trunk;
- trunk port accidentally configured as access;
- required VLAN missing from trunk allowed list;
- native VLAN mismatch;
- router subinterface using the wrong VLAN ID;
- router subinterface missing an IP address.

A useful troubleshooting approach is to verify the VLAN locally before investigating Layer 3 routing.

---

## Skills Demonstrated

This section demonstrates:

- VLAN design;
- access-port configuration;
- IEEE 802.1Q trunking;
- allowed VLANs;
- native VLAN concepts;
- switch management VLAN;
- router-on-a-stick;
- inter-VLAN routing.
