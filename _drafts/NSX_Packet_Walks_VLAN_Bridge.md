---
layout: post
title: NSX Packet Walks - VLAN Bridge
categories: vmware nsx
---

This is the fourth post in the NSX Packet Walks series. You probably want to start at [the first post](http://vantmet.github.io/2015/02/10/packet-walks).

Up to now we have focused on the traffic from one VM to another VM somewhere within the NSX system, as well as how traffic moves between physical hosts. But what if you're data center isn't 100% virtualised? Can you still use NSX? What are the constraints? This post will look at this question.

There are a few reasons why we may have physical machines still within the network, which must be accessed from within the NSX environment, but the two most common are Legacy applications that will not support being virtualised (say Sparc/Solaris banking software) and high performance systems that use the full capacity of the hardware and so are not virtualised (huge database systems fall into this category, although it's a category that's shrinking all the time). In these cases we ned a way to connect the two sides, the VXLAN  on the NSX side, with the VLAN ont he physical side. This is done using VXLAN-to-VLAN bridging.

Consider the following image, takn from the VMware NSX Design Guide version 2.1 (fig 35)

![VXLAN Bridge]({{ site.url }}/assets/nsx_design_guide_fgi_35.png)


