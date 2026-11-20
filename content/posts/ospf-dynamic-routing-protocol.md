+++
title = "OSPF Dynamic Routing Protocol"
date = 2026-11-19T21:02:24.792Z
description = "Introduction to the Open Shortest Path First protocol. "
tags = [ "Networking" ]
+++

locatedI started updating the OSPF topology with a few selected Cisco routers deployed in ContainerLab. ContainerLab is amazing when you configure it with the VS Code extension.

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

![Five-router topology](/images/ospf-five-routers/2026-11-20_19-20.png#large "Picture 2 - Five-router topology")Picture 2 - Five-router topology

Let's configure a single OSPF area. Each router is localted in Area 0. The commands below use the exact interface IP addresses:

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
