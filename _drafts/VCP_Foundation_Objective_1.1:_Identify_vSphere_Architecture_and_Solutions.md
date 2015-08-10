---
layout: post
title: VCP Foundation Objective 1.1 Identify vSphere Architecture and Solutions
categories: vmware nsx
---

This is the start of the series digging into the blueprint for the VCP Foundation Exam. This post will deal with "Objective 1.1 Identify vSphere Architecture and Solutions for a given use case". Let's get started.

### Identify available vSphere editions and features

There are essentially 11 editions of vSphere available today, although the comparison on the website only lists 10, and it is debatable if the last one I have included here should be considered part of vSphere at all. I've included it though, because it is the base on which the rest is built, and it's good to know it exists. There are a lot of acronyms in this table, most of them we will dig into later

| *vSphere Edition*                          	| Description                                                                                                                                                                                                                              	|
|--------------------------------------------	|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| Standard                                   	| The base vSphere edition: vMotion, svMotion, HA, DP, FT, vShield Endpoint, vSphere Replication, Hott Add, vVols,Storage Policy BAsed Management, Content Library, Storage APIs                                                           	|
| Enterprise                                 	| Standard plus: Reliable Memory, Big data extensions, virtual serial port concentrator, DRS, SPM                                                                                                                                          	|
| Enterprise Plus                            	| Enterprise plus: sDRS, SIOC, NIOC, SR-IOV, flash read cache, NVIDA Grid vGPU, dvSwitch, host prifles, autodeploy                                                                                                                         	|
| Standard with Operations Management        	| Standard plus: Operations Visibility and Management, Performance Monitoring and Predictive Analytics, Capacity Management and Optimization, Change, Configuration and Compliance Management, including vSphere Security Hardening        	|
| Enterprise with Operations Management      	| Enterprise plus: Operations Visibility and Management, Performance Monitoring and Predictive Analytics, Capacity Management and Optimization, Change, Configuration and Compliance Management, including vSphere Security Hardening      	|
| Enterprise Plus with Operations Management 	| Enterprise Plus plus: Operations Visibility and Management, Performance Monitoring and Predictive Analytics, Capacity Management and Optimization, Change, Configuration and Compliance Management, including vSphere Security Hardening 	|
| Remote office/Branch Office Standard       	| Adds VM capacity into existing Std, Ent, Ent+ system. Packs of 25 VMs. Feature set roughly equivilent to Std.                                                                                                                            	|
| Remote office/Branch Office Advanced       	| Adds VM capacity into existing Std, Ent, Ent+ system. Packs of 25 VMs. Feature set roughly equivilent to Ent+                                                                                                                            	|
| Essentials Standard                        	| For very small enterprises. Cut down vCenter(vCenter Server Essentials), up to 3 servers with 2CPUs each                                                                                                                                 	|
| Essentials Advanced                        	| Essentials Std adds vMotion, HA, DP, vShield endpoint, vSphere replication.                                                                                                                                                              	|
| ESXi Hypervisor Free                       	| Basic Hypervisor. No central management. No advanced features.                                                                                                                                                                           	|

These editions break down into five basic categories:

1. The hypervisor - not really a vSphere eidition at all, and unable to connect to vCenter server. Included for completness.
2. Essentials - A reduced feature set, only usable on up to three hosts, designed for the SMB. Upgrade capacity is limited.
3. ROBO (Remote Office/Branch Office) - Designed to add hosts in remote locations to an existing vSphere installation. 
4. vSphere - The baseline for medium to large enterprise. A nice upgrade path from fewer to more features by licensing. Most additional products assume this as a base. Most documentation assumes this edition set.
5. vSphere with Operations Management - Basically a way to purchase vSphere along with the vRealise suit to gain orchistration, insight and automation.

### Identify the various data center solutions that interact with vSphere (Horizon, SRM, etc.)

In addition to the vSphere system with gives you the ability to virtualise, there are the VMware add in products that extend the functionality.

* Horizon extends vSphere into the Virtual Desktop domain.
* Site Recovery Manager (SRM) gives active/passive DR capabilities, with the ability to fail your virtual infrastructure to a remote location.
* vRealise gives operations management and insight, along with Orchestration.
* vCloud Suite gives the ability to create multi-tenant private clouds.
* NSX gives fine granied network virtualisation with distributed routing and firewalling along with data protection.
* VSAN moves storage closer to compute by implementing a virtual SAN in your ESXi hosts
* Airwatch allows Enterpise mobility and builds on Horizon.

### Explain ESXi and vCenter Server architectures

There are a few ways we can design our VMware infrastructure depending upon the constraints. These start simple, and get more complex, but the added complexity often has distinct benefits. For any given customer, a solution will uisually fit broadly into one of these schemes, but I have seen situations where more than one has been implemented.

#### ESXi Standalone

This is the only solution we can use for the ESXi Free Hypervisor. There can be external storage, but this is not necessary. In this case we use a single ESXi host with no vCenter. 

![ESXi Architecture]({{ site.url }}/assets/esxi_architecture.png)

This gives us the benefits of consolodating physical servers onto a single host and better resource utiliation. 

This system is harder to manage with multiple hosts, and does not scale well. There are no advanced features such as live migrations.

I have used this in an instance where I needed a couple of low utilisation VMs at multiple sites, but dodn't need to manage them often, or worry about fail-over. 

#### Single Cluster

This is the solution introduced in the Essentials Product line, and the simplest of Full Fat vSphere deployments. Here we introduce vCenter and Shared Storage, to gain the advantages of live migration, and manageability.

This solution is more scaleable than the first solution we discussed, but the limit of 64 hosts per cluster means that is doesn't scale as well as the final architecture we will look at.

By including Management (i.e. vCenter) and usually DMZ (De-militarized zone, or "unsafe") traffic into the cluster we have a single failure domain where failure of a host, or compromise of a single network affects the whole system.

This is the standard SME solution that most people businesses atrt out with. The constraints are loose enough that this is a good fit for a large number of clients.

####Many, specialised clusters

This is the mostr scaleable system available. This is used for cloud environments and large deployments, or when VDI is introduced.

In this system the servers doing the work (Compute) are in dedicated clusters. The servers doing management and DMZ traffic get clusters dedicated to them. Servers holding VDI user sessions get dedicated clusters. There are usually multiple vCenter servers, one serving the Management cluster, one serving the compute clusters, and one serving the VDI clusters. This level of segragation makes the system very scaleable. Adding in new compute capacity is a modular process. The seperate clusters also become seperate failure domains. Finally, delegation of admin work is easier and more secure, so VDI admins can be kept away from Compute admin proviledges and vice versa.

The downside to this architecture is it's complexity.

####Multiple vCenter systems

The final architecture we will look at runs parallel to the others. It is possible to have multiple vCenters running in deifferent datacenters, and now to vMotion between them. This is new in vSphere 6.0. This means that vCenter traffic can be kept local to a DC and not transported accorss the WAN. 
