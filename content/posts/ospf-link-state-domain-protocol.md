+++
title = "OSPF Link State Domain Protocol - Part 2"
date = 2026-11-28T21:15:57.185Z
description = "Part 2 covers the area concept in OSPF and how it works."
tags = [ "OSPF Dynamic-Protocols Routing" ]
+++

The information about topology is flooding, or rather, being exchanged between routers. As I explained in part 1, routers exchange information using LSA records (Link State Advertisements). Each router must maintain its own database (Link State Database), so it has an exact copy to build the shortest path later.

To propagate topology changes across each router's database, we need a mesh connection for redundancy. Besides, it requires high hardware capacity. The OSPF protocol provides a solution to this problem in the link-state domain model.  As mentioned before, routers exchange information to maintain the same LSDB records. Therefore, OSPF introduces an area model in which segments of the network are divided, and information is propagated in a controlled manner. 

## Area Design 

OSPF area design creates a 2-tier hierarchy with two main types: 

* Area 0 (Backbone Area) as the top hierarchy 
* All other areas (identified from 1 to 65 535)

An area is identified as a **32-bit number**. Area 0 is reserved for the Backbone. 

&#x20;
