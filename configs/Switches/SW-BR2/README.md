# SW-BR2

## Role in the Topology

`SW-BR2` is the Layer 2 access switch for Branch 2.

It connects branch devices to the local `10.32.10.0/24` network and provides an IP address for switch management.

The complete startup configuration is available in:

[SW-BR2_startup-config.txt](SW-BR2_startup-config.txt)

---

## Device Information

| Property | Value |
|---|---|
| Hostname | `SW-BR2` |
| Cisco IOS version | `15.0` |
| Role | Branch 2 Layer 2 access switch |
| FastEthernet interfaces visible in config | `24` |
| GigabitEthernet interfaces visible in config | `2` |
| Spanning Tree mode | PVST |
| Management SVI | VLAN 1 |
| Management IP | `10.32.10.2/24` |
| Default gateway | `10.32.10.1` |
| SSH | Version 2 |

---

## Layer 2 Design

No explicit access VLAN or trunk configuration appears on the physical ports.

The switch therefore uses default Layer 2 port behavior in this simple branch LAN.

Branch 2 does not require the same VLAN segmentation used at headquarters.

---

## Management SVI

The management interface is:

```text
interface Vlan1
 ip address 10.32.10.2 255.255.255.0
```

The switch uses:

```text
ip default-gateway 10.32.10.1
```

The gateway address belongs to `RT-BR2`.

This allows management traffic to leave the local subnet and reach other enterprise networks.

---

## Spanning Tree

PVST is enabled:

```text
spanning-tree mode pvst
```

This provides normal Layer 2 loop prevention.

---

## SSH

The switch supports SSH version 2 with local authentication.

Relevant configuration includes:

```text
ip ssh version 2
ip domain-name ensa.local
login local
transport input ssh
```

---

## VTY Configuration Review

VTY lines `0 4` are configured for SSH and local authentication.

VTY lines `5 15` only contain:

```text
login
```

For a consistent SSH-only policy, those lines should ideally match the secure configuration used on VTY `0 4`.

---

## Production Hardening Considerations

The current lab leaves most physical ports at default settings.

A production deployment would normally consider:

- explicit access-port mode;
- unused-port shutdown;
- unused VLAN assignment;
- port-security where appropriate;
- additional management-plane restrictions.

These are not required to explain the current Packet Tracer implementation, but they represent logical next steps beyond the lab.

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

## What SW-BR2 Demonstrates

This switch demonstrates:

- branch Layer 2 connectivity;
- management SVI configuration;
- default-gateway configuration on a Layer 2 switch;
- PVST;
- SSH administration.
