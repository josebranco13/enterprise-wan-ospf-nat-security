# RT-BR2

## Role in the Topology

`RT-BR2` is the router for Branch 2.

It connects the Branch 2 LAN to the headquarters over a dedicated WAN link and provides routing, DHCP relay, IPv6, and remote-management functions.

The complete startup configuration is available in:

[RT-BR2_startup-config.txt](RT-BR2_startup-config.txt)

---

## Device Information

| Property | Value |
|---|---|
| Hostname | `RT-BR2` |
| Platform reported by configuration | `CISCO2911/K9` |
| Cisco IOS version | `15.1` |
| Role | Branch 2 router |
| OSPF process | `10` |
| OSPF router ID | `3.3.3.3` |
| SSH | Version 2 |

---

## Interface Overview

| Interface | Purpose | IPv4 | IPv6 |
|---|---|---|---|
| `G0/0` | Branch 2 LAN | `10.32.10.1/24` | `2001:DB8:AAAA:32::1/64` |
| `S0/0/0` | WAN toward HQ | `10.255.0.6/30` | `2001:DB8:AAAA:FF20::2/64` |
| `G0/1` | No IP configured | None | — |
| `G0/2` | Unused | None | — |
| `S0/0/1` | Unused | None | — |

---

## Branch LAN

The Branch 2 IPv4 network is:

```text
10.32.10.0/24
```

with default gateway:

```text
10.32.10.1
```

The IPv6 LAN prefix is:

```text
2001:DB8:AAAA:32::/64
```

---

## DHCP Relay

The LAN-facing interface uses:

```text
ip helper-address 10.30.20.10
```

This forwards DHCP requests to the centralized internal server at headquarters.

As a result, Branch 2 does not need its own dedicated DHCP server.

---

## WAN Connection

The Branch 2 WAN interface uses:

```text
IPv4: 10.255.0.6/30
IPv6: 2001:DB8:AAAA:FF20::2/64
```

This interface connects toward the headquarters side of the `10.255.0.4/30` WAN network.

---

## OSPFv2

The OSPF configuration uses:

```text
router ospf 10
router-id 3.3.3.3
```

The router advertises:

```text
10.32.10.0/24
10.255.0.4/30
```

The configuration follows:

```text
passive-interface default
no passive-interface Serial0/0/0
```

The LAN is advertised without attempting to establish neighbor relationships with end devices, while the serial WAN interface forms OSPF adjacency with `RT-HQ`.

---

## OSPFv3

OSPFv3 is configured for IPv6 with the same router ID:

```text
3.3.3.3
```

The Branch 2 LAN and WAN interface participate in OSPFv3.

Only the WAN serial interface is configured as non-passive.

---

## Default Routing

No static IPv4 default route is configured locally.

Branch 2 is expected to learn the default route dynamically from `RT-HQ` through OSPF.

This keeps ISP exit routing centralized at headquarters.

---

## SSH and Management Services

SSH version 2 is enabled.

The router also points to the HQ server for:

```text
Syslog → 10.30.20.10
NTP    → 10.30.20.10
```

A read-only SNMP community is configured for monitoring.

---

## Configuration Review Note

`GigabitEthernet0/1` currently has no IP address but is not explicitly administratively shut down in the startup configuration.

If this interface is not used in the topology, a cleaner hardening configuration would be:

```text
interface GigabitEthernet0/1
 shutdown
```

This is not required for the current routing design to function, but disabling unused interfaces is a good operational practice.

---

## Useful Verification Commands

```text
show ip interface brief
show ipv6 interface brief
show ip ospf neighbor
show ipv6 ospf neighbor
show ip route
show ipv6 route
show ip protocols
show ip ssh
```

---

## What RT-BR2 Demonstrates

This device demonstrates:

- branch LAN routing;
- DHCP relay;
- dual-stack IPv4/IPv6;
- point-to-point WAN connectivity;
- OSPFv2;
- OSPFv3;
- centralized default-route learning;
- SSH;
- NTP;
- Syslog;
- SNMP.
