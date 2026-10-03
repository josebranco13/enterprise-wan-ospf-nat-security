# RT-HQ

## Role in the Topology

`RT-HQ` is the central router of the enterprise network.

It connects the headquarters LANs, both remote branches, and the simulated ISP. Because of this position, it is responsible for several core functions at the same time:

- inter-VLAN routing at headquarters;
- IPv4 and IPv6 routing;
- OSPFv2 and OSPFv3;
- DHCP relay for HQ users;
- NAT/PAT at the enterprise edge;
- default routing toward the ISP;
- management-network traffic filtering;
- SSH administration;
- NTP, Syslog, and SNMP integration.

The complete startup configuration is available in:

[RT-HQ_startup-config.txt](RT-HQ_startup-config.txt)

---

## Device Information

| Property | Value |
|---|---|
| Hostname | `RT-HQ` |
| Platform reported by configuration | `CISCO2911/K9` |
| Cisco IOS version | `15.1` |
| Main role | Headquarters / enterprise edge router |
| IPv4 routing protocol | OSPFv2 |
| IPv6 routing protocol | OSPFv3 |
| OSPF process | `10` |
| OSPF router ID | `1.1.1.1` |
| Remote administration | SSH version 2 |

The Cisco 2911 is used here as a multi-role enterprise router capable of combining LAN routing, WAN connectivity, security policies, and network services in the same simulated device.

---

## Interface Overview

| Interface | Purpose | IPv4 | IPv6 | NAT Role |
|---|---|---|---|---|
| `G0/0` | Physical 802.1Q parent interface | No IP | — | — |
| `G0/0.10` | HQ Users / VLAN 10 | `10.30.10.1/24` | `2001:DB8:AAAA:10::1/64` | Inside |
| `G0/0.20` | HQ Servers / VLAN 20 | `10.30.20.1/24` | `2001:DB8:AAAA:20::1/64` | Inside |
| `G0/0.99` | HQ Management / VLAN 99 | `10.30.99.1/24` | `2001:DB8:AAAA:99::1/64` | Inside |
| `G0/1` | Link toward ISP | `203.0.113.2/30` | `2001:DB8:BBBB:1::2/64` | Outside |
| `S0/0/0` | WAN to Branch 1 | `10.255.0.1/30` | `2001:DB8:AAAA:FF10::1/64` | Inside |
| `S0/0/1` | WAN to Branch 2 | `10.255.0.5/30` | `2001:DB8:AAAA:FF20::1/64` | Inside |

---

## Router-on-a-Stick

The physical interface `GigabitEthernet0/0` does not have an IP address directly assigned to it.

Instead, three 802.1Q subinterfaces are used:

```text
G0/0.10 → VLAN 10 → HQ Users
G0/0.20 → VLAN 20 → HQ Servers
G0/0.99 → VLAN 99 → HQ Management
```

Each subinterface acts as the default gateway for its VLAN.

Example:

```text
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.30.10.1 255.255.255.0
```

This design allows multiple logical LANs to share one physical router interface while remaining separated at Layer 2.

---

## DHCP Relay

The HQ user subinterface contains:

```text
ip helper-address 10.30.20.10
```

The DHCP server is located in the server network at `10.30.20.10`.

Because DHCP clients initially use broadcast traffic, the router must relay these requests between VLANs.

This allows HQ users to obtain addressing information from the centralized server without placing the DHCP server directly inside VLAN 10.

---

## OSPFv2

The router uses OSPF process `10` with router ID:

```text
1.1.1.1
```

The main IPv4 networks advertised are:

```text
10.30.10.0/24
10.30.20.0/24
10.30.99.0/24
10.255.0.0/30
10.255.0.4/30
```

The configuration follows the pattern:

```text
passive-interface default
no passive-interface Serial0/0/0
no passive-interface Serial0/0/1
```

This means that the LAN networks are advertised into OSPF, but OSPF Hello packets are only sent over the WAN links where router adjacencies are actually required.

`RT-HQ` should establish OSPF adjacency with:

```text
RT-BR1 → Router ID 2.2.2.2
RT-BR2 → Router ID 3.3.3.3
```

---

## IPv4 Default Route

The enterprise default route is:

