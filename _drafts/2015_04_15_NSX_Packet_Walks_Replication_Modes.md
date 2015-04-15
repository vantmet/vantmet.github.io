---
layout: post
title: NSX Packet Walks - Replication Modes
categories: vmware nsx
---

This is the third post in the NSX Packet Walks series. You probably want to start at [the first post](http://vantmet.github.io/2015/02/10/packet-walks) or [the second post](http://vantmet.github.io/2015/03/24/packet-walks1).

Up to now we have forcused on the traffic from one VM to another VM somewhere within the NSX system. I have skipped over the way traffic is distributed between hosts, and I haven't discussed the ways that traffic can leave the NSX system. The next three posts will cover these topics.

This post will focus on the different methedologies available in NSX for dealing with BUM traffic. That is Broadcast, Unknown Unicast and Multicast traffic. This encompasses all traffic that doesn't have a specific layer three address. In all three of these cases, the traffic is destined to touch anything up to all physical hosts in the cluster, and NSX has a few different ways of handling that traffic.

Before we get started though, it would be useful to discus VTEP Segments. Following the VMware best practice design guide, our VMWware system will be split into multiple clusters, with Management and Edge clusters (sometimes consolodated) and probably multiple computer clusters. This makes for ease of management, and reduced failure domains. It is common to design in a 1 cluster per rack pattern, so that the rack unit becomes a module of capacity. Each rack is then created with unique IP information, so that addition (or removal) of a rack doesn't affect IP ranges, and so that equal cost multipathing (ECMP) can be used to form resiliant connections to a Leaf-Spine network.

Of all of that, we are concerned with just one thing. Each rack will be on a different IP subnet. These subnets are refered to as VTEP Segments. The VTEP Segment a host is conencted to, is the IP subnet of it's VXLAN interfaces.

There are three available VXLAN replication modes:

1. Muticast
2. Hybrid
3. Unicast


