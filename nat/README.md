# NAT and PAT

## Purpose

This folder documents the Network Address Translation configuration used at the headquarters edge.

The enterprise uses private IPv4 address ranges internally. NAT/PAT provides a mechanism for internal hosts to communicate with the simulated external network through the address assigned to the ISP-facing interface of `RT-HQ`.

---

## Internal Networks

The NAT policy includes the enterprise IPv4 networks:

```text
10.30.0.0/16
10.31.10.0/24
10.32.10.0/24
```

These networks represent headquarters and the two branches.

---

## Inside and Outside Interfaces

NAT distinguishes between two logical sides.

### Inside

Interfaces connected toward the enterprise network are considered NAT inside interfaces.

This includes the headquarters internal VLAN subinterfaces and the WAN links toward the branches where applicable to the configured design.

### Outside

The interface facing the ISP is the NAT outside interface.

For `RT-HQ`:

```text
GigabitEthernet0/1
```

connects toward the simulated ISP.

---

## PAT

The project uses Port Address Translation, also known as NAT overload.

Instead of assigning a different public IPv4 address to every internal device, multiple internal connections can share the ISP-facing address of `RT-HQ`.

Conceptually:

```text
10.30.10.x \
10.31.10.x  > → RT-HQ → 203.0.113.2 → external network
10.32.10.x /
```

The router distinguishes flows using transport-layer information.

---

## NAT ACL

A standard ACL identifies which source networks are eligible for translation.

The intended networks are:

```text
10.30.0.0/16
10.31.10.0/24
10.32.10.0/24
```

The ACL is not being used here as a security filter. Its role is to select traffic for NAT.

This distinction is important: ACLs can be used for different purposes depending on where and how they are referenced.

---

## Verification

Useful commands include:

```text
show ip nat translations
show ip nat statistics
```

`show ip nat translations` displays active translation entries.

`show ip nat statistics` provides information about the NAT configuration and the interfaces identified as inside and outside.

A connectivity test toward the simulated public server can be used together with these commands.

---

## Recommended Evidence

```text
nat-statistics.png
nat-translations.png
nat-public-server-ping.png
```

A strong evidence sequence is:

1. initiate traffic from an internal host;
2. verify that the destination is reachable;
3. immediately inspect the NAT translation table.

---

## Important Lab Observation

The simulated public server exists inside the Packet Tracer topology rather than on the real Internet.

Because of this, routing behavior in a simulation can sometimes allow a destination to remain reachable even when a NAT fault has been deliberately introduced.

For that reason, NAT verification should not rely only on `ping`.

The NAT table and NAT statistics provide more direct evidence that translation is occurring.

---

## Common NAT Problems

Typical issues include:

- incorrect NAT ACL;
- missing internal network from the NAT ACL;
- incorrect inside/outside assignment;
- missing overload configuration;
- wrong external interface;
- missing route toward the ISP;
- stale translations during troubleshooting.

Useful troubleshooting command:

```text
clear ip nat translation *
```

This should be used carefully and only in a lab or controlled environment.

---

## Skills Demonstrated

This section demonstrates:

- private IPv4 addressing;
- NAT inside/outside concepts;
- PAT / overload;
- traffic selection with an ACL;
- translation-table verification;
- edge-network troubleshooting.
