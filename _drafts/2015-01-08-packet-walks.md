---
layout: post
title: NSX Packet Walks
categories: vmware nsx
---

I was once asked an interview question that seemed very basic to me, and probably will to anyone with any sort of Network experience, but that I'm told many people don't even think about. It was a simple question: How does a switch pass packets.

I'm going to start in this blog post, with that question, but in a virtual environment. Then I'm going to extrapolate to a couple of NSX situations that I find quite interesting.

![Basic Network]({{ site.url }}/assets/basic_layout.png)

The above diagram shows two VMs on the same VLAN. The vSwitches can be standard or distributed, and the physical switches could be anything from a desktop unmanaged switch through to a Nexus. For our purposes, that doesn't matter.

The two VMs boot. The have a mac address, and an IP address preconfigured. Just for fun we'll assume they each have the IP address of the other in their hosts file, so we don't have to involve DNS. VM1 wants to send a packet to VM2.

1. VM1 sends an ARP packet to FF:FF:FF:FF:FF:FF. This is passed via the portgroup into the vSwitch. The vSwitch at this point stores VM1's MAC and IP addresses in it's [fib](http://en.wikipedia.org/wiki/Forwarding_information_base)  table. 
2. the vSwitch passes the packet to the physical switch. The physical switch stores the IP and MAC of VM1 along with the port the packet entered on.
3. the first physical switch passes the broadcast traffic out of all ports, but stores where the request came from in their fib tables.
4. the second physical switch passes the broadcast traffic out of all ports, but stores where the request came from in their fib tables.
5. the second vSwitch switch passes the broadcast traffic out of all ports, but stores where the request came from in their fib tables.
6. VM2 sees the request and replies. In this case the destination MAC is known, so the packet is passed back through the steps in reverse order. At each step the physical and virtual switches store the MAC address of VM2 to the fib table, and pass the packet out of a single port, back toward VM1.

This is about the simplest conversation that can happen over a switched Ethernet network.

Now let's step it up a gear.

![Basic NSX Network]({{ site.url }}/assets/basic_nsx_layout.png)

