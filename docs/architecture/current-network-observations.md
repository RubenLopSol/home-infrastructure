# Current Network Observations

This document preserves historical network observations from the original
project context. The observations have not been revalidated during the context
refactor and must be treated as historical observation / pending revalidation.

## Historical Physical Topology Observation

Previously observed ISP and physical topology:

- Nokia G-010G-P appeared to be the ONT.
- Main router/gateway appeared to be Sagemcom F@ST 5657.
- Two ZTE ZXHN H3601 V9.1 devices were observed.
- Both ZTE devices appeared to operate in repeater mode.
- SSID observed: `MIWIFI_fMP7`.
- LAN observed: `192.168.1.0/24`.
- Gateway observed: `192.168.1.1`.
- No evidence of a second NAT/subnet was observed at that time.
- Both ZTEs appeared to use the same LAN.
- Wired Ethernet backhaul availability still required verification.

Do not assume exact physical mapping of the ZTE devices without verification.

## Historical Device Observations

Previously observed ZTE A:

- IP: `192.168.1.128`
- Base MAC: `04:20:84:36:D1:BF`
- 2.4 GHz BSSID: `04:20:84:36:D1:BF`
- 2.4 GHz channel: `6`
- 5 GHz BSSID: `04:20:84:36:D1:C0`
- 5 GHz channel: `64`

Previously observed ZTE B:

- IP: `192.168.1.129`
- Base MAC: `04:20:84:36:CC:BB`
- 2.4 GHz BSSID: `04:20:84:36:CC:BB`
- 2.4 GHz channel: `11`
- 5 GHz BSSID: `04:20:84:36:CC:BC`
- 5 GHz channel: `64`

Previously observed Sagemcom:

- Gateway: `192.168.1.1`
- Gateway MAC: `08:7B:12:DC:24:07`
- 2.4 GHz BSSID: `08:7B:12:DC:24:04`
- Channel: `12`

Previously observed Ubuntu client:

- Wi-Fi interface: `wlo1`
- IP: `192.168.1.172/24`
- MAC: `24:ee:9a:8b:e0:a7`
- Gateway: `192.168.1.1`
- Connection observed to ZTE B 5 GHz BSSID: `04:20:84:36:CC:BC`
- Signal during testing: approximately `-40/-41 dBm`
- Link rate under favorable conditions: approximately `866.7 Mbit/s`

Other previously observed devices:

- Samsung mobile: `192.168.1.137`
- Fire TV: `192.168.1.133`
- Additional clients: `192.168.1.134`, `192.168.1.138`, and others

## Historical Diagnostic Measurements

Previously tested successfully:

- Ubuntu to gateway ping: `0%` loss.
- Ubuntu to ZTE `.128` ping: `0%` loss.
- ARP to gateway succeeded.
- `nmap` host discovery found expected infrastructure/devices.
- ZTE management ports `80` and `443` were reachable.

Example measured results from the original context:

- 50 pings to `192.168.1.128`: `0%` loss, approximately `3.96 ms` average.
- 50 pings to `192.168.1.1`: `0%` loss, approximately `6.74 ms` average.

These results only proved basic IP connectivity at the time of testing. They do
not prove correct operation of multicast, broadcast, mDNS, SSDP, Miracast,
Wi-Fi Direct, discovery protocols, casting, or other L2-sensitive traffic.

## Historical Smart View / Casting Observation

Samsung Smart View mirroring to an old LG TV or Amazon Fire TV was observed to
be unstable after several minutes.

The same phone and equipment reportedly worked correctly in a previous
apartment.

In the current house at the time of observation, disabling the two repeaters
often made mirroring work correctly while the main router remained operational.

Historical working hypothesis:

```text
Repeater topology / wireless backhaul may be interfering with
multicast/discovery/bridging behavior required by Smart View/Miracast or
casting.
```

This was a hypothesis, not a proven root cause.

Important distinction from the original context:

- Streaming: Internet/server -> mobile.
- Mirroring: mobile -> local TV/Fire TV.

Server saturation should not normally directly terminate local mirroring,
although traffic/load may be a trigger.

Long-term preferred network characteristics from the original context:

- one stable LAN
- properly managed APs
- preferably Ethernet backhaul
- controlled multicast/discovery behavior
- VLAN-aware design where required
- no unnecessary wireless repeater chains

When VLANs are designed, mDNS, SSDP, casting, Home Assistant, and IoT discovery
requirements must be considered explicitly.
