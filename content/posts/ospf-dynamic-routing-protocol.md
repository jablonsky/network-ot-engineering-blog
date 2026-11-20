+++
title = "OSPF Dynamic Routing Protocol"
date = 2026-11-19T21:02:24.792Z
description = "Introduction to the Open Shortest Path First protocol. "
tags = [ "Networking" ]
+++

I started updating the OSPF topology with a few selected Cisco routers deployed in ContainerLab. ContainerLab is amazing when you configure it with the VS Code extension.

VS Code give us full control from the user GUI, which speeds up the experimentation process; in this case, I tried OSPF routing in a single area 0 (backbone).

![VS Code Editor](/images/ospf-five-routers/vs-code.png "Picture 1 - VS Code Editor with Containerlab extension")

Picture 1 - VS Code with running Containerlab extension

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

![Five-router topology](/images/ospf-five-routers/2026-11-20_19-20.png#medium-large "Picture 2 - Five-router topology")Picture 2 - Five-router topology

Let's configure a single OSPF area. Each router is located in Area 0. The commands below use the exact interface IP addresses:

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

Each router was configured for OSPF Area 0. Now we must visit the updated routing table on each router from the topology. A routing table is the router's map of where IP networks are located. This is the router's brain, deciding where to send packets next. 

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
