# SW-ISP

## Role in the Topology

`SW-ISP` is the Layer 2 switch used on the simulated public network.

It connects devices in the `198.51.100.0/24` segment behind `RT-ISP`.

The complete startup configuration is available in:

[SW-ISP_startup-config.txt](SW-ISP_startup-config.txt)

---

## Device Information

| Property | Value |
|---|---|
| Hostname | `SW-ISP` |
| Cisco IOS version | `15.0` |
| Role | Simulated public-network Layer 2 switch |
| FastEthernet interfaces visible in config | `24` |
| GigabitEthernet interfaces visible in config | `2` |
| Spanning Tree mode | PVST |
| Management SVI | VLAN 1 |
| Management IP | `198.51.100.2/24` |
| Default gateway | `198.51.100.1` |
| SSH | Version 2 |

The startup configuration does not identify a specific switch product model.

---

## Public-Side LAN

The switch belongs to the simulated public network:

```text
198.51.100.0/24
```

Its management IP is:

```text
198.51.100.2
```

while the router gateway is:

```text
198.51.100.1
```

The simulated public server also exists in this network.

---

## Layer 2 Port Configuration

The physical switch ports do not contain explicit VLAN or trunk configuration in the startup file.

This means the switch is being used as a simple Layer 2 access switch in the default VLAN.

That is sufficient for the single-subnet public-side simulation used in this project.

---

## Management SVI

The device uses:

```text
interface Vlan1
 ip address 198.51.100.2 255.255.255.0
```

with:

```text
ip default-gateway 198.51.100.1
```

This allows the switch itself to communicate beyond the local LAN through `RT-ISP`.

---

## Spanning Tree

PVST is enabled:

```text
spanning-tree mode pvst
```

This provides the standard Layer 2 loop-prevention mechanism.

---

## SSH

SSH version 2 is configured with a local user database.

Relevant commands include:

```text
ip ssh version 2
ip domain-name ensa.local
login local
transport input ssh
```

---

## VTY Configuration Review

VTY lines `0 4` use SSH-only local authentication.

VTY lines `5 15` contain only:

```text
login
```

For a consistent administrative policy, these lines should ideally be aligned with the `0 4` configuration.

---

## Security Context

This device exists in a simulated public network.

In a real production environment, management access to a public-side switch would require much stronger restrictions and would normally not rely only on basic SSH configuration.

The current design is appropriate for a Packet Tracer learning environment and should not be interpreted as a production Internet-edge security architecture.

---

## Useful Verification Commands

```text
show ip interface brief
show vlan brief
show interfaces status
show spanning-tree
show ip ssh
ping 198.51.100.1
```

---

## What SW-ISP Demonstrates

This switch demonstrates:

- simple Layer 2 public-network connectivity;
- switch management through an SVI;
- Layer 2 default-gateway configuration;
- PVST;
- SSH administration;
- support for a simulated external network used in NAT and routing tests.
