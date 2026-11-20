+++
title = "OSPF Dynamic Routing Protocol"
date = 2026-11-19T21:02:24.792Z
description = "Introduction to the Open Shortest Path First protocol. "
tags = [ "Networking" ]
+++

I started updating the OSPF topology with a few selected Cisco routers deployed in ContainerLab. ContainerLab is amazing when you configure it with the VS Code extension. 

VS Code give us full control from the user GUI, which speeds up the experimentation process; in this case, I tried OSPF routing. 

![](/images/ospf-five-routers/vs-code.png)

Below, you can see a five-router topology configured with interfaces to IP addresses: 

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

![](/images/ospf-five-routers/2026-11-20_19-20.png)Picture 1 - Five-router topology 

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
