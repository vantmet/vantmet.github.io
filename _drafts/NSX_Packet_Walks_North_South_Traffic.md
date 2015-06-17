---
layout: post
title: NSX Packet Walks - North/South Traffic
categories: vmware nsx
---

This is my final port in the NSX Packet walk series. So far I have discussed only so called "East/West" traffic. That is traffic which is moving from one VM, or physical machine, in out network to another. This traffic will never leave the datacenter, and in a lot of cases, will never leave the same rack in a small system, or NSX system.

###Traditional Network Design

In the traditional network, traffic would be seperated by purpose onto different VLANs, and would all be funnelled towards the network core to be routed. North-bound traffic (i.e. traffic leaving the network) would then be routed to a physical firewall, before leaving the network via an edge router. South-bound (i.e. traffic entering the network) would  traverse int he opposite direction.

This has the very obvious disadvantage that for traffic to reach the servers, the correct VLANs must be in place, and the correct forewall rules must have been implemented at the edge. Historically the network and security teams would have each handeled that, and requests that involved a new subnet would take a long time while those teams processed the request.

###Virtualised Network - Physical Next Hop

As we've seen, internally we have removed the need for the VLANs that span outside of our compute clusters for the most part. All of our East/West traffic is handled by Distributed Routers. The first, and most obvious step to making North/South Traffic then is to utilise the DLRs ability to perform dynamic routing to pass traffic to a physical router as the next hop. 

Using OSPF or BGP would mean that the next hop router knows of our internal networks as and when we create them. The downside to this is that we still need to pass the VLAN the Phsycial router is connected to to all of the compute nodes.

###Virtualised Network - Edge Router

The next option we could come up with would be to put a VM performing routing in the Edge Rack. We could then have dynamic routing updates from this VM to the DLR, and from this VM to the next hop router.

As this VM is in the edge rack, the external VLAN only needs to be passed into the hosts in the Edge Rack.

The biggest constraint here is pushing all of the North/South traffic through the edge rack, and the vulnerability of the NSX Edge VM. If the Edge VM fails, we would lose all North/South traffic.

