# RT-ISP

## Role in the Topology

`RT-ISP` represents the simulated Internet Service Provider side of the Packet Tracer lab.

It is not intended to model a complete real ISP infrastructure. Instead, it creates a controlled external network that allows the enterprise to test:

- edge routing;
- default routing;
- NAT/PAT;
- communication with a simulated public server.

The complete startup configuration is available in:

[RT-ISP_startup-config.txt](RT-ISP_startup-config.txt)

---

## Device Information

| Property | Value |
|---|---|
| Hostname | `RT-ISP` |
| Platform reported by configuration | `CISCO2911/K9` |
| Cisco IOS version | `15.1` |
| Role | Simulated ISP router |
| Remote administration | SSH version 2 |
| Dynamic routing | Not configured |

---

## Interface Overview

| Interface | Purpose | IPv4 |
|---|---|---|
| `G0/0` | Link to enterprise / RT-HQ | `203.0.113.1/30` |
| `G0/1` | Simulated public network | `198.51.100.1/24` |
| `G0/2` | Unused | No IP |

The enterprise-facing link is:

```text
203.0.113.0/30
```

and the public simulation network is:

```text
198.51.100.0/24
```

The public server in the project uses:

```text
198.51.100.10
```

with `RT-ISP` acting as its gateway.

---

## Topology Position

Conceptually:

```text
Enterprise Networks
        |
      RT-HQ
        |
203.0.113.0/30
        |
      RT-ISP
        |
198.51.100.0/24
        |
   Public Server
```

This provides an external destination that can be reached without requiring real Internet access.

---

## Routing

The startup configuration contains:

```text
ip route 0.0.0.0 0.0.0.0 GigabitEthernet0/0
```

This defines a default route using the enterprise-facing interface.

For this simulated lab, it provides a simple routing behavior.

In a production Ethernet environment, a next-hop address is often preferable because it identifies the next router explicitly.

For example, a design could use a next-hop route when appropriate to the scenario.

The current README documents the configuration exactly as implemented rather than replacing it with a different design.

---

## No OSPF Participation

`RT-ISP` does not participate in the enterprise OSPF domain.

This is intentional for the lab architecture.

The internal enterprise routing protocol remains separated from the simulated provider network.

`RT-HQ` reaches the provider through its static default route.

---

## SSH Administration

The router is configured with:

```text
ip ssh version 2
ip domain-name ensa.local
login local
transport input ssh
```

This allows secure CLI administration in the simulated environment.

---

## IPv6 Status

The current startup configuration does not include IPv6 addressing or:

```text
ipv6 unicast-routing
```

This is important because `RT-HQ` currently has an IPv6 default route that points toward:

```text
2001:DB8:BBBB:1::1
```

For full IPv6 external connectivity, `RT-ISP` would also need an IPv6 configuration on the relevant interfaces.

Until that is implemented and verified, this router should be documented as an IPv4 ISP simulation.

---

## Management Features

Unlike the enterprise routers, the current `RT-ISP` startup configuration does not include the centralized:

- NTP server;
- Syslog destination;
- SNMP community.

This reflects its simpler provider-simulation role in the project.

---

## Useful Verification Commands

```text
show ip interface brief
show ip route
show running-config | include ip route
show ip ssh
ping 203.0.113.2
ping 198.51.100.10
```

---

## What RT-ISP Demonstrates

This router demonstrates:

- enterprise-to-provider connectivity;
- separation between internal routing and provider routing;
- simulated public addressing;
- edge-routing behavior;
- support for NAT/PAT validation;
- secure SSH administration.
