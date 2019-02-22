---
layout: post
title: NSX Packet Walks - North/South Traffic
categories: vmware nsx
---

This is my final port in the NSX Packet walk series. So far I have discussed only so called "East/West" traffic. That is traffic which is moving from one VM, or physical machine, in our network to another. This traffic will never leave the datacenter, and in a lot of cases, will never leave the same rack in a small system, or NSX system.

### Traditional Network Design

In the traditional network, traffic would be separated by purpose onto different VLANs, and would all be funnelled towards the network core to be routed. North-bound traffic (i.e. traffic leaving the network) would then be routed to a physical firewall, before leaving the network via an edge router. South-bound (i.e. traffic entering the network) would traverse in the opposite direction.

This has the very obvious disadvantage that for traffic to reach the servers, the correct VLANs must be in place, and the correct firewall rules must have been implemented at the edge. Historically the network and security teams would have each handled that, and requests that involved a new subnet would take a long time while those teams processed the request.

### Virtualised Network - Physical Next Hop

As we've seen, internally we have removed the need for the VLANs that span outside of our compute clusters for the most part. All of our East/West traffic is handled by Distributed Routers. The first, and most obvious step to making North/South Traffic then is to utilise the DLRs ability to perform dynamic routing to pass traffic to a physical router as the next hop. 

Using OSPF or BGP would mean that the next hop router knows of our internal networks as and when we create them. The downside to this is that we still need to pass the VLAN the Physical router is connected to to all of the compute nodes.

### Virtualised Network - Edge Router

The next option we could come up with would be to put a VM performing routing in the Edge Rack. We could then have dynamic routing updates from this VM to the DLR, and from this VM to the next hop router.

As this VM is in the edge rack, the external VLAN only needs to be passed into the hosts in the Edge Rack.

The biggest constraint here is pushing all of the North/South traffic through the edge rack, and the vulnerability of the NSX Edge VM. If the Edge VM fails, we would lose all North/South traffic. This has been alleviated by VMware by allowing multiple Edge VMs.

This VM is called the NSX Edge Services gateway; It is an evolution of the vShield Edge that was first part of vCloud Director, and later vCNS.

The Edge services gateway can have up to 10 internal, up-link or trunk interfaces. This combines with the "Edge Router" which we have so far referred to as the Distributed Logical Router (DLR) which can have up to 8 up-links and 1,000 internal interfaces. In essence, a given Edge services gateway can connect to multiple external networks, or multiple DLRs (or both) and a given DLR can utilise multiple Edge Services Gateways for load balancing and resilience.

### Connectivity

The figure below (taken from the VMware NSX Design Guide version 2.1 (fig 41)) shows the logical and physical networks we will be thinking about.

![NSX Edge Services Gateway]({{ site.url }}/assets/NSOutline.png)

In the top part of the figure we can see the green circle with Arrows, which represents the combination of the DLR and Edge Services Gateway, is connected to both of the logical switches, and also to the up-link to the L3 network. We can envisage how there could be other up-links to a WAN, or DMZ (or even multiple DMZs), or to other L3 networks if we had multiple ISPs etc. These up-links come from the pool of 10 links in the Edge Services Gateway. The logical Switches connect to the DLR which can connect to up to 1,000 logical switches.

Connectivity between the DLR and the Edge is through a transit network.

### Resilience

It is possible to configure BGP, or OSPF between the Edge Services Gateway and the DLR. This means that we can have multiple Edge Services Gateways (up to 8) with connections to a given DLR, which can use ECMP (Equal Cost Multi-Pathing) to spread the North/South traffic load over the multiple gateways and also give resilience. This is very much and Active/Active setup.

The Alternative is to have the Edge Services Gateway deployed as a HA pair. This means that we get an Active/Passive setup whereby if one Edge fails, the other takes over within a few seconds. This is used when the Active/Active option above is not possible, due to using the other Edge services that are available such as Load Balancing, NAT and the Edge Firewall.

Of course, we can have multiple layers of Edge Services gateways if necessary, with HA Pairs running NAT close to the logical switches, and ECMP aggregating the traffic outbound.


### Conclusion

This ends our short series on NSX and Packet flows. Although the later posts have become much more generic and less about how the packets actually move, that to some extent is precisely the point of NSX. We gain the ability to think much more logically about our whole datacenter network, with almost no reliance on physical hardware. We can micro-segment traffic so that only the allowed VMs see it, regardless of where they are running. We can connect to existing networks and migrate slowly and seamlessly into NSX. We can even plug our internet transit directly into hosts and bypass physical firewall and routing devices.

