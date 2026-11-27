+++
title = "Introduction to OSPF Dynamic Routing Protocol - Part 1"
date = 2026-11-19T21:02:24.792Z
description = "Introduction to the Open Shortest Path First protocol - Part 1."
tags = [ "Networking OSPF Dynamic Routing" ]
+++

To show how the OSPF protocol works, I created a topology with a few selected Cisco routers in ContainerLab. ContainerLab is an amazing tool when you configure it with the VS Code extension.

This extension is called Containerlab and provides a graphical representation of Your Topology.

![](/images/ospf-five-routers/container-lab.png)Picture 1 - Containerlab extension shown in VS Code

Therefore, VS Code with this extension gives us full control through the friendly interface (GUI), which speeds up the experimentation process; in this case, I tried OSPF routing in a single area 0 (backbone).

![VS Code Editor](/images/ospf-five-routers/vs-code.png "Picture 1 - VS Code Editor with Containerlab extension")Picture 2 - VS Code with running Containerlab extension

Below, you can see five routers forming the topology above with configured IP addresses on their interfaces:

```
Router-1#show ip int brief
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            172.16.63.11    YES manual up                    up      
Ethernet0/1            10.10.10.1      YES NVRAM  up                    up      
Ethernet0/2            unassigned      YES NVRAM  administratively down down    
Ethernet0/3            unassigned      YES NVRAM  administratively down down    
Router-1#
```

```
Router-2#show ip int brief
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            172.16.63.12    YES manual up                    up      
Ethernet0/1            10.10.10.2      YES manual up                    up      
Ethernet0/2            10.1.0.1        YES manual up                    up      
Ethernet0/3            10.2.0.1        YES manual up                    up      
Ethernet1/0            10.3.0.1        YES manual up                    up      
Ethernet1/1            unassigned      YES NVRAM  administratively down down    
Ethernet1/2            unassigned      YES NVRAM  administratively down down    
Ethernet1/3            unassigned      YES NVRAM  administratively down down    
Router-2#
```

```
Router-3#show ip int brief
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            172.16.63.13    YES manual up                    up      
Ethernet0/1            10.1.0.2        YES manual up                    up      
Ethernet0/2            unassigned      YES NVRAM  administratively down down    
Ethernet0/3            unassigned      YES NVRAM  administratively down down    
Router-3#
```

```
Router-4#show ip int brief
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            172.16.63.14    YES manual up                    up      
Ethernet0/1            10.2.0.2        YES manual up                    up      
Ethernet0/2            unassigned      YES NVRAM  administratively down down    
Ethernet0/3            unassigned      YES NVRAM  administratively down down    
Router-4#
```

```
Router-5#show ip int brief
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            172.16.63.15    YES manual up                    up      
Ethernet0/1            10.3.0.1        YES manual up                    up      
Ethernet0/2            unassigned      YES NVRAM  administratively down down    
Ethernet0/3            unassigned      YES NVRAM  administratively down down    
Router-5#
```

