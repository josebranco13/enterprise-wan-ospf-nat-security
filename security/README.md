# Network Security

## Purpose

This folder documents the security controls used to protect network-management access and restrict traffic toward the management network.

The project focuses on two main areas:

- traffic filtering with an extended ACL;
- secure remote administration with SSH.

The goal is not to create a complete enterprise security architecture, but to demonstrate how network devices can enforce basic access rules.

---

## Management Network

The dedicated management network is:

```text
10.30.99.0/24
```

The network-administration workstation is:

```text
PC-NETADMIN
10.30.99.10
```

This device is intended to represent an authorized administrator.

---

## Extended ACL

The project uses an extended ACL named:

```text
PROTEGER-GESTAO
```

Its purpose is to control traffic associated with the management network.

The intended logic is:

```text
Allow authorized management traffic from PC-NETADMIN
Allow required ICMP from PC-NETADMIN
Block unauthorized access toward the management network
Allow unrelated traffic to continue normally
```

A representative rule set is:

```text
permit tcp host 10.30.99.10 any eq 22
permit tcp host 10.30.99.10 any eq 443
permit icmp host 10.30.99.10 any
deny ip any 10.30.99.0 0.0.0.255
permit ip any any
```

The final `permit ip any any` is important because every ACL has an implicit deny at the end.

Without an explicit final permit for unrelated traffic, traffic that does not match the earlier rules is denied.

---

## ACL Direction and Placement

An ACL only affects traffic on the interface and direction where it is applied.

For example:

```text
ip access-group PROTEGER-GESTAO in
```

means that the ACL evaluates traffic entering that interface.

Understanding direction is essential when troubleshooting ACL behavior.

---

## SSH

SSH provides encrypted remote CLI access to routers and switches.

The management workstation can be used to test administrative access:

```text
ssh -l <username> <device-ip>
```

Useful router-side verification commands include:

```text
show ip ssh
show users
show running-config | section line vty
```

---

## Important Security Design Note

An interface ACL that protects `10.30.99.0/24` is not automatically equivalent to restricting every possible SSH connection to the router itself.

If the design requirement is literally:

> Only PC-NETADMIN may initiate SSH sessions to network devices

then VTY-line access control should also be considered, for example with an `access-class` applied to the VTY lines.

This distinction is useful because it separates:

- data-plane ACL filtering;
- management-plane access control.

---

## Verification

Useful commands include:

```text
show access-lists
show access-lists PROTEGER-GESTAO
show ip ssh
show users
```

In Packet Tracer, some command variants may depend on the current privilege level.

If the router prompt shows:

```text
RT-HQ>
```

enter:

```text
enable
```

to reach:

```text
RT-HQ#
```

before running privileged verification commands.

---

## Recommended Evidence

```text
management-acl.png
netadmin-ssh-success.png
normal-user-ssh-denied.png
```

Only include `normal-user-ssh-denied.png` if that restriction has actually been implemented and verified.

Do not claim that an access attempt is blocked unless the test reproduces that behavior.

---

## Troubleshooting Connection

The project also includes an ACL troubleshooting incident where the final:

```text
permit ip any any
```

is temporarily removed.

This demonstrates the effect of the ACL's implicit deny rule.

See:

```text
../troubleshooting/
```

for the before, failure, diagnosis, and fixed evidence.

---

## Skills Demonstrated

This section demonstrates:

- extended ACLs;
- source/destination matching;
- TCP port matching;
- ACL ordering;
- implicit deny behavior;
- ACL direction;
- SSH;
- management-plane security concepts;
- security verification and troubleshooting.
