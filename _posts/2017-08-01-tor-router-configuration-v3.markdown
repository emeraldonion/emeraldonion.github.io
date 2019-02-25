---
author: emeraldonion
comments: false
date: 2017-08-01 07:22:48+00:00
layout: post
link: https://emeraldonion.org/tor-router-configuration-v3/
slug: tor-router-configuration-v3
title: Tor router configuration v3
wordpress_id: 265
categories:
- Infrastructure
---

# 01 August 2017


We experienced a catastrophic hardware failure recently which will be detailed in an upcoming blog post. We are back online today with new router IDs and we added two more routers for a total of six Tor routers.

We moved to Google Cloud DNS recently to be able to manage our PTR records for reverse DNS since we have our own IP scopes now. We also moved our forward-lookup zone to Google Cloud DNS. Next on the agenda is setting up DNSSEC.


## IPv6 PTR



    
    1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.1.1.0.0.0.0.c.8.1.0.0.2.6.2.ip6.arpa.
    1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.2.1.0.0.0.0.c.8.1.0.0.2.6.2.ip6.arpa.
    1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.3.1.0.0.0.0.c.8.1.0.0.2.6.2.ip6.arpa.
    1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.4.1.0.0.0.0.c.8.1.0.0.2.6.2.ip6.arpa.
    1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.5.1.0.0.0.0.c.8.1.0.0.2.6.2.ip6.arpa.
    1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.6.1.0.0.0.0.c.8.1.0.0.2.6.2.ip6.arpa.




## DNS



    
    2620:18c:0:1100::1 tor01.emeraldonion.org
    2620:18c:0:1200::1 tor02.emeraldonion.org
    2620:18c:0:1300::1 tor03.emeraldonion.org
    2620:18c:0:1400::1 tor04.emeraldonion.org
    2620:18c:0:1500::1 tor05.emeraldonion.org
    2620:18c:0:1600::1 tor06.emeraldonion.org



    
    23.129.64.11 tor01.emeraldonion.org
    23.129.64.12 tor02.emeraldonion.org
    23.129.64.13 tor03.emeraldonion.org
    23.129.64.14 tor04.emeraldonion.org
    23.129.64.15 tor05.emeraldonion.org
    23.129.64.16 tor06.emeraldonion.org




## Tor router #1



    
    Nickname EmeraldOnion01
    Address tor01.emeraldonion.org
    ContactInfo abuse_at_emeraldonion_dot_org
    OutboundBindAddressExit 23.129.64.11
    OutboundBindAddressOR 23.129.64.11
    DirPort 23.129.64.11:80
    ORPort 23.129.64.11:443
    ORPort [2620:18c:0:1100::1]:443
    RelayBandwidthRate 18 MBytes
    RelayBandwidthBurst 18 MBytes
    IPv6Exit 1
    Exitpolicy accept *:*
    ExitPolicy accept6 *:*




## Tor router #2



    
    Nickname EmeraldOnion02
    Address tor02.emeraldonion.org
    ContactInfo abuse_at_emeraldonion_dot_org
    OutboundBindAddressExit 23.129.64.12
    OutboundBindAddressOR 23.129.64.12
    DirPort 23.129.64.12:80
    ORPort 23.129.64.12:443
    ORPort [2620:18c:0:1200::1]:443
    RelayBandwidthRate 18 MBytes
    RelayBandwidthBurst 18 MBytes
    IPv6Exit 1
    Exitpolicy accept *:*
    ExitPolicy accept6 *:*




## Tor router #3



    
    Nickname EmeraldOnion03
    Address tor03.emeraldonion.org
    ContactInfo abuse_at_emeraldonion_dot_org
    OutboundBindAddressExit 23.129.64.13
    OutboundBindAddressOR 23.129.64.13
    DirPort 23.129.64.13:80
    ORPort 23.129.64.13:443
    ORPort [2620:18c:0:1300::1]:443
    RelayBandwidthRate 18 MBytes
    RelayBandwidthBurst 18 MBytes
    IPv6Exit 1
    Exitpolicy accept *:*
    ExitPolicy accept6 *:*




## Tor router #4



    
    Nickname EmeraldOnion04
    Address tor04.emeraldonion.org
    ContactInfo abuse_at_emeraldonion_dot_org
    OutboundBindAddressExit 23.129.64.14
    OutboundBindAddressOR 23.129.64.14
    DirPort 23.129.64.14:80
    ORPort 23.129.64.14:443
    ORPort [2620:18c:0:1400::1]:443
    RelayBandwidthRate 18 MBytes
    RelayBandwidthBurst 18 MBytes
    IPv6Exit 1
    Exitpolicy accept *:*
    ExitPolicy accept6 *:*




## Tor router #5



    
    Nickname EmeraldOnion05
    Address tor05.emeraldonion.org
    ContactInfo abuse_at_emeraldonion_dot_org
    OutboundBindAddressExit 23.129.64.15
    OutboundBindAddressOR 23.129.64.15
    DirPort 23.129.64.15:80
    ORPort 23.129.64.15:443
    ORPort [2620:18c:0:1500::1]:443
    RelayBandwidthRate 18 MBytes
    RelayBandwidthBurst 18 MBytes
    IPv6Exit 1
    Exitpolicy accept *:*
    ExitPolicy accept6 *:*




## Tor router #6



    
    Nickname EmeraldOnion06
    Address tor06.emeraldonion.org
    ContactInfo abuse_at_emeraldonion_dot_org
    OutboundBindAddressExit 23.129.64.16
    OutboundBindAddressOR 23.129.64.16
    DirPort 23.129.64.16:80
    ORPort 23.129.64.16:443
    ORPort [2620:18c:0:1600::1]:443
    RelayBandwidthRate 18 MBytes
    RelayBandwidthBurst 18 MBytes
    IPv6Exit 1
    Exitpolicy accept *:*
    ExitPolicy accept6 *:*




## Starting the processes



    
    sudo service tor@tor01 start
    sudo service tor@tor02 start
    sudo service tor@tor03 start
    sudo service tor@tor04 start
    sudo service tor@tor05 start
    sudo service tor@tor06 start