![Five-router topology](/images/ospf-five-routers/2026-11-20_19-20.png#medium-large "Picture 2 - Five-router topology")Picture 3 - Five-router topology

Let's configure a single OSPF area in this topology. For now, I decided to place each router in Area 0. The commands below use the exact interface IP addresses:

#### Router 1

```
enable
configure terminal

router ospf 1
 router-id 1.1.1.1
 network 1.1.1.1 0.0.0.0 area 0
 network 10.10.10.1 0.0.0.0 area 0
 passive-interface Loopback0

end
write memory
```

#### Router 2

```
enable
configure terminal

router ospf 1
 router-id 2.2.2.2
 network 2.2.2.2 0.0.0.0 area 0
 network 10.10.10.2 0.0.0.0 area 0
 network 10.1.0.1 0.0.0.0 area 0
 network 10.2.0.1 0.0.0.0 area 0
 network 10.3.0.1 0.0.0.0 area 0
 passive-interface Loopback0

end
write memory
```

#### Router 3

```
enable
configure terminal

router ospf 1
 router-id 3.3.3.3
 network 3.3.3.3 0.0.0.0 area 0
 network 10.1.0.2 0.0.0.0 area 0
 passive-interface Loopback0

end
write memory
```

#### Router 4

```
enable
configure terminal

router ospf 1
 router-id 4.4.4.4
 network 4.4.4.4 0.0.0.0 area 0
 network 10.2.0.2 0.0.0.0 area 0
 passive-interface Loopback0

end
write memory
```

#### Router 5

```
enable
configure terminal

router ospf 1
 router-id 5.5.5.5
 network 5.5.5.5 0.0.0.0 area 0
 network 10.3.0.2 0.0.0.0 area 0
 passive-interface Loopback0

end
write memory
```

In OSPF, we have 3 router tables and 5 independent packet flows. I will explain it in the article below.

## Routing Table

All routers were configured for OSPF Area 0. Now I need to check the routing table on each router in the topology to confirm the changes.

A routing table is the router's map of where IP networks are located. This is the router's brain, deciding where to send packets next.

A routing table supplies common route codes:

&#x20;C = Connected network

L = Local interface address

O = Learned through OSPF

S = Static route

Regardless, this code is omitted from the presented script to make it easier to read.

Let's see what changed.

Router 1 routing table (some lines are omitted):

```
      1.0.0.0/32 is subnetted, 1 subnets
C        1.1.1.1 is directly connected, Loopback0
      2.0.0.0/32 is subnetted, 1 subnets
O        2.2.2.2 [110/11] via 10.10.10.2, 00:32:18, Ethernet0/1
      3.0.0.0/32 is subnetted, 1 subnets
O        3.3.3.3 [110/21] via 10.10.10.2, 00:25:36, Ethernet0/1
      4.0.0.0/32 is subnetted, 1 subnets
O        4.4.4.4 [110/21] via 10.10.10.2, 00:21:56, Ethernet0/1
      5.0.0.0/32 is subnetted, 1 subnets
O        5.5.5.5 [110/21] via 10.10.10.2, 00:10:32, Ethernet0/1
      10.0.0.0/8 is variably subnetted, 5 subnets, 2 masks
O        10.1.0.0/24 [110/20] via 10.10.10.2, 00:31:47, Ethernet0/1
O        10.2.0.0/24 [110/20] via 10.10.10.2, 00:31:40, Ethernet0/1
O        10.3.0.0/24 [110/20] via 10.10.10.2, 00:31:36, Ethernet0/1
C        10.10.10.0/24 is directly connected, Ethernet0/1
L        10.10.10.1/32 is directly connected, Ethernet0/1
Router-1#
```

Router 2 routing table (some lines are omitted):

```
      1.0.0.0/32 is subnetted, 1 subnets
O        1.1.1.1 [110/11] via 10.10.10.1, 00:34:07, Ethernet0/1
      2.0.0.0/32 is subnetted, 1 subnets
C        2.2.2.2 is directly connected, Loopback0
      3.0.0.0/32 is subnetted, 1 subnets
O        3.3.3.3 [110/11] via 10.1.0.2, 00:27:25, Ethernet0/2
      4.0.0.0/32 is subnetted, 1 subnets
O        4.4.4.4 [110/11] via 10.2.0.2, 00:23:50, Ethernet0/3
      5.0.0.0/32 is subnetted, 1 subnets
O        5.5.5.5 [110/11] via 10.3.0.2, 00:12:26, Ethernet1/0
      10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
C        10.1.0.0/24 is directly connected, Ethernet0/2
L        10.1.0.1/32 is directly connected, Ethernet0/2
C        10.2.0.0/24 is directly connected, Ethernet0/3
L        10.2.0.1/32 is directly connected, Ethernet0/3
C        10.3.0.0/24 is directly connected, Ethernet1/0
L        10.3.0.1/32 is directly connected, Ethernet1/0
C        10.10.10.0/24 is directly connected, Ethernet0/1
L        10.10.10.2/32 is directly connected, Ethernet0/1
Router-2#

```

Router 3 routing table (some lines are omitted):

```
      1.0.0.0/32 is subnetted, 1 subnets
O        1.1.1.1 [110/21] via 10.1.0.1, 00:28:33, Ethernet0/1
      2.0.0.0/32 is subnetted, 1 subnets
O        2.2.2.2 [110/11] via 10.1.0.1, 00:28:33, Ethernet0/1
      3.0.0.0/32 is subnetted, 1 subnets
C        3.3.3.3 is directly connected, Loopback0
      4.0.0.0/32 is subnetted, 1 subnets
O        4.4.4.4 [110/21] via 10.1.0.1, 00:24:53, Ethernet0/1
      5.0.0.0/32 is subnetted, 1 subnets
O        5.5.5.5 [110/21] via 10.1.0.1, 00:13:29, Ethernet0/1
      10.0.0.0/8 is variably subnetted, 5 subnets, 2 masks
C        10.1.0.0/24 is directly connected, Ethernet0/1
L        10.1.0.2/32 is directly connected, Ethernet0/1
O        10.2.0.0/24 [110/20] via 10.1.0.1, 00:28:33, Ethernet0/1
O        10.3.0.0/24 [110/20] via 10.1.0.1, 00:28:33, Ethernet0/1
O        10.10.10.0/24 [110/20] via 10.1.0.1, 00:28:33, Ethernet0/1
Router-3#
```

Router 4 routing table (some lines are omitted):

```
      1.0.0.0/32 is subnetted, 1 subnets
O        1.1.1.1 [110/21] via 10.2.0.1, 00:25:38, Ethernet0/1
      2.0.0.0/32 is subnetted, 1 subnets
O        2.2.2.2 [110/11] via 10.2.0.1, 00:25:38, Ethernet0/1
      3.0.0.0/32 is subnetted, 1 subnets
O        3.3.3.3 [110/21] via 10.2.0.1, 00:25:38, Ethernet0/1
      4.0.0.0/32 is subnetted, 1 subnets
C        4.4.4.4 is directly connected, Loopback0
      5.0.0.0/32 is subnetted, 1 subnets
O        5.5.5.5 [110/21] via 10.2.0.1, 00:14:10, Ethernet0/1
      10.0.0.0/8 is variably subnetted, 5 subnets, 2 masks
O        10.1.0.0/24 [110/20] via 10.2.0.1, 00:25:38, Ethernet0/1
C        10.2.0.0/24 is directly connected, Ethernet0/1
L        10.2.0.2/32 is directly connected, Ethernet0/1
O        10.3.0.0/24 [110/20] via 10.2.0.1, 00:25:38, Ethernet0/1
O        10.10.10.0/24 [110/20] via 10.2.0.1, 00:25:38, Ethernet0/1
Router-4#
```

Router 5 routing table (some lines are omitted):

```
      1.0.0.0/32 is subnetted, 1 subnets
O        1.1.1.1 [110/21] via 10.3.0.1, 00:14:53, Ethernet0/1
      2.0.0.0/32 is subnetted, 1 subnets
O        2.2.2.2 [110/11] via 10.3.0.1, 00:14:53, Ethernet0/1
      3.0.0.0/32 is subnetted, 1 subnets
O        3.3.3.3 [110/21] via 10.3.0.1, 00:14:53, Ethernet0/1
      4.0.0.0/32 is subnetted, 1 subnets
O        4.4.4.4 [110/21] via 10.3.0.1, 00:14:53, Ethernet0/1
      5.0.0.0/32 is subnetted, 1 subnets
C        5.5.5.5 is directly connected, Loopback0
      10.0.0.0/8 is variably subnetted, 5 subnets, 2 masks
O        10.1.0.0/24 [110/20] via 10.3.0.1, 00:14:53, Ethernet0/1
O        10.2.0.0/24 [110/20] via 10.3.0.1, 00:14:53, Ethernet0/1
C        10.3.0.0/24 is directly connected, Ethernet0/1
L        10.3.0.2/32 is directly connected, Ethernet0/1
O        10.10.10.0/24 [110/20] via 10.3.0.1, 00:14:53, Ethernet0/1
Router-5#
```

We can see the newly added route inside each routing table. For example, Router 5 shows 8 potential OSPF protocol routes on its Ethernet 0/1 interface.

## Neighbour Table

&#x20; The OSPF neighbour table shows routers that have formed an OSPF relationship on directly connected links. An OSPF neighbour table includes Neighbour ID and priority on this network. Priority helps elect the DR and BDR. A priority of 0 prevents a router from becoming the DR or BDR. The next column is state, which indicates OSPF database synchronisation.

As mentioned above, that relationship is the direct connection to the other OSPF routers with its local interfaces.

| Field                       | Explanation                                                                                                                                                    |
| --------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Neighbour ID — 2.2.2.2**  | The neighbour's **OSPF Router ID**. It is not necessarily the neighbour's interface IP address.                                                                |
| **Pri — 1**                 | The neighbour's OSPF interface priority, used to elect the **DR** and **BDR** on multi-access networks.                                                        |
| **State — FULL/BDR**        | `FULL` means the routers have synchronised their link-state databases. `BDR` means this neighbour is the **Backup Designated Router** on this network segment. |
| **Dead Time — 00:00:36**    | Time remaining before the neighbour is declared down if no Hello packet arrives. Receiving a Hello resets this timer.                                          |
| **Address — 10.10.10.2**    | The neighbour's interface IP address on the shared link.                                                                                                       |
| **Interface — Ethernet0/1** | The local router interface through which the neighbour is reached.                                                                                             |

Table 1 - Router 1 Neighbour Table

For example, Router 1 has one OSPF neighbour, Router-ID 2.2.2.2, with neighbour interface address 10.10.10.2, and can reach this connection through local interface Ethernet 0/1.

This does not mean every router in OSPF will appear in the Neighbour Table. Those additional distinctions (connections) which are not directly linked to the router are placed inside the Link State Database, which is the topology table described below in this article.

Router 1

```
Router-1#show ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/BDR        00:00:36    10.10.10.2      Ethernet0/1
Router-1#

```

Router 2

```
Router-2#show ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
5.5.5.5           1   FULL/BDR        00:00:35    10.3.0.2        Ethernet1/0
4.4.4.4           1   FULL/BDR        00:00:33    10.2.0.2        Ethernet0/3
3.3.3.3           1   FULL/BDR        00:00:33    10.1.0.2        Ethernet0/2
1.1.1.1           1   FULL/DR         00:00:31    10.10.10.1      Ethernet0/1
Router-2#
```

Router 3

```
Router-3#show ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/DR         00:00:33    10.1.0.1        Ethernet0/1
Router-3#
```

Router 4

```
Router-4#show ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/DR         00:00:36    10.2.0.1        Ethernet0/1
Router-4#
```

Router 5

```
Router-5#show ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/DR         00:00:32    10.3.0.1        Ethernet0/1
Router-5#
```

## Topology Table (LSDB)

The actual OSPF database, or Link State Database (LSDB), is a router topology table. This is topology information for the OSPF area. This table contains more information about the routers, networks, and links in OSPF. Each router in the same area should have an identical LSDB table. How is it built?

OSPF routers discover neighbours using Hello packets. Neighbours exchange Link State Advertisements (LSAs). Each router stores the LSAs in its LSDB. The router runs the Shortest Path First (SPF) algorithm and installs the shortest path in the router's routing table.

| Table                     | Purpose                                  | Command                 |
| ------------------------- | ---------------------------------------- | ----------------------- |
| **Neighbour table**       | Lists directly connected OSPF neighbours | `show ip ospf neighbor` |
| **Topology table / LSDB** | Describes the OSPF network topology      | `show ip ospf database` |
| **Routing table**         | Contains the best calculated routes      | `show ip route ospf`    |

Table 2 - OSPF commands to check router configuration.

Below, we can observe all OSPF routing between 5 routers in the topology shown. 

Router 1

```
      2.0.0.0/32 is subnetted, 1 subnets
O        2.2.2.2 [110/11] via 10.10.10.2, 00:03:45, Ethernet0/1
      3.0.0.0/32 is subnetted, 1 subnets
O        3.3.3.3 [110/21] via 10.10.10.2, 00:03:40, Ethernet0/1
      4.0.0.0/32 is subnetted, 1 subnets
O        4.4.4.4 [110/21] via 10.10.10.2, 00:03:40, Ethernet0/1
      5.0.0.0/32 is subnetted, 1 subnets
O        5.5.5.5 [110/21] via 10.10.10.2, 00:03:36, Ethernet0/1
      10.0.0.0/8 is variably subnetted, 5 subnets, 2 masks
O        10.1.0.0/24 [110/20] via 10.10.10.2, 00:03:45, Ethernet0/1
O        10.2.0.0/24 [110/20] via 10.10.10.2, 00:03:45, Ethernet0/1
O        10.3.0.0/24 [110/20] via 10.10.10.2, 00:03:40, Ethernet0/1
Router-1#
```

Router 2

```
      1.0.0.0/32 is subnetted, 1 subnets
O        1.1.1.1 [110/11] via 10.10.10.1, 00:05:04, Ethernet0/1
      3.0.0.0/32 is subnetted, 1 subnets
O        3.3.3.3 [110/11] via 10.1.0.2, 00:05:03, Ethernet0/2
      4.0.0.0/32 is subnetted, 1 subnets
O        4.4.4.4 [110/11] via 10.2.0.2, 00:05:03, Ethernet0/3
      5.0.0.0/32 is subnetted, 1 subnets
O        5.5.5.5 [110/11] via 10.3.0.2, 00:04:58, Ethernet1/0
Router-2#
```

Router 3

```
      1.0.0.0/32 is subnetted, 1 subnets
O        1.1.1.1 [110/21] via 10.1.0.1, 00:07:04, Ethernet0/1
      2.0.0.0/32 is subnetted, 1 subnets
O        2.2.2.2 [110/11] via 10.1.0.1, 00:07:04, Ethernet0/1
      4.0.0.0/32 is subnetted, 1 subnets
O        4.4.4.4 [110/21] via 10.1.0.1, 00:06:59, Ethernet0/1
      5.0.0.0/32 is subnetted, 1 subnets
O        5.5.5.5 [110/21] via 10.1.0.1, 00:06:57, Ethernet0/1
      10.0.0.0/8 is variably subnetted, 5 subnets, 2 masks
O        10.2.0.0/24 [110/20] via 10.1.0.1, 00:07:04, Ethernet0/1
O        10.3.0.0/24 [110/20] via 10.1.0.1, 00:06:59, Ethernet0/1
O        10.10.10.0/24 [110/20] via 10.1.0.1, 00:07:04, Ethernet0/1
Router-3#
```

Router 4

```
      1.0.0.0/32 is subnetted, 1 subnets
O        1.1.1.1 [110/21] via 10.2.0.1, 00:08:15, Ethernet0/1
      2.0.0.0/32 is subnetted, 1 subnets
O        2.2.2.2 [110/11] via 10.2.0.1, 00:08:15, Ethernet0/1
      3.0.0.0/32 is subnetted, 1 subnets
O        3.3.3.3 [110/21] via 10.2.0.1, 00:08:15, Ethernet0/1
      5.0.0.0/32 is subnetted, 1 subnets
O        5.5.5.5 [110/21] via 10.2.0.1, 00:08:07, Ethernet0/1
      10.0.0.0/8 is variably subnetted, 5 subnets, 2 masks
O        10.1.0.0/24 [110/20] via 10.2.0.1, 00:08:15, Ethernet0/1
O        10.3.0.0/24 [110/20] via 10.2.0.1, 00:08:10, Ethernet0/1
O        10.10.10.0/24 [110/20] via 10.2.0.1, 00:08:15, Ethernet0/1
Router-4#
```

Router 5

```
      1.0.0.0/32 is subnetted, 1 subnets
O        1.1.1.1 [110/21] via 10.3.0.1, 00:09:00, Ethernet0/1
      2.0.0.0/32 is subnetted, 1 subnets
O        2.2.2.2 [110/11] via 10.3.0.1, 00:09:00, Ethernet0/1
      3.0.0.0/32 is subnetted, 1 subnets
O        3.3.3.3 [110/21] via 10.3.0.1, 00:09:00, Ethernet0/1
      4.0.0.0/32 is subnetted, 1 subnets
O        4.4.4.4 [110/21] via 10.3.0.1, 00:09:00, Ethernet0/1
      10.0.0.0/8 is variably subnetted, 5 subnets, 2 masks
O        10.1.0.0/24 [110/20] via 10.3.0.1, 00:09:00, Ethernet0/1
O        10.2.0.0/24 [110/20] via 10.3.0.1, 00:09:00, Ethernet0/1
O        10.10.10.0/24 [110/20] via 10.3.0.1, 00:09:00, Ethernet0/1
Router-5#
```
