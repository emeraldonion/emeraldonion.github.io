---
author: xanaduregio
date: 2017-07-22 21:31:30+00:00
layout: post
title: EO pfSense Conversion plan
---

Currently pfSense is setup in a traditional 1 route (Default Gateway) configuration that you might find in a small business or home. Meaning it has a WAN port and a LAN port. The WAN port is currently set to have a default route of the upstream ISP provider.

Introducing BGP requires a fundamental change in architecture such that there are no default routes and that no Virtual IPs (VIPs) are placed on the BGP interfaces. Furthermore services like NTP and DNS need to be bound to the LAN port. The internal networking (LAN) needs to be reconfigured so that we are making use of the public IPv4 & IPv6 space directly allocated by our local internet registry (ARIN).

The overall design is to connect the router to two different uplinks. One as our transit provider (Wowrack, which connects to a blended network of Wavebroadband and Internap) and also to our local Internet Exchange (Seattle Internet Exchange (SIX)).

One thing we will discuss or add to this post later is more detail on our conversion away from NAT. Our reason for that conversion is simplification because it removes the need for VIPs and Inbound and Outbound NAT rules.

Execution Steps:



 	
  1. Before performing any upstream maintenance on the network, ensure that the Tor processes downstream have been gracefully shutdown.

 	
  2. Take a configuration backup of the pfSense router. This ensures if you get to a state that you cannot recover from, you can simply restore and come back online.

 	
  3. Remove the default gateway from the WAN interface.

 	
  4. Configure BGP with a neighbor for Wowrack

 	
  5. Re-IP the LAN interface to:

 	
    1. 23.129.64.1/24

 	
    2. 2620:18c:000:1000::1/52




 	
  6. Bond NTP & DNS to the LAN interface

 	
  7. Re-IP the interfaces on the Tor node and update the Tor process configuration files

 	
  8. Adjust all Firewall rules to reflect the new IP scheme and service bonding

 	
  9. Change the Firewall such that is it in a default closed state so that only specified IPs are allowed outbound.

 	
  10. Test ping from the Tor server outbound.

 	
  11. Test pinging the Tor server from outside the network.

 	
  12. Check routes via traceroute from outside the network to ensure proper routing.

 	
  13. Start Tor processes and validate operation


