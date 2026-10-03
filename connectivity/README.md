# Connectivity Validation

## Purpose

This folder contains end-to-end connectivity tests performed after the network configuration was completed.

These tests are important because configuration commands alone do not prove that the complete network works. The screenshots below show communication between devices located in different parts of the enterprise topology.

---

## Branch 1 Connectivity

<p align="center">
  <img src="ping%20br1%20to%20others.png" alt="Branch 1 connectivity tests" width="850">
</p>

<p align="center">
  <em>Connectivity tests originating from Branch 1 toward other enterprise destinations.</em>
</p>

This evidence demonstrates that Branch 1 can reach remote networks through the WAN and the routing infrastructure.

Depending on the destinations included in the screenshot, this can validate communication toward headquarters resources, servers, or the second branch.

---

## Branch 2 Connectivity

<p align="center">
  <img src="ping%20br2%20to%20others.png" alt="Branch 2 connectivity tests" width="850">
</p>

<p align="center">
  <em>Connectivity tests originating from Branch 2 toward other enterprise destinations.</em>
</p>

This confirms that Branch 2 is also participating correctly in the routed enterprise network.

---

## Headquarters Connectivity

<p align="center">
  <img src="ping%20hq%20to%20others.png" alt="Headquarters connectivity tests" width="850">
</p>

<p align="center">
  <em>Connectivity tests originating from the headquarters network.</em>
</p>

The headquarters tests help confirm reachability toward remote branch networks and other configured destinations.

---

## What These Tests Validate

Successful end-to-end communication depends on several independent parts of the project working together:

```text
Correct addressing
      ↓
VLAN / LAN configuration
      ↓
Router interfaces
      ↓
OSPF routing
      ↓
Default routing where required
      ↓
ACL / NAT policies
      ↓
Destination host configuration
```

A successful ping therefore acts as final evidence that multiple layers of the implementation are functioning together.

---

## Troubleshooting

If a ping fails, the problem is not automatically related to routing.

Possible causes include:

- incorrect IP address;
- incorrect subnet mask;
- wrong default gateway;
- interface down;
- VLAN assignment error;
- trunking problem;
- missing OSPF route;
- ACL filtering;
- NAT configuration;
- destination device configuration.

For deliberate failure scenarios, see:

```text
../troubleshooting/
```
