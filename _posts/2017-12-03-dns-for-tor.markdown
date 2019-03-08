---
author: xanaduregio
date: 2017-12-03 00:15:55+00:00
layout: post
title: DNS for Tor Exit Relaying
tags:
- DNS
- Education
- Infrastructure
- pfSense
---

One of the major pieces of infrastructure run by Tor Exit Nodes is DNS. DNS is the system that translates human readable names, like `emeraldonion.org`, into IP addresses. In Tor, the exit node is the location where this translation takes place. As such, DNS has been [recognized](https://lists.torproject.org/pipermail/metrics-team/2016-February/000078.html) as one of the places where centralization or attacks could be performed that could affect the integrity of the Tor network. To serve our users well, we want to mitigate the risks of compromise and surveillance as we resolve names on behalf of our users. It's these principles that direct how we structure our DNS resolution.



Emerald Onion currently uses pfSense, which uses [Unbound](https://unbound.net/) for DNS. Per our architectural design, we run our own recursive DNS server, meaning we query up to the [root name servers](https://en.wikipedia.org/wiki/Root_name_server) for DNS resolution, and avoid the cache any upstream ISPs offering us DNS resolvers. This also means we query the authoritative resolvers directly, minimizing the number of additional parties able to observe domain resolutions coming from our users.


## General Settings:





 	
  * We use DNS Resolver and disable the DNS Forwarder

 	
  * Only bind the DNS listener to the NIC the Tor server is connected to and localhost.

 	
  * Only bind the DNS outgoing interface to the NIC that carries our public IP. If you use BGP, do NOT bind DNS to the interface used to connect to BGP peers.

 	
  * Enable DNSSEC support

 	
  * Disable DNS Query Forwarding

 	
  * We don't use DHCP, leave DHCP Registration disabled

 	
  * Same goes with Static DHCP




## Custom options:



    
    prefer-ip6: yes
    hide-trustanchor: yes
    harden-large-queries: yes
    harden-algo-downgrade: yes
    qname-minimisation-strict: yes
    ignore-cd-flag: yes


The most important of these options is `qname-minimization`, which means that when we perform a resolution like `www.emeraldonion.org`, we ask the root name servers only for who controls `.org`, the Org name servers, who controls `emeraldonion.org`, and only ask emerald onion's name servers for the IP of `www.emeraldonion.org`. This helps to protect against our traffic resolutions being swept into the various "passive DNS" feeds that have been commoditized around the network.

Of the other custom options, the bulk are related to DNSSEC security.



[![](/images/general.jpg)](/images/general.jpg)


## Advanced Settings:





 	
  * Hide Identity

 	
  * Hide Version

 	
  * Use Prefetch Support

 	
  * Use Prefetch DNS Keys

 	
  * Harden DNSSEC data

 	
  * Leave the tuning values alone for now (Things like cache size, buffers, queues, jostle, etc)

 	
  * Log Level is 1, which is pretty low.

 	
  * Leave the rest alone.


Hiding the identity and version helps prevent the leakage of information that could be used in attacks against us. Prefetch Support changes how the DNS server fetches DNS records. Without it, it fetches the DNS record at the time of the request. With Prefetch Support, it refreshes the DNS entries as each record's TTL expires helping to further obfuscate requests and makes it harder for specific Tor request correlation attacks.

![](/images/screencapture-23-129-64-2-services_unbound_advanced-php-1513541167725.png)


## Access Lists:


We don't use this, but if you want to work with the Access Lists, that should be fine, just keep it in mind when troubleshooting.