```text
ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

This sends unknown IPv4 destinations toward `RT-ISP`.

OSPF also contains:

```text
default-information originate
```

so the default route can be propagated to the branch routers.

As a result, Branch 1 and Branch 2 do not require their own manually configured Internet-facing default routes.

---

## OSPFv3 and IPv6

IPv6 routing is enabled on the device.

OSPFv3 also uses process `10` and router ID:

```text
1.1.1.1
```

The LAN subinterfaces and both WAN serial interfaces participate in OSPFv3.

As with OSPFv2, the serial links are the interfaces allowed to establish OSPF neighbor relationships.

The configured IPv6 default route is:

```text
ipv6 route ::/0 2001:DB8:BBBB:1::1
```

and OSPFv3 is configured to originate a default route.

### Current configuration note

The current `RT-ISP` startup configuration does not contain IPv6 addressing. Therefore, the enterprise-side IPv6 configuration exists on `RT-HQ`, but full IPv6 reachability through the simulated ISP should only be claimed after IPv6 is also configured and verified on `RT-ISP`.

---

## NAT and PAT

`RT-HQ` is the NAT boundary between the enterprise and the simulated external network.

The ISP-facing interface is configured as:

```text
ip nat outside
```

while the internal VLANs and branch WAN interfaces are configured as:

```text
ip nat inside
```

PAT is configured with:

```text
ip nat inside source list 1 interface GigabitEthernet0/1 overload
```

This allows multiple internal devices to share the external IPv4 address of `G0/1`.

### Current NAT ACL

The current startup configuration contains these NAT-selection entries:

```text
access-list 1 permit 10.31.10.0 0.0.0.255
access-list 1 permit 10.32.10.0 0.0.0.255
```

This includes Branch 1 and Branch 2.

However, the headquarters range `10.30.0.0/16` is not present in the current startup file.

If HQ networks are intended to use PAT as well, the intended configuration should also include:

```text
access-list 1 permit 10.30.0.0 0.0.255.255
```

This should be verified before treating the startup file as the final published configuration.

---

## Management ACL

The router contains the extended ACL:

```text
PROTECT-MANAGMENT
```

Its configured logic is:

```text
permit tcp host 10.30.99.10 any eq 22
permit tcp host 10.30.99.10 any eq 443
permit icmp host 10.30.99.10 any
deny ip any 10.30.99.0 0.0.0.255
permit ip any any
```

The ACL protects the management network while allowing unrelated traffic to continue.

The authorized management workstation is:

```text
PC-NETADMIN → 10.30.99.10
```

### Naming note

`PROTECT-MANAGMENT` is the exact name used in the current configuration.

For presentation consistency, it could be renamed to:

```text
PROTECT-MANAGEMENT
```

The spelling does not affect functionality, but the corrected name is clearer for documentation.

### Management-plane note

An interface ACL protecting `10.30.99.0/24` is not exactly the same as restricting all SSH access to one source host.

If the requirement is strictly:

> Only PC-NETADMIN may open SSH sessions to the network devices

then a VTY `access-class` can be used in addition to the interface ACL.

---

## SSH and Local Administration

The router is configured for SSH version 2.

Relevant elements include:

```text
ip ssh version 2
ip domain-name ensa.local
login local
transport input ssh
```

The VTY lines use the local username database for authentication.

A five-minute idle timeout is configured.

---

## Network Management Services

`RT-HQ` sends management information to the internal server.

### Syslog

```text
logging 10.30.20.10
```

### NTP

```text
ntp server 10.30.20.10
```

### SNMP

A read-only SNMP community is configured for monitoring.

For a public GitHub repository, configuration secrets and reusable credentials should not be reproduced in documentation.

---

## Useful Verification Commands

```text
show ip interface brief
show ipv6 interface brief
show ip ospf neighbor
show ipv6 ospf neighbor
show ip route
show ipv6 route
show ip nat translations
show ip nat statistics
show access-lists
show ip ssh
show clock
show ntp associations
```

---

## Key Skills Demonstrated by RT-HQ

This device combines the largest number of technologies in the project:

- router-on-a-stick;
- inter-VLAN routing;
- IPv4 and IPv6;
- OSPFv2;
- OSPFv3;
- DHCP relay;
- NAT/PAT;
- default-route propagation;
- extended ACLs;
- SSH;
- NTP;
- Syslog;
- SNMP;
- WAN routing.

Because of this, `RT-HQ` is the central technical component of the entire lab.
