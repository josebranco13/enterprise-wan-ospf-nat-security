# Network Management

## Purpose

This folder documents the management and monitoring functions configured in the lab.

A production network must provide more than connectivity. Administrators also need methods to access devices, synchronize time, collect logs, and retrieve operational information.

The management portion of the project introduces several of these functions in a simulated Cisco Packet Tracer environment.

---

## Management Network

The headquarters includes a dedicated management network:

```text
10.30.99.0/24
```

Important addresses include:

```text
RT-HQ management gateway → 10.30.99.1
PC-NETADMIN              → 10.30.99.10
SW1-HQ                   → 10.30.99.11
SW2-HQ                   → 10.30.99.12
```

Separating management traffic from normal user traffic provides a clearer administrative boundary and makes access control easier.

---

## SSH

SSH is used for remote command-line administration.

Compared with Telnet, SSH protects the management session by using encryption.

Typical requirements include:

- device hostname;
- domain name;
- local user account;
- RSA keys;
- SSH-enabled VTY lines;
- IP reachability between the administrator and the device.

A typical verification command is:

```text
show ip ssh
```

From the management workstation, a connection can be tested with:

```text
ssh -l <username> <device-ip>
```

The security folder contains the access-control side of this implementation.

---

## NTP

NTP is used to synchronize device clocks.

Correct time is important because logs and troubleshooting evidence are much more useful when events have consistent timestamps.

In this project, the internal server is used as the reference point for time synchronization.

Useful verification commands can include:

```text
show clock
show ntp associations
```

Availability of individual commands may depend on the Packet Tracer IOS implementation.

---

## Syslog

Syslog provides centralized event logging.

Instead of relying only on messages shown locally on each router or switch, network devices can send operational events to a central server.

Typical configuration includes a logging destination such as:

```text
logging host 10.30.20.10
```

The exact logging severity commands supported by Packet Tracer can vary by simulated IOS image.

For this reason, the main validation evidence should focus on whether messages are successfully received by the Syslog service.

---

## SNMP

SNMP is used to retrieve management information from network devices.

In this lab, a read-only community is used to demonstrate basic monitoring behavior.

A MIB Browser can query device information such as:

- system identity;
- interface information;
- operational values exposed by the simulated device.

Packet Tracer may support a limited MIB set compared with real Cisco IOS. When a numeric OID is not accepted manually, the Packet Tracer MIB tree can be used to select an available object.

---

## Recommended Evidence

```text
ntp-status.png
syslog-server.png
snmp-configuration.png
snmp-mib-browser.png
ssh-management.png
```

Only include evidence for features that were successfully validated in the final lab state.

---

## Why These Features Matter

For someone without a networking background, these services can be summarized as:

```text
SSH    → remotely administer a device
NTP    → keep device clocks synchronized
Syslog → collect event messages
SNMP   → retrieve monitoring information
```

Together, they represent some of the basic operational capabilities required to manage a network after it has been deployed.

---

## Packet Tracer Limitations

Cisco Packet Tracer is a learning simulator rather than a complete IOS emulator.

As a result:

- some IOS logging commands may be unavailable;
- some SNMP objects may not exist;
- some management behaviors may be simplified.

These limitations should be documented rather than hidden. The objective of the project is to demonstrate the networking concepts using the capabilities available in the simulator.
