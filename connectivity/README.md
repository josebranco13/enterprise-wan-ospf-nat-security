# Connectivity Validation

## Purpose

This folder contains end-to-end tests used to prove that the main parts of the enterprise network can communicate correctly.

Connectivity testing is an important part of the project because a valid configuration is not enough by itself. The final network must also be tested from the perspective of the devices that actually use it.

---

## What Is Being Validated?

The tests in this folder verify communication between:

- Branch 1 and headquarters users;
- Branch 1 and the internal server;
- Branch 2 and the internal server;
- Branch 1 and Branch 2;
- headquarters and the simulated public network.

Together, these tests demonstrate that VLAN routing, WAN routing, OSPF, default routing, DHCP, and other supporting components are working as expected.

---

## Recommended Validation Tests

### Branch 1 to Headquarters User

From a Branch 1 client:

```text
ping <HQ-user-IP>
```

This proves that a remote branch can reach a user network located at headquarters.

Recommended evidence:

```text
br1-to-hq-user.png
```

### Branch 1 to Internal Server

From a Branch 1 client:

```text
ping 10.30.20.10
```

This validates communication between a remote branch and the centralized internal server.

Recommended evidence:

```text
br1-to-internal-server.png
```

### Branch 2 to Internal Server

From a Branch 2 client:

```text
ping 10.30.20.10
```

Recommended evidence:

```text
br2-to-internal-server.png
```

### Branch 1 to Branch 2

Use the actual DHCP address assigned to the destination client.

For example:

```text
ping <Branch-2-client-IP>
```

Recommended evidence:

```text
br1-to-br2.png
```

This test is particularly useful because traffic must cross multiple routing devices before reaching the destination.

### Headquarters to Simulated Public Server

From a headquarters client:

```text
ping 198.51.100.10
```

Recommended evidence:

```text
hq-to-public-server.png
```

This validates reachability between the enterprise network and the simulated public segment.

---

## How to Read a Ping Test

A successful ping shows that ICMP Echo Request packets reached the destination and that Echo Reply packets returned to the source.

Example:

```text
Reply from 10.30.20.10: bytes=32 time<1ms TTL=...
```

A failed ping does not automatically identify the cause. Possible causes include:

- incorrect IP addressing;
- incorrect default gateway;
- interface down;
- missing VLAN assignment;
- trunking problem;
- missing route;
- OSPF adjacency failure;
- ACL blocking traffic;
- NAT problem;
- destination host misconfiguration.

This is why connectivity testing is used together with the verification commands documented in the other folders.

---

## Useful Supporting Commands

When a test fails, useful commands include:

```text
ipconfig
show ip interface brief
show ip route
show ip ospf neighbor
show vlan brief
show interfaces trunk
show access-lists
show ip nat translations
```

The exact command depends on where the failure is occurring.

---

## Evidence Strategy

For GitHub, a small number of meaningful screenshots is better than many repetitive screenshots.

Each image should clearly show:

1. the source device;
2. the destination being tested;
3. the command;
4. the result.

Avoid screenshots where the important output is cut off.

---

## Why This Matters

For a non-networking reviewer, these screenshots answer a simple question:

> Can devices located in different parts of the simulated company actually communicate?

For a networking reviewer, the same evidence confirms that the underlying switching, routing, addressing, and policy configuration is operating as intended.
