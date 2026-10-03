# Troubleshooting Scenarios

## Purpose

This folder documents deliberate failures introduced into an otherwise functional network.

The objective is to demonstrate a structured troubleshooting methodology rather than only the ability to configure a working topology.

Each scenario follows the same process:

```text
1. Confirm the network is working
2. Introduce one controlled fault
3. Observe the symptoms
4. Collect diagnostic evidence
5. Identify the root cause
6. Correct the configuration
7. Verify that normal operation has returned
```

Only one fault should be introduced at a time.

---

# Incident 1 — OSPF Area Mismatch

## Normal State

`RT-HQ` should have full OSPF adjacency with both branch routers.

Verification:

```text
show ip ospf neighbor
```

Expected neighbors include router IDs:

```text
2.2.2.2
3.3.3.3
```

with the adjacency in the `FULL` state.

## Fault Introduced

On `RT-BR1`, the HQ-BR1 WAN network is temporarily moved from OSPF Area 0 to Area 1.

Conceptually:

```text
RT-HQ                 RT-BR1
Area 0                Area 1
  |                      |
  +------ WAN link ------+
          mismatch
```

OSPF neighbors on the same link must agree on the area.

## Symptoms

The Branch 1 OSPF neighbor relationship disappears.

Branch 2 should remain unaffected.

## Diagnosis

Useful commands:

```text
show ip ospf neighbor
show ip protocols
show ip route ospf
```

The results help isolate the problem to the OSPF configuration of the HQ-BR1 link.

## Resolution

Return the WAN network on `RT-BR1` to Area 0.

Verify that the neighbor relationship returns to `FULL`.

## Evidence

```text
01-ospf-area-mismatch/
├── before.png
├── failure.png
├── diagnosis.png
└── fixed.png
```

---

# Incident 2 — Incorrect Passive Interface

## Normal State

Both branch routers should appear as full OSPF neighbors on `RT-HQ`.

## Fault Introduced

The HQ interface toward Branch 1 is temporarily configured as passive.

A passive OSPF interface does not send Hello packets.

## Symptoms

The OSPF adjacency with Branch 1 is lost.

The adjacency with Branch 2 should remain operational.

## Diagnosis

Useful commands:

```text
show ip ospf neighbor
show ip protocols
```

`show ip protocols` can reveal that the WAN interface was incorrectly placed in the passive-interface list.

## Resolution

Remove the passive setting from the Branch 1 WAN interface.

Verify that the adjacency returns to `FULL`.

## Evidence

```text
02-passive-interface/
├── before.png
├── failure.png
└── fixed.png
```

---

# Incident 3 — Missing Default Route

## Normal State

`RT-HQ` has a static default route toward the ISP:

```text
0.0.0.0/0 → 203.0.113.1
```

The branch routers receive a default route through OSPF.

On a branch router, this may appear as:

```text
O*E2 0.0.0.0/0
```

## Fault Introduced

The static default route is temporarily removed from `RT-HQ`.

## Symptoms

The HQ router no longer has its ISP default route.

Because OSPF is configured to originate the default route based on the presence of that route, the branches also lose their OSPF-learned default route.

Internal OSPF routes may continue to work.

## Diagnosis

Useful commands:

```text
show ip route
show ip route ospf
show running-config | include ip route
```

The key observation is the absence of:

```text
S* 0.0.0.0/0
```

on the headquarters and the absence of:

```text
O*E2 0.0.0.0/0
```

on the branches.

## Resolution

Restore:

```text
ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

Wait for OSPF to propagate the change and verify that the branch default route returns.

## Evidence

```text
03-missing-default-route/
├── before.png
└── failure.png
```

---

# Incident 4 — ACL Implicit Deny

## Normal State

The management ACL ends with:

```text
permit ip any any
```

This explicitly allows traffic that was not meant to be blocked by the earlier management-security rules.

## Fault Introduced

The final permit statement is temporarily removed.

Every Cisco ACL has an implicit:

```text
deny ip any any
```

at the end, even though it is not normally displayed as a configured line.

## Symptoms

Traffic entering an interface where the ACL is applied may begin to fail if it does not match one of the earlier permit statements.

## Diagnosis

Useful command:

```text
show access-lists
```

The output shows that the explicit final permit has disappeared.

The missing permit explains why otherwise legitimate traffic is now reaching the ACL's implicit deny.

## Resolution

Restore:

```text
permit ip any any
```

and repeat the connectivity test.

## Evidence

```text
04-acl-implicit-deny/
├── before.png
├── failure.png
├── diagnosis.png
└── fixed.png
```

---

## Screenshot Strategy

For each incident:

### `before.png`

Proves that the relevant feature was working before the fault.

### `failure.png`

Shows the observable symptom.

### `diagnosis.png`

Shows the command output that reveals the root cause.

### `fixed.png`

Proves that normal operation was restored.

This structure makes each scenario understandable even to someone who does not have access to the Packet Tracer file.

---

## Troubleshooting Methodology

A useful general approach followed in this project is:

```text
Physical / Interface State
        ↓
Addressing
        ↓
Local Connectivity
        ↓
Routing / Neighbor State
        ↓
Routing Table
        ↓
Policies such as ACL / NAT
        ↓
End-to-End Test
```

The exact order can change depending on the symptoms, but the goal is always to narrow down the failure systematically instead of changing several configurations at once.

---

## Important Rule

Do not save the deliberately broken state as the final `project.pkt`.

After every incident:

1. restore the correct configuration;
2. verify the feature again;
3. save only the working topology.

The troubleshooting screenshots preserve the fault for documentation without leaving the main project in a broken state.

---

## Skills Demonstrated

The troubleshooting section demonstrates:

- baseline verification;
- controlled fault injection;
- symptom analysis;
- OSPF troubleshooting;
- route-table analysis;
- passive-interface troubleshooting;
- default-route troubleshooting;
- ACL troubleshooting;
- root-cause identification;
- post-fix validation.
