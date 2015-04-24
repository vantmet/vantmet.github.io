---
layout: post
title: NSX Packet Walks - VLAN Bridge
categories: vmware nsx
---

This is the fourth post in the NSX Packet Walks series. You probably want to start at [the first post](http://vantmet.github.io/2015/02/10/packet-walks).

Up to now we have focused on the traffic from one VM to another VM somewhere within the NSX system, as well as how traffic moves between physical hosts. But what if you're data center isn't 100% virtualised? Can you still use NSX? What are the constraints? This post will look at this question.

There are a few reasons why we may have physical machines still within the network, which must be accessed from within the NSX environment, but the two most common are Legacy applications that will not support being virtualised (say Sparc/Solaris banking software) and high performance systems that use the full capacity of the hardware and so are not virtualised (huge database systems fall into this category, although it's a category that's shrinking all the time). In these cases we ned a way to connect the two sides, the VXLAN  on the NSX side, with the VLAN ont he physical side. This is done using VXLAN-to-VLAN bridging, also called L2-Bridging or the L2-Bridge.

Consider the following image, taken from the VMware NSX Design Guide version 2.1 (fig 35)

![VXLAN Bridge]({{ site.url }}/assets/nsx_design_guide_fgi_35.png)

The diagram shows how the pysical workload can be connected to a VLAN, which can then be conencted to one (although in reality multiple) but not all hosts. We don't need to span the VLAN to all of our compute racks just to get the VM access to the VLAN. This is a major advantage.

We would probably connect the VLAN to all of the servers in the Edge rack, to ensure that it remained accessable in case of failover.

We can also see from the diagram that the L2-Bridging is done from the virtual distributed router, and not from the Edge Router VM. Finally, we can see that the host that is used for the L2-Bridge, is the one running the "Active Control VM". 

===Active Control VM===


