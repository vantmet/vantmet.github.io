---
layout: post
title: NSX Packet Walks - Replication Modes
categories: vmware nsx
---

This is the third post in the NSX Packet Walks series. You probably want to start at [the first post](http://vantmet.github.io/2015/02/10/packet-walks) or [the second post](http://vantmet.github.io/2015/03/24/packet-walks1).

Up to now we have forcused on the traffic from one VM to another VM somewhere within the NSX system. I have skipped over the way traffic is distributed between hosts, and I haven't discussed the ways that traffic can leave the NSX system. The next three posts will cover these topics.

This post will focus on the different methedologies available in NSX for dealing with BUM traffic. That is Broadcast, Unknown Unicast and Multicast traffic. This encompasses all traffic that doesn't have a specific layer three address. In all three of these cases, the traffic is destined to touch anything up to all physical hosts in the cluster, and NSX has a few different ways of handling that traffic.

## VTEP Segments

Before we get started though, it would be useful to discus VTEP Segments. Following the VMware best practice design guide, our VMWware system will be split into multiple clusters, with Management and Edge clusters (sometimes consolodated) and probably multiple computer clusters. This makes for ease of management, and reduced failure domains. It is common to design in a 1 cluster per rack pattern, so that the rack unit becomes a module of capacity. Each rack is then created with unique IP information, so that addition (or removal) of a rack doesn't affect IP ranges, and so that equal cost multipathing (ECMP) can be used to form resiliant connections to a Leaf-Spine network.

Of all of that, we are concerned with just one thing. Each rack will be on a different IP subnet. These subnets are refered to as VTEP Segments. The VTEP Segment a host is conencted to, is the IP subnet of it's VXLAN interfaces.

![VTEP Segments]({{ site.url }}/assets/vtep_segments.png)

##Replication Modes

There are three available VXLAN replication modes:

1. Muticast
2. Unicast
3. Hybrid

Multicast has the most restrictive hardware requirements, but is the easiest conceptually to grasp. The earlier posts int he series assumed Unicast, which is only available on the vSphere implementation of NSX and is the most complex, but also the most flexible. The replication mode is set at the Transport Zone layer. It *can* be over-ridden at the Virtual Switch layer, but I can't see any reason why you would want to do this on a case by case basis. KISS!

### Multicast Replication Mode

Muticast replication mode depends on the underlay switch configuration to support a full multicast implementation. The VTEP(s) on each host, join a multicast group on the physical switch for each Logical Switch that is created, so any BUM traffic can be directed to the multicast group, and is distributed to all VTEPs in the group. This has the advantage that BUM traffic is only distributed to hosts that participate in a given Logical Switch.

The downsides are:

1. Support for IGMP, PIM and layer 3 multicast routing are required at the hardware layer. This is a nortoriously complex area of network design and maintenance that many wish to avoid.
2. The system is most deeply tied to the hardware layer.
3. With many Logical Switches, you need many multicast groups which could become a limitation in a large environment.

### Unicast Replication Mode

Unicast replication mode is the exact opposite of multicast mode. With unicast mode, when a BUM frame is recieved by a VTEP, it send the frame directly to each other host in it's own VXLAN Segment. It then picks a host in each other VXLAN Segment that is designated the "UTEP" or "Unicast Tunnel End Point". When this host recieves the frame, it sends it to each other host in the segment.

Again, the calculation of "each other host" and "each other segment" takes into account logical switch location, so if a logical switch is not active on a host, or segment, it will not recieve the frame.

This has the positive side that the underlying hardware capability is compleatly ignored. It is a pure software solution that decouples us from the underlying hardware.

The major downside is that in the case of many segments, many packets may be sent from a single server rather than just one in the multicast mode. This can eat bandwidth in high density cases.

### Hybrid Replication Mode