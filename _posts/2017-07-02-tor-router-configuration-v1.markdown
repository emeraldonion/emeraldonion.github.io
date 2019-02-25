---
author: emeraldonion
comments: false
date: 2017-07-02 07:00:58+00:00
layout: post
link: https://emeraldonion.org/tor-router-configuration-v1/
slug: tor-router-configuration-v1
title: Tor router configuration v1
wordpress_id: 100
categories:
- Infrastructure
---

# 2 July 2017


We have started [four Tor exit routers](https://atlas.torproject.org/#search/EmeraldOnion) to saturate our unmetered-1Gbps, 10Gbps-burstable link.


## DNS



    
    216.176.186.131 tor01.emeraldonion.org
    216.176.186.132 tor02.emeraldonion.org
    216.176.186.133 tor03.emeraldonion.org
    216.176.186.134 tor04.emeraldonion.org




# Create instances



    
    sudo tor-instance-create tor01
    sudo tor-instance-create tor02
    sudo tor-instance-create tor03
    sudo tor-instance-create tor04




## Tor router #1



    
    sudo vim /etc/tor/instances/tor01/torrc



    
    Nickname EmeraldOnion01
    Address tor01.emeraldonion.org
    ContactInfo abuse@emeraldonion.org
    OutboundBindAddressExit 10.10.10.101
    OutboundBindAddressOR 10.10.10.101
    DirPort 216.176.186.131:80 NoListen
    DirPort 10.10.10.101:80 NoAdvertise
    ORPort 216.176.186.131:443 NoListen
    ORPort 10.10.10.101:443 NoAdvertise
    #ORPort [2620:18c:0:100::1]:443
    RelayBandwidthRate 50 MBytes
    RelayBandwidthBurst 50 MBytes
    #IPv6Exit 1
    Exitpolicy accept *:*
    #ExitPolicy accept6 *:*




## Tor router #2



    
    sudo vim /etc/tor/instances/tor02/torrc



    
    Nickname EmeraldOnion02
    Address tor02.emeraldonion.org
    ContactInfo abuse@emeraldonion.org
    OutboundBindAddressExit 10.10.10.102
    OutboundBindAddressOR 10.10.10.102
    DirPort 216.176.186.132:80 NoListen
    DirPort 10.10.10.102:80 NoAdvertise
    ORPort 216.176.186.132:443 NoListen
    ORPort 10.10.10.102:443 NoAdvertise
    #ORPort [2620:18c:0:200::1]:443
    RelayBandwidthRate 20 MBytes
    RelayBandwidthBurst 20 MBytes
    #IPv6Exit 1
    Exitpolicy accept *:*
    #ExitPolicy accept6 *:*




## Tor router #3



    
    sudo vim /etc/tor/instances/tor03/torrc



    
    Nickname EmeraldOnion03
    Address tor03.emeraldonion.org
    ContactInfo abuse@emeraldonion.org
    OutboundBindAddressExit 10.10.10.103
    OutboundBindAddressOR 10.10.10.103
    DirPort 216.176.186.133:80 NoListen
    DirPort 10.10.10.103:80 NoAdvertise
    ORPort 216.176.186.133:443 NoListen
    ORPort 10.10.10.103:443 NoAdvertise
    #ORPort [2620:18c:0:300::1]:443
    RelayBandwidthRate 20 MBytes
    RelayBandwidthBurst 20 MBytes
    #IPv6Exit 1
    Exitpolicy accept *:*
    #ExitPolicy accept6 *:*




## Tor router #4



    
    sudo vim /etc/tor/instances/tor04/torrc



    
    Nickname EmeraldOnion04
    Address tor04.emeraldonion.org
    ContactInfo abuse@emeraldonion.org
    OutboundBindAddressExit 10.10.10.104
    OutboundBindAddressOR 10.10.10.104
    DirPort 216.176.186.134:80 NoListen
    DirPort 10.10.10.104:80 NoAdvertise
    ORPort 216.176.186.134:443 NoListen
    ORPort 10.10.10.104:443 NoAdvertise
    #ORPort [2620:18c:0:400::1]:443
    RelayBandwidthRate 20 MBytes
    RelayBandwidthBurst 20 MBytes
    #IPv6Exit 1
    Exitpolicy accept *:*
    #ExitPolicy accept6 *:*




## Start instances



    
    sudo systemctl start tor@tor01
    sudo systemctl start tor@tor02
    sudo systemctl start tor@tor03
    sudo systemctl start tor@tor04




## Check logs



    
    sudo journalctl --boot -u tor@tor01.service
    sudo journalctl --boot -u tor@tor02.service
    sudo journalctl --boot -u tor@tor03.service
    sudo journalctl --boot -u tor@tor04.service




## Tor changes + reloading



    
    sudo service tor@tor01 reload
    sudo service tor@tor02 reload
    sudo service tor@tor03 reload
    sudo service tor@tor04 reload
