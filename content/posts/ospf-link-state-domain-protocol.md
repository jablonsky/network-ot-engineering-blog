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

## OSPF Types of Routers

In the OSPF protocol, we can identify 4 types of routers:

* Internal Routers: Internal routers are routers which have all interfaces located in a single area. 
* Backbone Routers: are routers that have at least one interface configured in Backbone area. 
* ABR - Area Border Routers: These routers have one or more interfaces in the Backbone area and one or more interfaces in another area. ABR routers are responsible for managing LSDBs between areas they are sitting in. Thus, ABR routers have more capable hardware. They summarise the LSAs between areas and exchange them. 
* ASBR - Autonomous System Border Routers: These routers redistribute routes from non-OSPF areas. 

In the OSPF design, routers can be different types depending on where they sit. For example, a router can be an internal router and a backbone router at the same time.  
