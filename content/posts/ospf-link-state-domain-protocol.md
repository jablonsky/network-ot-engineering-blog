+++
title = "OSPF Link State Domain Protocol - Part 2"
date = 2026-11-28T21:15:57.185Z
description = "Part 2 covers the area concept in OSPF and how it works."
tags = [ "OSPF" ]
+++

The information about topology is flooding, or rather, being exchanged between routers. As I explained in part 1, routers exchange information using LSA records (Link State Advertisements). Each router must maintain its own database (Link State Database), so it has an exact copy to build the shortest path later.

To propagate changes in each router's topology database, we need a mesh connection for redundancy. Besides, it requires high hardware capacity. The OSPF protocol provides a solution through a link-state domain model, where routers exchange information to maintain the same LSDB records. OSPF introduces an area model in which segments of the network are divided, and information is propagated in a controlled flow.
