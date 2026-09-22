# Current Network Inventory

This document records the currently verified network facts for the Home Lab
environment. Historical network observations remain in
`docs/architecture/current-network-observations.md` and should not be treated as
current truth unless revalidated here.

## Verified Host Network State

| Item | Value |
|---|---|
| Host | `homelab-server-01` |
| Interface | `enp1s0f1` |
| Interface altname | `enxf0761cbe7fd1` |
| MAC address | `f0:76:1c:be:7f:d1` |
| Addressing | DHCPv4 with router DHCP reservation |
| IPv4 | `192.168.1.181/24` |
| IPv6 link-local | `fe80::f276:1cff:febe:7fd1/64` |
| Gateway | `192.168.1.1` |
| DNS servers | `77.26.11.233`, `212.142.173.65` |
| DNS mode | `systemd-resolved` stub |
| Network manager | Netplan + systemd-networkd |
| Driver | `r8169` |
| NIC | Realtek RTL8111/8168/8211/8411 PCI Express Gigabit Ethernet |
| Link | 1 Gbps, full duplex |
| Link state | routable/configured/online |

No static IP has been configured on the server. The stable LAN address is provided by the router through DHCP reservation.


## Verified Router And DHCP State

| Item | Value |
|---|---|
| Router | Sagemcom F@st 5670Eth_EKT |
| Router/LAN address | `192.168.1.1` |
| LAN subnet | `192.168.1.0/24` |
| DHCP | enabled |
| DHCP server | `192.168.1.1` |
| DHCP pool | `192.168.1.128` - `192.168.1.254` |
| DHCP lease time | 86400 seconds |
| Current DNS from DHCP | `77.26.11.233`, `212.142.173.65` |

A DHCP reservation exists in the router:

| Item | Value |
|---|---|
| MAC | `f0:76:1c:be:7f:d1` |
| Reserved IPv4 | `192.168.1.181` |
| Router UI device name | `Add MAC Address` |
| Status | saved in router and validated after DHCP renewal |

The router firmware did not allow manually entering a friendly device name in
this workflow. This is acceptable because the functional binding is MAC to IPv4.

DHCP renewal validation was completed with:

```bash
sudo networkctl renew enp1s0f1
ip -4 addr show enp1s0f1
networkctl status enp1s0f1 --no-pager
```

After renewal, the server retained `192.168.1.181/24` and `networkctl` reported
`Address: 192.168.1.181 (DHCPv4 via 192.168.1.1)`.

## Verified Connectivity

| Target | Result | RTT avg |
|---|---|---:|
| `192.168.1.1` | 4/4 received, 0% packet loss | 4.281 ms |
| `1.1.1.1` | 4/4 received, 0% packet loss | 22.611 ms |
| `google.com` | 4/4 received, 0% packet loss | 38.486 ms |

Additional gateway check:

| Target | Result | RTT avg |
|---|---|---:|
| `192.168.1.1` | 2/2 received, 0% packet loss | 3.441 ms |

## Verified Neighbor Table

Observed from `homelab-server-01`:

| IP | MAC | Interface | State / note |
|---|---|---|---|
| `192.168.1.1` | `08:7b:12:dc:24:07` | `enp1s0f1` | gateway, reachable/delay |
| `192.168.1.140` | `e4:54:e8:3a:b9:3f` | `enp1s0f1` | workstation, reachable |

## Phase 2A Status

Phase 2A is complete for the currently available hardware. The current network
baseline documents the router, server LAN address, DHCP reservation, DNS,
gateway, and explicit limits of what has not been changed.

Phase 2B remains pending until future hardware purchases and decisions allow a
real design for segmentation, firewall policy, multicast/discovery behavior,
private access, and rollback.

## Current Network Decisions / Pending Work

- Keep the server on DHCP for now, with the router DHCP reservation providing a
  stable LAN address.
- Do not configure VLANs yet.
- Do not replace the router/firewall yet.
- Do not expose SSH or management services publicly.
- No changes were made in this phase to WAN firewall, port forwarding, DMZ,
  public exposure, VLANs, global DNS, DHCP pool, or router gateway address.
- Current available network hardware is limited to the ISP/router, existing
  repeaters, and `homelab-server-01`.
- Dedicated firewall/router, managed switch, NAS, UPS, and dedicated wired APs
  have not been purchased yet.
- Phase 2B remains pending:
  - Future dedicated router/firewall decision.
  - Future managed switch and wired AP decisions.
  - Future VLAN/firewall design and rollback plan.
  - Multicast/discovery and Smart View/casting behavior if still relevant.
