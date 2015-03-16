---
layout: post
title: NSX Packet Walks Continued
categories: vmware nsx
status: draft
---

This post is a continuation of the [last post]({% post_url 2015-02-10-packet-walks %}) where I discussed a very simple virtual network, and a very simple VXLAN environment. If you haven't already, you will want to read that post first.  In this post I'm going to step it up a gear and introduce a virtual distributed router to the mix. This is a key part of NSX and the ability to create virtual networks on the fly without waiting for provisioning externally. It also makes for an interesting thought process when the "router" is distributed across all hosts.

Again we will start with a very basic network, to get some concepts out of the way, then we will move to the NSX version.

![Basic Network]({{ site.url }}/assets/basic_routed_layout.png)

The above diagram shows two VMs this time on different VLANs separated by a router. Now is probably a good time to make a small diversion to talk about where the decision about what device to send a packet too is made. This doesn't matter to the vast majority of cases, as with just a single NIC the outcome of the decision process is the same. But as soon as you get devices with multiple routes, this starts to get interesting. Last time we discussed devices on the same VLAN, the same subnet, the same broadcast domain. These three terms are often used interchangeably, and are indeed usually kept the same to make life easier. This meant that the OS withing the VM, when sending a packet to VM2 knew to send an arp to discover that VMs MAC address to use for all further communication. That decision tree though started a good ways before that.

When the OS gets the message from the Application that a packet needs to be sent to another device it first looks at the IP address of the destination machine. It compares this to it's ARP table, a list of MAC addresses, and IP addresses, and Interfaces they have been seen on. If the IP address of the other machine exists in the ARP table, then the packet can be addressed to the correct MAC address and sent out of the correct interface. If there is no entry, then it must work it out. It then compares the IP to any and all known IP subnets connected to interfaces on the machine. If the IP address is in the same subnet as one (or more) of the interfaces, then it matches to the most restrictive subnet that contains the IP, and sends an ARP out of that interface.

If the IP address doesn't fall into the subnet of any of the subnets on the machine, then it is sent to the Default Gateway MAC address instead. The Default Gateway must always be in the subnet of one of the interfaces on the machine.

So, now we can look at the diagram again. We can assume that each VM has "router" as it's Default Gateway.

The two VMs boot. The have a mac address, and an IP address configured. Just for fun we'll assume they each have the IP address of the other in their hosts file, so we don't have to involve DNS. VM1 wants to send a packet to VM2. Following the decision logic outlined above, VM1 'knows' that it cannot send the packet direct to VM2 and must instead send to the interface of it's default gateway.

1. VM1 sends an ARP packet to FF:FF:FF:FF:FF:FF, with the IP address of the default gateway. This is passed via the portgroup into the vSwitch. The vSwitch at this point stores the gateway's MAC in it's [CAM Table](http://en.wikipedia.org/wiki/CAM_Table) and IP addresses in it's [fib](http://en.wikipedia.org/wiki/Forwarding_information_base) table. 
2. the vSwitch passes the packet to the physical switch. The physical switch stores the IP and MAC of VM1 along with the port the packet entered on.
3. the first physical switch either broadcasts the packet, or more likely already has the IP/MAC combination in memory and passesd the packet to the router.
4. the router replies with it's ARP reply back up to the VM.
5. VM1 then sends the original packet to VM two, addressing the packet with a destination MAC address of the Default Gateway.
6. The packet flows to the router, which then inspects the packet, and does a similar look-up to the one the VM OS originally did. That is it compares the destination IP to those of the subnets it is connected to, and posts the packet out of the correct interface. If it already has the IP/MAC details in it's arp table then the packet will be unicast, addressed to the correct MAC address. If not it will be broadcast, addressed to FF:FF:FF:FF:FF:FF. Either way it will be seen by VM2 which can reply. If the router doesn't have an entry for the correct subnet, and also doesn't have a default gateway. The packet is dropped.

This is about the simplest conversation that can happen over a routed Ethernet network.

Now let's step it up a gear.

![Basic NSX Network]({{ site.url }}/assets/basic_routed_nsx_layout.png)

Here we have a very simple, NSX based network. I am assuming one transport zone, set to "unicast mode", with one logical network (say VXLAN 5001) which both VMs are attached to.

One of the key differences between Unicast Mode and the other modes is that, in unicast mode, the host relays the IP information for the VM to the controllers which then relay that information to the vDistributed Switches that make up the Logical Switch. This means that when the Arp is generated at 1. The host already knows the IP/MAC combination of VM2. The full flow goes like:

1. VM1 sends an ARP packet to FF:FF:FF:FF:FF:FF. This is passed to the local dvSwitch and into the VTEP.
2. The VTEP encapsulates the ARP into a VXLAN packet with the destination mac address being the VTEP mac address of the host VM2 is running on, and the source address being it's own TEP MAC address. This packet is pushed out to the physical switch.
3. In normal circumstances the physical switches will have the VTEP MAC addresses through communication with the controller, but if not, this is identical to the first scenario, except the VTEP MAC addresses are used, not the VM addresses.
4. The second physical switch acts again just like it did last time.
5. The VTEP deencapsulates the packet, and passes the original ARP into the dvSwitch.
6. Packet is delivered to the VMs on that VLAN on that host, and VM2 responds.

In both scenarios our VMs have both sent and received the same information. Notice how the MAC address of the VMs is never seen by the switches though in our second case. This means we've just reduced the fib on the switches by a couple of orders of magnitude.
