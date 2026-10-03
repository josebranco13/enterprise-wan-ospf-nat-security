# SW1-HQ

## Role in the Topology

`SW1-HQ` is one of the main Layer 2 switches at headquarters.

It provides access connectivity for HQ user devices and transports multiple VLANs over trunk links.

It also has its own management IP address in VLAN 99 and supports SSH administration.

The complete startup configuration is available in:

[SW1-HQ_startup-config.txt](SW1-HQ_startup-config.txt)

---

## Device Information

| Property | Value |
|---|---|
| Hostname | `SW1-HQ` |
| Cisco IOS version | `15.0` |
| Role | Headquarters Layer 2 access/distribution switch |
| FastEthernet interfaces visible in config | `24` |
| GigabitEthernet interfaces visible in config | `2` |
| Spanning Tree mode | PVST |
| Management VLAN | VLAN 99 |
| Management IP | `10.30.99.11/24` |
| Default gateway | `10.30.99.1` |
| SSH | Version 2 |

The startup configuration does not explicitly report a switch product ID, so this README does not claim a specific Catalyst model.

---

## Access Ports

The following ports are configured as VLAN 10 access ports:

```text
Fa0/1
Fa0/2
Fa0/3
Fa0/4
```

Each uses:

```text
switchport access vlan 10
switchport mode access
```

These ports belong to the HQ user network.

---

## Trunk Links

Both GigabitEthernet interfaces are configured as 802.1Q trunks:

```text
Gi0/1
Gi0/2
```

The trunk configuration includes:

```text
switchport trunk native vlan 999
switchport trunk allowed vlan 10,20,99
switchport mode trunk
```

The allowed VLAN list ensures that only the required production VLANs are carried across these links.

---

## Native VLAN 999

VLAN 999 is configured as the native VLAN for the trunks.

Using a separate native VLAN avoids using one of the normal user/server/management VLANs as the untagged trunk VLAN.

### VLAN database note

The startup configuration does not show the VLAN database definitions themselves.

On Cisco switches, VLAN information may be stored separately from the startup configuration.

Therefore, VLAN existence should be verified with:

```text
show vlan brief
```

rather than assuming from the startup file alone that a VLAN does or does not exist.

---

## Management SVI

The management interface is:

```text
interface Vlan99
 ip address 10.30.99.11 255.255.255.0
```

The switch uses:

```text
ip default-gateway 10.30.99.1
```

This allows the Layer 2 switch to communicate with management devices outside its local subnet.

---

## Spanning Tree

The switch uses:

```text
spanning-tree mode pvst
```

PVST maintains a separate spanning-tree instance per VLAN.

In this project, this provides standard Layer 2 loop prevention while the two HQ switches exchange VLAN traffic over trunk links.

---

## SSH Administration

SSH version 2 is enabled with:

```text
ip ssh version 2
ip domain-name ensa.local
login local
transport input ssh
```

The local user database is used for authentication.

---

## VTY Configuration Review

VTY lines `0 4` contain:

```text
exec-timeout 5 0
login local
transport input ssh
```

However, VTY lines `5 15` currently contain only:

```text
login
```

For a more consistent SSH-only management policy, the VTY configuration could be standardized across all available lines, for example:

```text
line vty 0 15
 exec-timeout 5 0
 login local
 transport input ssh
```

if supported by the Packet Tracer IOS image.

The README documents the current startup configuration and highlights this as a configuration-hardening opportunity.

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

## What SW1-HQ Demonstrates

This switch demonstrates:

- VLAN access-port assignment;
- 802.1Q trunking;
- allowed VLAN lists;
- dedicated native VLAN;
- management SVI;
- Layer 2 default gateway;
- PVST;
- SSH administration.
