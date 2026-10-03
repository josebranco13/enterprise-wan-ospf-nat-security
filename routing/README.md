# Routing

## Purpose

This folder documents the IPv4 routing design used to connect the headquarters, Branch 1, and Branch 2.

The project uses OSPF as the internal dynamic routing protocol and a static default route at the headquarters for traffic toward the simulated ISP.

---

## Why Routing Is Required

Each site uses different IP networks.

A device in Branch 1 does not automatically know how to reach:

```text
10.30.10.0/24
10.30.20.0/24
10.30.99.0/24
10.32.10.0/24
```

Routers need routing information that tells them which next hop or outgoing interface should be used for remote networks.

Rather than manually creating a static route for every internal destination on every router, the project uses OSPF.

---

## OSPF Design

The OSPF configuration uses:

```text
OSPF process: 10
Area: 0
```

Router IDs:

```text
RT-HQ  → 1.1.1.1
RT-BR1 → 2.2.2.2
RT-BR2 → 3.3.3.3
```

The serial WAN links between headquarters and the branches establish OSPF neighbor relationships.

---

## Passive Interfaces

The configuration uses:

```text
passive-interface default
```

and then explicitly enables OSPF neighbor formation on the required WAN interfaces.

This is useful because LAN interfaces still advertise their networks into OSPF without sending OSPF Hello packets toward ordinary end devices.

Conceptually:

```text
LAN interface
Advertise network ✅
Form OSPF neighbors with PCs ❌

WAN interface
Advertise network ✅
Form OSPF router adjacency ✅
```

---

## Headquarters Networks

`RT-HQ` advertises the headquarters networks into OSPF.

These include the user, server, and management networks.

The branch routers therefore learn how to reach headquarters dynamically.

---

## Branch Networks

Each branch advertises its local LAN.

This allows:

- HQ to reach both branches;
- Branch 1 to reach Branch 2;
- Branch 2 to reach Branch 1.

The headquarters becomes the central routing point between the remote sites.

---

## Default Route

`RT-HQ` has a static default route toward the ISP:

```text
ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

Instead of configuring a separate default route manually on each branch, HQ can advertise the default route into OSPF.

The branch routers should then learn an OSPF external default route, typically displayed as:

```text
O*E2 0.0.0.0/0
```

This tells the branches:

> If no more specific route exists, send the traffic toward headquarters.

---

## OSPF Verification

The most important command is:

```text
show ip ospf neighbor
```

On `RT-HQ`, both branch routers should appear as OSPF neighbors in the `FULL` state.

This demonstrates that the OSPF adjacency has been successfully established.

---

## Routing Table Verification

Use:

```text
show ip route
show ip route ospf
```

Expected route types can include:

```text
C   Connected
L   Local
O   OSPF
O*E2 OSPF external default route
S*  Static default route
```

The exact output depends on the device being inspected.

---

## Recommended Evidence

```text
ospfv2-neighbors-rt-hq.png
ospfv2-routes-rt-hq.png
ospfv2-routes-rt-br1.png
ospfv2-routes-rt-br2.png
default-route-br1.png
```

---

## Relationship with Troubleshooting

Routing is also used in several deliberate troubleshooting scenarios.

Examples include:

- OSPF area mismatch;
- incorrect passive-interface configuration;
- missing default route.

These incidents demonstrate how a routing problem can be identified from neighbor tables, routing tables, protocol configuration, and connectivity symptoms.

See:

```text
../troubleshooting/
```

for the full troubleshooting evidence.

---

## Skills Demonstrated

This part of the project demonstrates:

- dynamic routing with OSPFv2;
- router IDs;
- OSPF Area 0;
- adjacency formation;
- passive interfaces;
- route advertisement;
- route-table analysis;
- static default routing;
- default-route propagation;
- routing troubleshooting.
