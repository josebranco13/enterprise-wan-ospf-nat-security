# Addressing Plan

## Purpose

This folder documents the IPv4 and IPv6 addressing plan used throughout the enterprise topology.

A good addressing plan is important because every router interface, switch management interface, server, client network, WAN link, and external segment must use a consistent and non-overlapping network. In this project, the addressing scheme was designed so that each site and each type of traffic can be identified easily.

The addressing documentation acts as a reference for the rest of the repository. When reviewing routing, DHCP, NAT, security, or connectivity tests, the networks listed here explain what each address represents.

---

## IPv4 Addressing

### Headquarters

| Purpose | Network | Default Gateway |
|---|---|---|
| HQ Users | `10.30.10.0/24` | `10.30.10.1` |
| HQ Servers | `10.30.20.0/24` | `10.30.20.1` |
| HQ Management | `10.30.99.0/24` | `10.30.99.1` |

The headquarters uses separate networks for users, servers, and management. This makes the topology easier to manage and allows traffic-control policies to be applied according to the role of each device.

### Branch Offices

| Site | Network | Default Gateway |
|---|---|---|
| Branch 1 | `10.31.10.0/24` | `10.31.10.1` |
| Branch 2 | `10.32.10.0/24` | `10.32.10.1` |

Each branch has its own local network and reaches the headquarters through a point-to-point WAN connection.

### WAN Links

| Link | Network |
|---|---|
| HQ ↔ Branch 1 | `10.255.0.0/30` |
| HQ ↔ Branch 2 | `10.255.0.4/30` |

`/30` networks are appropriate for point-to-point IPv4 links because they provide two usable host addresses.

### ISP and Public Network

| Purpose | Network |
|---|---|
| HQ ↔ ISP | `203.0.113.0/30` |
| Simulated Public Network | `198.51.100.0/24` |

These documentation-oriented address ranges are used to simulate external connectivity inside Cisco Packet Tracer.

---

## Key Device Addresses

### Routers

| Device | Interface / Role | IPv4 Address |
|---|---|---|
| RT-HQ | HQ Users gateway | `10.30.10.1/24` |
| RT-HQ | HQ Servers gateway | `10.30.20.1/24` |
| RT-HQ | HQ Management gateway | `10.30.99.1/24` |
| RT-HQ | WAN to BR1 | `10.255.0.1/30` |
| RT-HQ | WAN to BR2 | `10.255.0.5/30` |
| RT-HQ | ISP-facing interface | `203.0.113.2/30` |
| RT-BR1 | Branch 1 gateway | `10.31.10.1/24` |
| RT-BR1 | WAN to HQ | `10.255.0.2/30` |
| RT-BR2 | Branch 2 gateway | `10.32.10.1/24` |
| RT-BR2 | WAN to HQ | `10.255.0.6/30` |
| RT-ISP | Link to HQ | `203.0.113.1/30` |
| RT-ISP | Public network gateway | `198.51.100.1/24` |

### Infrastructure and Servers

| Device | IPv4 Address |
|---|---|
| SW1-HQ management | `10.30.99.11/24` |
| SW2-HQ management | `10.30.99.12/24` |
| PC-NETADMIN | `10.30.99.10/24` |
| SRV-INTERNAL | `10.30.20.10/24` |
| SRV-PUBLIC | `198.51.100.10/24` |

---

## IPv6 Addressing

The project also includes IPv6 as an extension of the main IPv4 implementation.

### Internal Prefixes

| Segment | IPv6 Prefix |
|---|---|
| HQ Users | `2001:db8:aaaa:10::/64` |
| HQ Servers | `2001:db8:aaaa:20::/64` |
| HQ Management | `2001:db8:aaaa:99::/64` |
| Branch 1 | `2001:db8:aaaa:31::/64` |
| Branch 2 | `2001:db8:aaaa:32::/64` |
| HQ ↔ BR1 | `2001:db8:aaaa:ff10::/64` |
| HQ ↔ BR2 | `2001:db8:aaaa:ff20::/64` |

### External Prefixes

| Segment | IPv6 Prefix |
|---|---|
| HQ ↔ ISP | `2001:db8:bbbb:1::/64` |
| Simulated Public Network | `2001:db8:bbbb:2::/64` |

The `2001:db8::/32` block is reserved for documentation and examples, which makes it suitable for a simulated lab.

---

## Design Rationale

The addressing plan follows a predictable pattern:

- `10.30.x.x` identifies headquarters networks.
- `10.31.x.x` identifies Branch 1.
- `10.32.x.x` identifies Branch 2.
- `10.255.x.x` is used for internal WAN links.
- documentation ranges are used for the simulated external network.

This structure makes troubleshooting easier because the origin of an address can often be identified immediately from the prefix.

---

## What to Verify

Useful evidence for this folder includes:

```text
show ip interface brief
show ipv6 interface brief
ipconfig
```

Recommended screenshots:

```text
rt-hq-ip-interfaces.png
rt-br1-ip-interfaces.png
rt-br2-ip-interfaces.png
end-device-addressing.png
```

These images should make it possible to compare the implemented addresses with the documented plan.
