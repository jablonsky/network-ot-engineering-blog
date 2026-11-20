+++
title = "OSPF Dynamic Routing Protocol"
date = 2026-11-19T21:02:24.792Z
description = "Introduction to the Open Shortest Path First protocol. "
tags = [ "Networking" ]
+++

I started updating the OSPF topology with a few selected Cisco routers deployed in ContainerLab. ContainerLab is amazing when you configure it with the VS Code extension. 

VS Code give us full control from the user GUI, which speeds up the experimentation process; in this case, I tried OSPF routing. 

![](/images/ospf-five-routers/vs-code.png)

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
