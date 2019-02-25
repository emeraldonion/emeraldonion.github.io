---
author: emeraldonion
comments: false
date: 2017-07-22 07:20:54+00:00
layout: post
link: https://emeraldonion.org/tor-router-configuration-v2/
slug: tor-router-configuration-v2
title: Tor router configuration v2
wordpress_id: 263
categories:
- Infrastructure
---

# 22 July 2017


We are rearchitecting our network by eliminating the use of our ISP-provisioned /27 IP scope in order to utilize our ARIN-assigned /24. Doing so allows us to route across multiple networks with the same ASN, a requirement in order to use our ARIN-assigned IPv6 scope. For network simplification, we are also eliminating the use of NAT.


## Tor router #1



    
    Nickname EmeraldOnion01
    Address tor01.emeraldonion.org
    ContactInfo abuse_at_emeraldonion_dot_org
    OutboundBindAddressExit 23.129.64.11
    OutboundBindAddressOR 23.129.64.11
    DirPort 23.129.64.11:80
    ORPort 23.129.64.11:443
    ORPort [2620:18c:0:1100::1]:443
    RelayBandwidthRate 27.5 MBytes
    RelayBandwidthBurst 27.5 MBytes
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
    RelayBandwidthRate 27.5 MBytes
    RelayBandwidthBurst 27.5 MBytes
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
    RelayBandwidthRate 27.5 MBytes
    RelayBandwidthBurst 27.5 MBytes
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
    RelayBandwidthRate 27.5 MBytes
    RelayBandwidthBurst 27.5 MBytes
    IPv6Exit 1
    Exitpolicy accept *:*
    ExitPolicy accept6 *:*
