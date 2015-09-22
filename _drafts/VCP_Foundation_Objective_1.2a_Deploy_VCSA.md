---
layout: post
title: VCP Foundation Objective 1.1a - Deploy the vCenter Appliance (vCSA)
categories: vmware nsx VCP
---

Objective 1.2 has a few installations and quite long processes, so I am going to split it up into multiple posts, each with plenty of screen shots detailing the different aspects. I will start with the VCSA installation, but before we start, we need to consider a few things, and set up a few pre-requisites.

### Prerequisites

The first thing to think about is the location of the database that vCenter will use. The Appliance can use either a built in Postgresql database, or an external Oracle one. The internal database supports up to 1,000 hosts and 10,000 virtual machines, so should be good for most installations. This is the database we will use within the example installations. We will cover using external databases seperatly later.

All clocks in the environment must be synchronised. The best way to do this is to ensure that all ESXi hosts, and the client machine used to install are using the same NTP servers. We conver setting up NTP on ESXi later in the series.

The final thing is that the DNS should be fully set up before the appliance can be installed. This means that at least, the DNS server given to the apliance should have the host name the appliance will be installed with. It is usually best to ensure that both forward and reverse lookups are correctly set up for all of the infrastructure.