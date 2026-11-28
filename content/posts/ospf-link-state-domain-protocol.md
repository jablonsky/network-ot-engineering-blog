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

The Hello packet's objective is to discover the neighbours. The packet is sent periodically every 10 seconds in the default router configuration, using the IP address 244.0.0.5. Each hello packet includes 7 elements:

* Router ID
* Hello interval
* Dead interval
* Neighbour Router ID
* Area ID
* Authentification data
* Network mask

The router ID is a numerical value represented as a 32-bit number in decimal form. Usually, each router has multiple IP addresses on its interfaces, so this number is not connected to interfaces and is more independent. Another important element is the hello interval. This parameter needs to be synchronised between neighbouring routers to communicate correctly. 

The dead interval element tells the router how long it must remember the neighbour. For example, if the dead interval is set to 40 seconds, the router waits for the neighbour for 40 seconds. However, if it still cannot hear from the neighbour after 40 seconds, it forgets about this relationship.  The router assumes the neighbour is no longer available in the topology.

The neighbour ID includes the neighbour router ID and is important for two-way reachability. This concept matters for neighbour adjacency, where routers see each other and can prove it. 

The Area ID is the OSPF area where the packets belong. The next interesting field of the Hello packet is authentication data. This is the password included in the router configuration for communication. The password can be included in each Hello packet. 

The network mask is the interface's subnet mask. 

Table 1 - Hello packet and its 7 elements

| Field                    | What it tells the router                                                                                    |
| ------------------------ | ----------------------------------------------------------------------------------------------------------- |
| **Router ID**            | The ID of the router sending the Hello.                                                                     |
| **Hello Interval**       | How often the router sends Hello packets.                                                                   |
| **Dead Interval**        | How long to wait without hearing a Hello before declaring the neighbour down.                               |
| **Neighbour Router IDs** | The Router IDs of OSPF neighbours the sender has heard from on that network.                                |
| **Area ID**              | The OSPF area the interface belongs to. Neighbours must agree on it.                                        |
| **Authentication Data**  | Authentication information, when configured. Its use depends on the authentication method.                  |
| **Network Mask**         | The subnet mask of the interface’s network. Neighbours on the same link generally need compatible settings. |

Using the CLI command: **Router# show ip ospf interface gigabitEthernet 0/0**, we can see most Hello elements associated with the router interface.

Router 1

```
Router-1#show ip ospf interface e0/0
%OSPF: OSPF not enabled on Ethernet0/0
Router-1#show ip ospf interface e0/1
Ethernet0/1 is up, line protocol is up 
  Internet Address 10.10.10.1/24, Interface ID 3, Area 0
  Attached via Network Statement
  Process ID 1, Router ID 1.1.1.1, Network Type BROADCAST, Cost: 10
  Topology-MTID    Cost    Disabled    Shutdown      Topology Name
        0           10        no          no            Base
  Transmit Delay is 1 sec, State BDR, Priority 1
  Designated Router (ID) 2.2.2.2, Interface address 10.10.10.2
  Backup Designated router (ID) 1.1.1.1, Interface address 10.10.10.1
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    oob-resync timeout 40
    Hello due in 00:00:06
  Supports Link-local Signaling (LLS)
  Cisco NSF helper support enabled
  IETF NSF helper support enabled
  Can be protected by per-prefix Loop-Free FastReroute
  Can be used for per-prefix Loop-Free FastReroute repair paths
  Not Protected by per-prefix TI-LFA
  Index 1/2/2, flood queue length 0
  Next 0x0(0)/0x0(0)/0x0(0)
  Last flood scan length is 1, maximum is 1
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 1, Adjacent neighbor count is 1 
    Adjacent with neighbor 2.2.2.2  (Designated Router)
  Suppress hello for 0 neighbor(s)
Router-1#
```

To check which router ID becomes the neighbour, we use the command: **Router# show ip ospf neighbor**

Router 1

```
Router-1#show ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/DR         00:00:32    10.10.10.2      Ethernet0/1
Router-1#
```

As shown above, Router 2 with SID 2.2.2.2 remains Router 1's neighbour, in full state as the designated router. The address 10.10.10.2 comes from Router 2 interface and is achievable by interface Ethernet 0/1 on Router 1. 
