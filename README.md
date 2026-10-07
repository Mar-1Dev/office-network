# Project 1 — Office Network

Small-office network built in Cisco Packet Tracer: three departments on separate
VLANs, routed by a single router-on-a-stick, with DHCP and DNS. Portfolio piece
toward a network-focused internship, summer 2028.

## Topology

- **3 × Cisco 2960** — one switch per department, 20 PCs each (60 PCs total)
  - `Sales-SW` — VLAN 10
  - `Engineering-SW` — VLAN 20
  - `Admin-SW` — VLAN 30
- **1 × Cisco 2911 (`Router2`)** — cabled directly to all three switches
  (an early daisy-chain plan was dropped in favor of direct links)
- Switch↔router uplinks are **trunks**; PC ports are access ports
- **Server1** sits on Admin-SW in VLAN 99, static `192.168.20.194`, running DNS

## Addressing

One /24 split into four /26s — each department gets its own subnet:

| VLAN | Department | Subnet | Router subinterface | Gateway | PC lease range |
|---|---|---|---|---|---|
| 10 | Sales | 192.168.20.0/26 | G0/0.10 | 192.168.20.1 | .11–.62 |
| 20 | Engineering | 192.168.20.64/26 | G0/1.20 | 192.168.20.65 | .75–.126 |
| 30 | Admin | 192.168.20.128/26 | G0/2.30 | 192.168.20.129 | .139–.190 |
| 99 | Servers & Management | 192.168.20.192/26 | G0/2.99 | 192.168.20.193 | n/a (static) |

DHCP pools on Router2 hand out IP, mask, gateway, and the DNS server
(`192.168.20.194`) per VLAN.

## What was built

**Session 1** — Design, full Layer 2 build, router-on-a-stick, DHCP.
All 60 PCs picked up leases in their own subnets; inter-VLAN ping verified.

**Session 2** — DNS on Server1 and name-based verification.
`ping server1` resolves via DNS from every VLAN. Debugging along the way caught
two real faults: Server1 had no IP address configured, and its switch port was
sitting in the default VLAN instead of VLAN 99 — DNS was configured correctly
from the start, it just had nothing to talk to.

## Files

| File | Contents |
|---|---|
| `office-network.pkt` | The Packet Tracer project |
| `router2.cfg` | Router2 running-config (subinterfaces, DHCP pools) |
| `switch-sales.cfg` | Sales-SW running-config (VLAN 10) |
| `switch-eng.cfg` | Engineering-SW running-config (VLAN 20) |
| `switch-admin.cfg` | Admin-SW running-config (VLAN 30 + Server1 in VLAN 99) |

## Tools

Cisco Packet Tracer
