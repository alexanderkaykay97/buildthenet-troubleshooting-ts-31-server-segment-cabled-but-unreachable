# Troubleshooting #31: Server Segment Cabled but Unreachable

_Level: Easy — BuildTheNet 3D Sandbox troubleshooting — IP addressing_

**Status: Verified.** Completed on 2026-11-16; every requirement was machine-checked against the network built in BuildTheNet and passed.

Snapshot taken 2026-11-16. Built in a simulation — no physical or production equipment was involved.

## The ticket

**Reported symptom:** The office LAN can't reach the new FileServer, although Router-Edge's port to it shows up/up.

Helpdesk Ticket #131: A new server segment was cabled to Router-Edge GigabitEthernet0/1 yesterday and the port shows up/up, but nobody on the office LAN can reach FileServer (10.50.0.10). The server team configured FileServer with gateway 10.50.0.1, as the IP plan says.

_Area: IP addressing_

## Required resolution

Give FileServer's segment a working gateway on Router-Edge so PC-Acct reaches FileServer, following the IP plan.

## Checks

| # | Check | Status | Checked by |
|---|---|---|---|
| 1 | PC-Acct to FileServer works | Passed | Sandbox check |
| 2 | PC-Acct keeps its documented addressing | Passed | Sandbox check |
| 3 | FileServer keeps its documented addressing | Passed | Sandbox check |
| 4 | Router-Edge GigabitEthernet0/0 keeps its documented addressing | Passed | Sandbox check |

## Root cause

Router-Edge GigabitEthernet0/1 is enabled but carries no IP address, so the server subnet is not connected.

## The fix

What was changed to resolve the ticket, compared with the network as it was reported:

Compared with the starting topology, the learner changed the configuration of 2 devices.

**Router-Edge**

```diff
  interface GigabitEthernet0/1
+  ip address 10.50.0.1 255.255.255.0
```

**PC-Acct**

```diff
+ dns-server 10.50.0.1
```

## Result

Every check passed on this snapshot:

> Router-Edge owns the server segment's gateway address and PC-Acct reaches FileServer.

## Topology

![Topology diagram](topology.svg)

## Addressing

| Device | Interface | Address | Mask | Mode | Gateway |
|---|---|---|---|---|---|
| Router-Edge | GigabitEthernet0/0 | 192.168.1.1/24 | 255.255.255.0 | routed |  |
| Router-Edge | GigabitEthernet0/1 | 10.50.0.1/24 | 255.255.255.0 | routed |  |
| PC-Acct | FastEthernet0 | 192.168.1.20/24 | 255.255.255.0 | VLAN 1 | 192.168.1.1 |
| FileServer | FastEthernet0 | 10.50.0.10/24 | 255.255.255.0 | VLAN 1 | 10.50.0.1 |

## Device configurations

Running-configs as `show running-config` prints them in the BuildTheNet sandbox. Passwords, secrets, keys and SNMP communities are replaced with `<removed>`.

- [Router-Edge](configs/Router-Edge.txt)

---

Built on [BuildTheNet](https://www.buildthenet.io/sandbox)
