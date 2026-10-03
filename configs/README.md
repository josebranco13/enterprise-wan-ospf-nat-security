# Device Configurations

## Purpose

This folder contains the configuration material for the routers and switches used in the Cisco Packet Tracer project.

The purpose of keeping the configurations in the repository is to make the project reproducible and easier to review. A screenshot can prove that a command worked at a specific moment, but a configuration file provides a broader view of how the device was built.

---

## Network Devices

The topology contains the following main infrastructure devices:

```text
RT-HQ
RT-BR1
RT-BR2
RT-ISP

SW1-HQ
SW2-HQ
SW-BR1
SW-BR2
SW-ISP
```

The exact configuration of each device depends on its role.

For example:

- `RT-HQ` acts as the central enterprise router.
- `RT-BR1` and `RT-BR2` connect the branch LANs to the headquarters.
- `RT-ISP` represents the simulated service-provider side.
- `SW1-HQ` and `SW2-HQ` provide headquarters LAN connectivity.
- branch switches provide local access for branch devices.

---

## What the Configurations Cover

Across the project, the device configurations include areas such as:

- interface addressing;
- interface activation;
- VLAN access ports;
- trunk links;
- router-on-a-stick subinterfaces;
- OSPF routing;
- default routing;
- DHCP relay;
- NAT/PAT;
- ACLs;
- SSH;
- NTP;
- Syslog;
- SNMP;
- IPv6;
- OSPFv3.

Not every device uses every feature. Each configuration reflects the role of that device in the topology.

---

## Why Store Configuration Files?

Keeping device configurations in GitHub provides several benefits:

### Reproducibility

Another person can inspect how the topology was implemented rather than relying only on screenshots.

### Troubleshooting

Configuration files make it easier to compare a working state with a faulty state.

### Technical Review

A networking recruiter or engineer can inspect the actual Cisco IOS configuration and understand how the lab was implemented.

### Version Control

When configuration files are stored in Git, changes can be reviewed over time.

---

## Recommended File Naming

A clear naming convention is:

```text
RT-HQ-running-config.txt
RT-BR1-running-config.txt
RT-BR2-running-config.txt
RT-ISP-running-config.txt

SW1-HQ-running-config.txt
SW2-HQ-running-config.txt
SW-BR1-running-config.txt
SW-BR2-running-config.txt
SW-ISP-running-config.txt
```

If startup configurations are used instead:

```text
RT-HQ-startup-config.txt
...
```

The important point is to use the same convention consistently.

---

## Exporting a Configuration

Useful Cisco IOS commands include:

```text
show running-config
show startup-config
```

For the final repository, the configuration should represent the intended working state of the project, not one of the deliberately broken troubleshooting states.

---

## Security Note

Configuration exports can contain sensitive information in real environments.

For a portfolio repository, do not publish:

- real passwords;
- real production IP addresses;
- private company information;
- API keys;
- real SNMP secrets;
- SSH private keys.

This Packet Tracer project uses lab-only values, but the same security habit should be followed in real projects.

---

## Relationship with Other Folders

The configuration files show **what is configured**.

The other folders show **why it is configured and whether it works**.

For example:

```text
configs/       → complete device configuration
routing/       → routing explanation and evidence
security/      → ACL and management-access evidence
connectivity/  → end-to-end validation
troubleshooting/ → deliberate faults and diagnosis
```

This separation keeps the repository easier to navigate.
