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
| DLR | Distributed Logical Router | Routing Function at the Host Kernel Level  |
| DFW | Distributed Logical Firewall | Firewall function at the Host Kenel Level (East West Traffic) |
| ESG | Edge Services Gateway        | Routing/Firewall Function at the ''edge'' (North South Traffic)  |
| LIF | Logical Interface        | Where VM interfaces to the logical network  |
| vxLAN | Virtual Extensable LAN | IP over IP tunnel to enable layer 2 over layer 3 networks |
| vCNS  | vCloud Networking and Security | Predecessor to NSX  |
| TZ | Transport Zone | |
| VNI | Virtual Network Interface | | 
| BUM | Broadcast, Unicast, Multicast | |
| VTEP | VXLAN Tunnel Endpoint | Provides Layer two servies ast the host level |
| UTEP | Unicast Tunnel Endpoint | VTEP when used in Unicast Mode |
| MTEP | Multicast Tunnel Endpoint | VTEP when used in Multicast Mode |
| ECMP | Equal Cost Multipathing | Using many active routes to load balance at layer 3 |
