# NWS UK Site: IP Address Redesign and VLSM Plan

IP addressing plan for the **United Kingdom site** of a four country enterprise network (Singapore, China, UK, US) built in Cisco Packet Tracer. The company was expanding, so each site needed a new 50 host branch LAN and non overlapping WAN addressing ahead of a site to site VPN rollout.

Group project of four for the Network Security module, Diploma in Cybersecurity & Digital Forensics, Temasek Polytechnic (February 2025). Each member owned one country site. **This repository covers only my part: the UK site.**

## The problem

- Add a new branch LAN (Site-X) at the UK office for up to 50 hosts
- Keep every UK range clear of the other three countries' ranges
- Readdress the UK links that connect to the shared WAN (Internet Cloud)

## New Site-X LAN

I reused an unallocated block from the existing UK address space.

| Item | Value |
|---|---|
| Requirement | 50 hosts |
| Smallest block that fits | 2^6 = 64 addresses (62 usable) |
| Prefix | /26 (255.255.255.192) |
| Network | 172.18.90.0/26 |
| Usable range | 172.18.90.1 to 172.18.90.62 |
| Broadcast | 172.18.90.63 |

A /27 gives only 30 usable hosts, so /26 is the smallest prefix that meets the requirement, with 12 addresses of headroom.

## WAN link to the Internet Cloud

The group split 159.240.0.0/28 into four /30 point to point links, one per country. The UK link is **159.240.0.8/30** (usable .9 and .10, broadcast .11).

## UK site addressing table

| Segment | Device | Interface | Network | Address |
|---|---|---|---|---|
| HQ outside | HQ perimeter router | G0/0 | 209.165.151.224/29 | 209.165.151.225 |
| | HQ firewall router | G0/0 | | 209.165.151.226 |
| HQ DMZ | HQ firewall router | G0/2 | 172.18.87.192/26 | 172.18.87.193 |
| | DMZ Web/DNS server | NIC | | 172.18.87.194 |
| HQ inside | HQ firewall router | G0/1 | 172.18.80.0/22 | 172.18.80.1 |
| | HQ switch | VLAN 1 | | 172.18.80.2 |
| HQ wireless | HQ wireless router | WLAN | 172.16.0.0/24 | DHCP from .100 |
| Branch1 LAN | MLS1 | VLAN 99 (mgmt) | 172.18.87.128/26 | 172.18.87.129 |
| | MLS1 | SVI VLAN 100 | 172.18.84.0/23 | 172.18.84.1 |
| | MLS1 | SVI VLAN 200 | 172.18.87.0/25 | 172.18.87.1 |
| Branch2 LAN | Branch2 router | G0/1.300 | 172.18.86.0/24 | 172.18.86.1 |
| | Branch2 router | G0/1.400 | 172.18.88.0/26 | 172.18.88.1 |
| | Branch2 router | G0/1.99 | 172.18.88.64/26 | 172.18.88.65 |
| | Branch2 SW1 | VLAN 99 / 300 / 400 | | 172.18.88.66 / 172.18.86.2 / 172.18.88.2 |
| | Branch2 SW2 | VLAN 99 / 300 / 400 | | 172.18.88.67 / 172.18.86.3 / 172.18.88.3 |
| | Admin laptop | NIC | 172.18.88.64/26 | 172.18.88.68 |
| | Internal server | NIC | 172.18.86.0/24 | 172.18.86.4 |
| Branch to Branch1 | Branch router / Branch1 router | G0/0 / G0/0 | 172.18.88.128/30 | .129 / .130 |
| Branch to Branch2 | Branch router / Branch2 router | G0/1 / G0/0 | 172.18.88.132/30 | .133 / .134 |
| Branch1 to Branch2 | Branch1 router / Branch2 router | G0/2 / G0/2 | 172.18.88.136/30 | .137 / .138 |
| Branch1 to MLS1 | Branch1 router / MLS1 | G0/1 / G0/1 | 172.18.88.140/30 | .141 / .142 |
| HQ to ISP | HQ perimeter router / ISP | S0/0/0 | 165.16.151.128/30 | .129 / .130 |
| Branch to local ISP | Branch perimeter router / Local ISP | S0/0/1 | 165.16.151.132/30 | .133 / .134 |
| Local ISP LAN | Local ISP router | Fa0/0 | 165.16.151.0/25 | 165.16.151.1 |
| Site-X to local ISP | Local ISP router / Site-X router | S0/2 / S0/0/0 | 165.16.151.136/30 | .137 / .138 |
| Site-X outside | Site-X router / Site-X firewall | G0/0 / G1/1 | 209.165.151.232/30 | .233 / .234 |
| **Site-X LAN (new)** | Site-X firewall (ASA) | G1/8 | **172.18.90.0/26** | |

## Design notes

- Point to point links use /30 so no addresses are wasted.
- User LANs are sized to their host counts with VLSM (a /22, a /23, a /24, a /25 and several /26 blocks) inside 172.18.80.0/20.
- The new LAN sits behind an ASA firewall, separate from the existing branch LANs.

## Skills shown

VLSM and subnetting, enterprise IP address planning, VLAN and inter VLAN design, WAN addressing, documentation.
