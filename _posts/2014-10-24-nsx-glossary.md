---
layout: post
title: NSX Glossary
categories: 
- vmware
- nsx
status: draft
type: post
meta:
author: Anthony Metcalf
---

This is a list of terms I've come accross so far in reading up on VMware NSX.

| Term | Meaning | Description |
|------|---------|---|
| BUM	| Broadcast, Unknown unicast, Multicast	| All traffic that isn't to a known unicast address |
| DFW	| Distributed Logical Firewall	| Firewall function at the Host Kenel Level (East West Traffic) |
| DLR	| Distributed Logical Router	| Routing Function at the Host Kernel Level |
| ECMP	| Equal Cost Multipathing	| Using many active routes to load balance at layer 3 |
| ESG	| Edge Services Gateway	| Routing/Firewall Function at the ''edge'' (North South Traffic) |
| LIF	| Logical Interface	| Where VM interfaces to the logical network |
| MTEP	| Multicast Tunnel Endpoint	| VTEP when used in Multicast Mode |
| TZ	| Transport Zone	|  |
| UTEP	| Unicast Tunnel Endpoint	| VTEP when used in Unicast Mode |
| vCNS	| vCloud Networking and Security	| Predecessor to NSX |
| VNI	| Virtual Network Interface	|  |
| VTEP	| VXLAN Tunnel Endpoint	Provides | Layer two servies ast the host level |
| vxLAN	| Virtual Extensable LAN	| IP over IP tunnel to enable layer 2 over layer 3 networks |

