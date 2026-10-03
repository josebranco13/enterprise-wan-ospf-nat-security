# RT-BR1

## Role in the Topology

`RT-BR1` is the router for Branch 1.

Its purpose is to connect the Branch 1 LAN to the headquarters through a point-to-point WAN connection.

It also provides:

- the default gateway for Branch 1 clients;
- DHCP relay toward the centralized HQ server;
- OSPFv2 routing;
- OSPFv3 routing;
- IPv4 and IPv6 connectivity;
- SSH administration;
- NTP, Syslog, and SNMP management functions.

The complete startup configuration is available in:

[RT-BR1_startup-config.txt](RT-BR1_startup-config.txt)

---

## Device Information

| Property | Value |
|---|---|
| Hostname | `RT-BR1` |
| Platform reported by configuration | `CISCO2911/K9` |
| Cisco IOS version | `15.1` |
| Role | Branch 1 router |
| OSPF process | `10` |
| OSPF router ID | `2.2.2.2` |
| SSH | Version 2 |

---

## Interface Overview

| Interface | Purpose | IPv4 | IPv6 |
|---|---|---|---|
| `G0/0` | Branch 1 LAN | `10.31.10.1/24` | `2001:DB8:AAAA:31::1/64` |
| `S0/0/0` | WAN toward HQ | `10.255.0.2/30` | `2001:DB8:AAAA:FF10::2/64` |
| `G0/1` | Unused | No IP | — |
| `G0/2` | Unused | No IP | — |
| `S0/0/1` | Unused | No IP | — |

The LAN interface is the default gateway for Branch 1 hosts.

---

## Branch LAN

The local branch network is:

```text
10.31.10.0/24
```

with router gateway:

```text
10.31.10.1
```

The corresponding IPv6 prefix is:

```text
2001:DB8:AAAA:31::/64
```

---

## DHCP Relay

The Branch 1 LAN interface contains:

```text
ip helper-address 10.30.20.10
```

The DHCP server is located at headquarters.

This is required because DHCP discovery traffic begins as a local broadcast and would not normally cross the router.

The relay function allows Branch 1 clients to receive centralized addressing information from the HQ server.

---

## WAN Link

The WAN interface uses:

```text
10.255.0.2/30
```

for IPv4 and:

```text
2001:DB8:AAAA:FF10::2/64
```

for IPv6.

The serial interface also contains:

```text
clock rate 2000000
```

In the Packet Tracer lab, this indicates that this side of the serial connection is providing clocking for the link.

The actual DCE/DTE role should always be verified against the physical serial connection in the topology.

---

## OSPFv2

The router uses:

```text
router ospf 10
router-id 2.2.2.2
```

The advertised IPv4 networks are:

```text
10.31.10.0/24
10.255.0.0/30
```

The configuration uses:

```text
passive-interface default
no passive-interface Serial0/0/0
```

This means:

- the Branch 1 LAN is advertised into OSPF;
- the LAN does not attempt to form OSPF neighbors with PCs;
- the WAN interface is allowed to establish adjacency with `RT-HQ`.

---

## OSPFv3

The same design is repeated for IPv6.

OSPFv3 uses:

```text
router-id 2.2.2.2
```

The Branch 1 LAN and the WAN interface participate in OSPFv3.

Only the WAN interface is non-passive, because this is where the router-to-router adjacency must be formed.

---

## Default Routing

`RT-BR1` does not contain its own manually configured IPv4 default route in the startup configuration.

Instead, it is expected to learn the enterprise default route dynamically from `RT-HQ` through OSPF.

In a working state, this can appear in the routing table as an OSPF external default route.

This design centralizes Internet/ISP exit control at headquarters.

---

## SSH

The router uses SSH version 2 and a local user database.

Relevant configuration includes:

```text
ip ssh version 2
ip domain-name ensa.local
login local
transport input ssh
```

The VTY session timeout is configured for five minutes.

---

## Management Services

### Syslog

```text
logging 10.30.20.10
```

### NTP

```text
ntp server 10.30.20.10
```

### SNMP

A read-only SNMP community is configured.

These services are centralized at the internal HQ server.

---

## Security and Operational Settings

The configuration also includes:

```text
service password-encryption
banner motd
no ip domain-lookup
```

The message-of-the-day banner warns that access is restricted.

Unused routed interfaces are administratively shut down in the current configuration, which is a useful basic hardening measure.

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
show clock
```

---

## What RT-BR1 Demonstrates

`RT-BR1` demonstrates how a remote site can remain relatively simple while relying on centralized services at headquarters.

Its main technical functions are:

- branch gateway;
- centralized DHCP relay;
- point-to-point WAN routing;
- OSPFv2;
- OSPFv3;
- IPv4/IPv6 dual-stack operation;
- secure remote management;
- centralized monitoring and time synchronization.
