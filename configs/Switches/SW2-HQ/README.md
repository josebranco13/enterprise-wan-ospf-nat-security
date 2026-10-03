# SW2-HQ

## Role in the Topology

`SW2-HQ` is the second main Layer 2 switch at headquarters.

Its access ports are used for the server and management portions of the HQ network, while its GigabitEthernet interfaces operate as trunks.

The complete startup configuration is available in:

[SW2-HQ_startup-config.txt](SW2-HQ_startup-config.txt)

---

## Device Information

| Property | Value |
|---|---|
| Hostname | `SW2-HQ` |
| Cisco IOS version | `15.0` |
| Role | HQ Layer 2 switch |
| FastEthernet interfaces visible in config | `24` |
| GigabitEthernet interfaces visible in config | `2` |
| Spanning Tree mode | PVST |
| Management VLAN | VLAN 99 |
| Management IP | `10.30.99.12/24` |
| Default gateway | `10.30.99.1` |
| SSH | Version 2 |

The exact switch model is not identified by the startup configuration, so the documentation only describes properties directly supported by the file.

---

## Access Ports

Two access ports have explicit VLAN assignments.

### FastEthernet0/1

```text
switchport access vlan 20
switchport mode access
```

This places the connected device in the HQ server VLAN.

### FastEthernet0/2

```text
switchport access vlan 99
switchport mode access
```

This places the connected device in the HQ management VLAN.

In the project topology, these VLANs are used for the internal server environment and network administration.

---

## Trunk Links

Both GigabitEthernet interfaces are configured as trunks:

```text
Gi0/1
Gi0/2
```

They use:

```text
switchport trunk native vlan 999
switchport trunk allowed vlan 20,99
switchport mode trunk
```

Unlike `SW1-HQ`, these trunks do not list VLAN 10.

This reflects the VLANs required on this portion of the Layer 2 topology.

---

## VLAN Database Note

The startup configuration shows VLAN assignments and trunk references, but it does not show the VLAN database entries themselves.

To verify that VLANs 20, 99, and 999 exist on the switch, use:

```text
show vlan brief
```

---

## Management SVI

The switch management interface is:

```text
interface Vlan99
 ip address 10.30.99.12 255.255.255.0
```

with default gateway:

```text
10.30.99.1
```

This allows remote administration from the management network.

---

## Spanning Tree

The switch uses:

```text
spanning-tree mode pvst
```

This provides Layer 2 loop prevention on a per-VLAN basis.

---

## SSH Administration

SSH version 2 and local authentication are enabled.

The device uses:

```text
ip domain-name ensa.local
login local
transport input ssh
```

on VTY lines `0 4`.

---

## VTY Configuration Review

VTY lines `5 15` contain only:

```text
login
```

They do not contain the same:

```text
login local
transport input ssh
```

settings as lines `0 4`.

For a consistent management policy, all VTY lines should ideally follow the same SSH-only authentication configuration.

---

## Useful Verification Commands

```text
show vlan brief
show interfaces trunk
show interfaces status
show ip interface brief
show spanning-tree
show ip ssh
```

---

## What SW2-HQ Demonstrates

This switch demonstrates:

- access VLAN assignment;
- server/management segmentation;
- 802.1Q trunking;
- restricted trunk VLAN lists;
- dedicated native VLAN;
- management SVI;
- PVST;
- SSH administration.
