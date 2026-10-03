# IPv6 Implementation

## Purpose

This folder documents the IPv6 extension of the enterprise topology.

The original network is primarily validated with IPv4. IPv6 was added to demonstrate dual-stack addressing and dynamic IPv6 routing across the same headquarters and branch topology.

The IPv6 implementation uses documentation prefixes so that the lab remains clearly separated from real Internet addressing.

---

## Addressing Strategy

The internal enterprise network uses prefixes from:

```text
2001:db8:aaaa::/48
```

The simulated external side uses:

```text
2001:db8:bbbb::/48
```

### Internal Networks

```text
HQ Users      2001:db8:aaaa:10::/64
HQ Servers    2001:db8:aaaa:20::/64
HQ Management 2001:db8:aaaa:99::/64
Branch 1      2001:db8:aaaa:31::/64
Branch 2      2001:db8:aaaa:32::/64
HQ ↔ BR1      2001:db8:aaaa:ff10::/64
HQ ↔ BR2      2001:db8:aaaa:ff20::/64
```

### External Networks

```text
HQ ↔ ISP      2001:db8:bbbb:1::/64
Public side   2001:db8:bbbb:2::/64
```

---

## IPv6 Routing

IPv6 forwarding is enabled on the routers with:

```text
ipv6 unicast-routing
```

OSPFv3 is used to dynamically exchange the internal IPv6 routes.

The project uses OSPF process `10`, matching the logical organization used in the IPv4 implementation.

Router IDs:

```text
RT-HQ  → 1.1.1.1
RT-BR1 → 2.2.2.2
RT-BR2 → 3.3.3.3
```

WAN serial interfaces participate actively in OSPFv3, while LAN-facing interfaces can be kept passive so that the networks are advertised without attempting to establish unnecessary OSPF neighbor relationships with end devices.

---

## Example OSPFv3 Interface Activation

An interface is associated with OSPFv3 using:

```text
ipv6 ospf 10 area 0
```

This is applied to the relevant LAN and WAN interfaces.

---

## IPv6 Default Route

The headquarters can use the ISP-facing IPv6 address as the next hop for a default route:

```text
ipv6 route ::/0 2001:db8:bbbb:1::1
```

The default route can then be advertised to the internal OSPFv3 domain.

---

## SLAAC

IPv6 client devices can use Stateless Address Autoconfiguration.

Routers advertise the local `/64` prefix, allowing clients to build their own global unicast addresses automatically.

This is different from the centralized IPv4 DHCP design used elsewhere in the project.

---

## Verification

Useful commands include:

```text
show ipv6 interface brief
show ipv6 route
show ipv6 ospf neighbor
```

Useful connectivity tests include:

```text
ping <IPv6-address>
```

The most important checks are:

- each router has the expected IPv6 addresses;
- HQ establishes OSPFv3 adjacency with both branches;
- branches learn remote IPv6 networks;
- IPv6 traffic can cross the WAN links.

---

## Recommended Evidence

```text
ipv6-interfaces-rt-hq.png
ospfv3-neighbors-rt-hq.png
ospfv3-routes-br1.png
ipv6-interbranch-ping.png
```

---

## Important Packet Tracer Note

IPv6 features in Packet Tracer may not behave exactly like a physical IOS environment, depending on the simulated device model and Packet Tracer version.

The repository therefore focuses on the commands and behavior that can be validated consistently in the lab.

---

## Skills Demonstrated

This part of the project demonstrates:

- IPv6 address planning;
- IPv6 interface configuration;
- dual-stack network design;
- OSPFv3;
- IPv6 default routing;
- SLAAC;
- IPv6 route verification.
