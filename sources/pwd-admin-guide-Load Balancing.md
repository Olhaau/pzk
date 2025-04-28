---
title: Load Balancing – Administration Guide
keywords: source
---

# Load Balancing – Administration Guide

url:: https://docs.posit.co/ide/server-pro/load_balancing/load_balancing.html
up: [[sources]]

[Workbench](https://docs.posit.co/ide/server-pro/load_balancing/../#intended-audience "This feature is only available with Posit Workbench.") | [Enhanced](https://docs.posit.co/ide/server-pro/load_balancing/../license_management/license_management.html#product-tiers "This feature is only available with the purchase of the Enhanced or Advanced product tier. Click the badge for additional info.") [Advanced](https://docs.posit.co/ide/server-pro/load_balancing/../license_management/license_management.html#product-tiers "This feature is only available with the purchase of the Advanced product tier. Click the badge for additional info.")

## Overview

Posit Workbench can be configured to load balance sessions across two or more nodes within a cluster. This provides both increased capacity as well as higher availability.

Use of more than two nodes per cluster requires an Advanced license. Contact Posit Support for more information.

Load balancing with Posit Workbench **always** operates in an active-active fashion where all nodes are equally equipped to serve users. All nodes have a primary role.

Note that load balancing for Posit Workbench has some particular “stickiness” requirements stemming from the fact that users must always return to the same session where their work resides (i.e. their traffic can’t be handled by more than one node). As a result, it’s not enough to simply place multiple Posit Workbench servers behind a conventional hardware or software load balancer—additional intelligence and routing is required.

Key characteristics of the Posit Workbench load balancer include:

1. Multiple primary nodes for high availability - all nodes can balance traffic to all other nodes.

2. Support for several load balancing strategies including least busy server by active sessions or system load, even distribution by user, or a custom strategy based on an external script.

3. The ability to add and remove nodes while the cluster is running.

4. Works standalone or can be integrated with other front-end load balancing environments.

The standalone load balance and higher availability characteristics of Posit Workbench are an exception among Posit products. Posit Connect and Shiny Server will require a front-end load balancing under the same scenarios. The use of an external load balancer can still be beneficial in a failover setup. See [External Load Balancers](https://docs.posit.co/ide/server-pro/load_balancing/../load_balancing/access_and_availability.html#external-load-balancers) for details.
Back to top
