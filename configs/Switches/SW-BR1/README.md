# SW-BR1

## Role in the Topology

`SW-BR1` is the Layer 2 access switch for Branch 1.

Its purpose is to connect branch end devices to the Branch 1 LAN and provide a management IP address for the switch itself.

The complete startup configuration is available in:

[SW-BR1_startup-config.txt](SW-BR1_startup-config.txt)

---

## Device Information

| Property | Value |
|---|---|
| Hostname | `SW-BR1` |
| Cisco IOS version | `15.0` |
| Role | Branch 1 Layer 2 access switch |
| FastEthernet interfaces visible in config | `24` |
| GigabitEthernet interfaces visible in config | `2` |
| Spanning Tree mode | PVST |
| Management SVI | VLAN 1 |
| Management IP | `10.31.10.2/24` |
| Default gateway | `10.31.10.1` |
| SSH | Version 2 |

The startup file does not identify a specific hardware model.

---

## Layer 2 Access Design

The physical FastEthernet and GigabitEthernet interfaces do not contain explicit VLAN or trunk configuration in the startup file.

Therefore, the switch is using the default Layer 2 behavior for those interfaces.

In this branch design, the LAN is intentionally simple and does not require the same multi-VLAN segmentation used at headquarters.

---

## Management Interface

The switch uses VLAN 1 as its management SVI:

```text
interface Vlan1
 ip address 10.31.10.2 255.255.255.0
```

Its default gateway is:

```text
ip default-gateway 10.31.10.1
```

`10.31.10.1` is the LAN address of `RT-BR1`.

This allows the switch to be managed from remote enterprise networks as long as routing and security policies permit it.

---

## Spanning Tree

The switch uses:

```text
spanning-tree mode pvst
```

Even though the branch topology is simple, spanning tree remains active as the normal Layer 2 loop-prevention mechanism.

---

## SSH

SSH version 2 is enabled.

The device uses:

```text
ip domain-name ensa.local
login local
transport input ssh
```

for VTY lines `0 4`.

---

## VTY Configuration Review

Lines `0 4` are configured with:

```text
exec-timeout 5 0
login local
transport input ssh
```

Lines `5 15` contain only:

```text
login
```

For consistency and stronger management security, all VTY lines should ideally use the same SSH-only local-authentication configuration.

---

## Operational Hardening Note

Most physical switch ports are left at their default configuration.

For a production environment, stronger hardening could include:

- explicitly configuring used ports as access ports;
- assigning them to the intended VLAN;
- shutting down unused ports;
- optionally placing unused ports in an unused VLAN.

The current configuration is appropriate to the simpler Packet Tracer branch design, but the distinction is worth documenting.

---

## Useful Verification Commands

```text
show ip interface brief
show vlan brief
show interfaces status
show spanning-tree
show ip ssh
```

---

## What SW-BR1 Demonstrates

This device demonstrates:

- basic Layer 2 branch access;
- switch management through an SVI;
- Layer 2 default-gateway configuration;
- PVST;
- SSH remote administration.
