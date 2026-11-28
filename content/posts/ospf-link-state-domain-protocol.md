+++
title = "OSPF Link State Domain Protocol - Part 2"
date = 2026-11-25T21:15:57.185Z
description = "Part 2 covers the area concept in OSPF and how it works."
tags = [ "OSPF", "Dynamic Protocols", "Routing" ]
+++

## OSPF Flooding the Information

Routers flood, or rather exchange, topology information. As I explained in Part 1, routers exchange information using LSA records (Link State Advertisements). Each router must maintain its own database (Link State Database), so it has an exact copy to build the shortest path later.

To propagate topology changes across each router's database, we need a mesh connection for redundancy. Besides, it requires high hardware capacity. The OSPF protocol provides a solution to this problem in the link-state domain model.  As mentioned before, routers exchange information to maintain the same LSDB records. Therefore, OSPF introduces an area model in which segments of the network are divided, and information is propagated in a controlled manner.

![Five routers topology](/images/ospf-five-routers/top-2.png "Figure 1 - Five routers topology with added links between router 3 and 3, also router 4 and 5.")

## Area Design

OSPF area design creates a 2-tier hierarchy with two main types:

* Area 0 (Backbone Area) as the top hierarchy
* All other areas (identified from 1 to 65 535)

An area is identified as a **32-bit number**. Area 0 is reserved for the Backbone.

&#x20;

## OSPF Types of Routers

In the OSPF protocol, we can identify 4 types of routers:

* Internal Routers: Internal routers have all interfaces located in a single area.
* Backbone Routers: Routers with at least one interface in the backbone area.
* ABR - Area Border Routers: These routers have one or more interfaces in the Backbone area and one or more interfaces in another area. ABR routers are responsible for managing LSDBs between areas they are sitting in. Thus, ABR routers have more capable hardware. They summarise and exchange LSAs between areas.
* ASBR - Autonomous System Border Routers: These routers redistribute routes from non-OSPF into an OSPF area.

In the OSPF design, routers can be different types depending on where they sit. For example, a router can be an internal router and a backbone router at the same time.

## Hello Packets

OSPF protocol uses HELLO packets to discover neighbours. These packets use a multicast IP address to send the message to neighbour routers. However, OSPF can send unicast to routers not operating in multicast mode. This is associated with NBMA network types, such as legacy Frame Relay Network. So, on a Cisco router, we can explicitly configure a unicast address to use in the Hello packet.

My example in this article uses the broadcast method, where OSPF dynamically discovers neighbours using multicast hellos.

The hollo packets objectives is to discover the neghbours. The packet is send periodically every 10 seconds in default router configurationm using IP address 244.0.0.5. Each hello packe includes 7 elements:

* Router ID

| Field                    | What it tells the router                                                                                    |
| ------------------------ | ----------------------------------------------------------------------------------------------------------- |
| **Router ID**            | The ID of the router sending the Hello.                                                                     |
| **Hello Interval**       | How often the router sends Hello packets.                                                                   |
| **Dead Interval**        | How long to wait without hearing a Hello before declaring the neighbour down.                               |
| **Neighbour Router IDs** | The Router IDs of OSPF neighbours the sender has heard from on that network.                                |
| **Area ID**              | The OSPF area the interface belongs to. Neighbours must agree on it.                                        |
| **Authentication Data**  | Authentication information, when configured. Its use depends on the authentication method.                  |
| **Network Mask**         | The subnet mask of the interface’s network. Neighbours on the same link generally need compatible settings. |
