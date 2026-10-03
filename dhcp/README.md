# DHCP and Centralized Address Assignment

## Purpose

This folder documents the use of DHCP to provide automatic IPv4 configuration to client devices.

Instead of manually assigning an IP address, subnet mask, default gateway, and DNS server to every workstation, DHCP allows these values to be delivered automatically.

In this project, the internal server at headquarters acts as the centralized DHCP service for multiple enterprise networks.

---

## Central DHCP Server

The internal server uses:

```text
IPv4 address: 10.30.20.10
Default gateway: 10.30.20.1
```

The server provides addressing information to clients in:

```text
HQ Users   → 10.30.10.0/24
Branch 1   → 10.31.10.0/24
Branch 2   → 10.32.10.0/24
```

The management network uses static addressing.

---

## DHCP Pools

The project uses client ranges based on the following plan:

```text
HQ Users:
10.30.10.100 - 10.30.10.199

Branch 1:
10.31.10.100 - 10.31.10.199

Branch 2:
10.32.10.100 - 10.32.10.199
```

Each pool provides the appropriate default gateway for its network.

The internal DNS server address used by the clients is:

```text
10.30.20.10
```

---

## Why DHCP Relay Is Required

DHCP begins with broadcast traffic.

Routers do not normally forward Layer 2 broadcasts between networks. Because the DHCP server is located at headquarters while clients also exist in other IP networks, the branch and headquarters router interfaces must relay the requests to the central server.

This is implemented with:

```text
ip helper-address 10.30.20.10
```

The relay agent receives the local DHCP broadcast and forwards it as routable traffic to the DHCP server.

---

## Example

Without DHCP relay:

```text
Branch PC
   |
DHCP Discover
   |
RT-BR1  X  broadcast is not routed
```

With DHCP relay:

```text
Branch PC
   |
DHCP Discover
   |
RT-BR1
   |
ip helper-address
   |
WAN / routed network
   |
SRV-INTERNAL
```

This allows a single centralized server to support several remote networks.

---

## Verification

On client devices:

```text
ipconfig
```

A correct result should show an address that belongs to the correct DHCP pool.

For example, an HQ client may receive:

```text
IP Address:      10.30.10.x
Subnet Mask:     255.255.255.0
Default Gateway: 10.30.10.1
DNS Server:      10.30.20.10
```

A Branch 1 client should receive a `10.31.10.x` address, while a Branch 2 client should receive a `10.32.10.x` address.

---

## Recommended Evidence

```text
srv-internal-dhcp-pools.png
hq-user-dhcp.png
br1-user-dhcp.png
br2-user-dhcp.png
```

The screenshots should show both the configured pools and examples of clients receiving valid addresses.

---

## Common DHCP Problems

Typical issues include:

- wrong DHCP pool network;
- incorrect default gateway in the pool;
- DHCP service disabled;
- missing `ip helper-address`;
- incorrect helper address;
- routing failure between relay and server;
- address conflict;
- client configured statically instead of DHCP.

When troubleshooting, first confirm that the client belongs to the expected network and that the router interface acting as gateway can reach the DHCP server.

---

## Skills Demonstrated

This part of the project demonstrates:

- centralized IPv4 address management;
- DHCP pool design;
- default gateway distribution;
- DNS option distribution;
- DHCP relay;
- troubleshooting across routed networks.
