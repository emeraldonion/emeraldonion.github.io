---
author: xanaduregio
date: 2018-06-30 07:00:52+00:00
layout: post
title: Edge routing with HardenedBSD
tags:
- BGP
- DNS
- HardenedBSD
- Infrastructure
- Internet
- Internet Exchange Points
---

This work would not have been possible without the help of Shawn (from [HardenedBSD](https://hardenedbsd.org/)), the determination of Jordan, Christian, Yawnbox, and the patience of the Seattle Internet Exchange.


# History


Emerald Onion set out to maximize the use of free/libre open source software for our compute environment, especially for the routing infrastructure (routing is actually all that we do). Before our partnership with HardenedBSD, we chose pfSense because we were most experienced with it. We had considered [OPNSense](https://opnsense.org/) but decided to move directly to HardenedBSD.

As part of our ongoing research and development for the enhancement of privacy infrastructure, we're actively working on identifying and testing open source hardware.


# Why did we move from pfSense to HardenedBSD?





 	
  * Misplaced ARPs in pfSense were occurring, for unknown reasons, and could not be mitigated

 	
  * Overall software minimization and attack surface reduction

 	
  * A "common operating environment" -- to have the same OS and application stack as our Tor router

 	
  * Flexibility, like being able to use the most up-to-date software packages and latest features

 	
  * Overall greater diversity and security for the Tor network




# [HardenedBSD](https://hardenedbsd.org/) - base operating system configuration


For our current configuration, we are using stock HBSD 11-stable.


###### rc.conf



    
    ipv6_gateway_enable="YES"
    gateway_enable="YES"
    clear_tmp_enable="YES"
    syslogd_flags="-ss"
    sendmail_enable="NONE"
    hostname="eo-pf-01.emeraldonion.org"
    unbound_enable="YES"
    sshd_enable="YES"
    ntpd_enable="YES"
    openbgpd_enable="YES"
    powerd_enable="YES"
    vnstat_enable="YES"
    # Set dumpdev to "AUTO" to enable crash dumps, "NO" to disable
    dumpdev="NO"
    zfs_enable="YES"
    
    ipv6_activate_all_interfaces="YES"
    ifconfig_ix0="inet 23.129.64.1/24"
    ifconfig_ix0_ipv6="inet6 2620:18c::1 prefixlen 36"
    
    pf_enable="YES"
    pf_rules="/usr/local/emeraldonion/etc/pf.rules"
    pflog_enable="NO"
    
    # Unused
    ifconfig_igb0="down"
    ifconfig_igb1="down"
    
    # SIX
    ifconfig_ix1="inet 206.81.81.158/23 group eo_egress"
    ifconfig_ix1_ipv6="inet6 2001:504:16::6:cdb prefixlen 64 accept_rtadv"




###### sysctl.conf



    
    # $FreeBSD$
    #
    # This file is read when going to multi-user and its contents piped thru
    # ``sysctl'' to adjust kernel values. ``man 5 sysctl.conf'' for details.
    #
    
    # Uncomment this to prevent users from seeing information about processes that
    # are being run under another UID.
    #security.bsd.see_other_uids=0
    security.bsd.see_other_uids=0
    security.bsd.see_other_gids=0
    security.bsd.unprivileged_read_msgbuf=0
    security.bsd.unprivileged_proc_debug=0
    security.bsd.stack_guard_page=1
    
    # SIX requirement
    net.link.ether.inet.max_age=14400
    
    # Allow router solicitations whilst being a router
    net.inet6.ip6.rfc6204w3=1




###### loader.conf



    
    aesni_load="YES"
    geom_eli_load="YES"
    geli_nvd0p5_keyfile0_load="YES"
    geli_nvd0p5_keyfile0_type="nvd0p5:geli_keyfile0"
    geli_nvd0p5_keyfile0_name="/boot/encryption.key"
    vfs.root.mountfrom="zfs:zroot/ROOT/default"
    kern.geom.label.disk_ident.enable="0"
    kern.geom.label.gptid.enable="0"
    vfs.zfs.min_auto_ashift=12
    zpool_cache_load="YES"
    zpool_cache_type="/boot/zfs/zpool.cache"
    zpool_cache_name="/boot/zfs/zpool.cache"
    geom_eli_passphrase_prompt="YES"
    zfs_load="YES"




# [PF](https://en.wikipedia.org/wiki/PF_(firewall)) - routing and firewall configuration



    
    #########
    #
    # Emerald Onion 
    #
    #########
    #
    ###### Variables and data used throughout this document:
    #
    ### Dynamic tables
    table <hardened_updates> persist
    table <hardened_updates6> persist
    table <ssh_permitted_hosts> { } persist
    table <tor_instances> const { 23.129.64.0/24, !23.129.64.1 } persist
    table <tor_instances6> const { 2620:18c::0/36, !2620:18c::1 } persist
    
    # SIX BGP sources
    h4_six_bgp="{ 206.81.80.0/23 }"
    h6_six_bgp="{ 2001:504:16::0/64 }"
    h4_six_bgp_nonat="206.81.80.0/23"
    h6_six_bgp_nonat="2001:504:16::0/64"
    
    # Private addresses
    h_tor_relay="23.129.64.10"
    
    ### Ports of note
    p_ssh = "22"
    p_dns = "53"
    p_hbsd_update = "{ 80 443 }"
    p_tor = "{ 80 443 }"
    p_ntp = "123"
    p_bgp = "179"
    p_internal_permitted = "{" $p_ntp $p_dns "}"
    
    ### Interfaces - Outside
    i_six = "ix1"
    # functionally equivalent to eo_egress interface group - need to collapse rules using this alias
    i_wan = "{" $i_six "}"
    a_wan = "23.129.64.1" 
    a6_wan = "2620:18c::1"
    
    ### Interfaces - Inside
    i_lan = "{" ix0 "}"
    
    ### Permissible ICMP
    icmp_types = "{ echoreq unreach }"
    icmp6_types = "{ toobig echoreq neighbrsol neighbradv }"
    i_wan_icmp6_types="{ echoreq listendone routeradv neighbrsol neighbradv redir }"
    
    #
    ###### Options section
    # Do not process loopback traffic
    set skip on lo0
    set limit { states 200000, frags 40000, src-nodes 50000 }
    
    # Scrub (normalize) packets to ensure consistent rule assignment
    scrub in all
    
    #
    ###### NAT/RDR
    #
    ### NAT Rules
    # Anything sent out by the host to a BGP peer should use the native address. Everything else should NAT to the advertised address
    nat on $i_six inet from ($i_six) to ! $h4_six_bgp_nonat -> $a_wan
    nat on $i_six inet6 from ($i_six) to ! $h6_six_bgp_nonat -> $a6_wan
    
    #
    ### Port Forwarding (RDR)
    # Remember your filter rules below! These rules only translate incoming traffic, the filter rules decide what is allowed....
    # These two rules should be collapsed to use eo_egress...
    rdr on $i_six inet proto tcp from any to $a_wan port $p_ssh -> $h_tor_relay port $p_ssh
    rdr on $i_six inet proto tcp from any to $a_wan port = 2222 -> $a_wan port $p_ssh
    
    #
    ###### Filter
    #
    # Strict default policy
    block all
    
    # Block spurious emissions
    block quick on $i_six inet6 from 2620:18c::1/128 to any
    
    # Permit DNS requests from anything - interface group eo_egress includes $i_wow and $i_six
    pass out quick on eo_egress inet proto { tcp udp } to any port = $p_dns keep state 
    pass out quick on eo_egress inet6 proto { tcp udp } to any port = $p_dns keep state
    
    # Permit openBGPd
    pass out quick on $i_six inet proto tcp from ($i_six) to $h4_six_bgp port = $p_bgp keep state
    pass out quick on $i_six inet6 proto tcp from ($i_six) to $h6_six_bgp port = $p_bgp keep state
    pass in quick on $i_six inet proto tcp from $h4_six_bgp to ($i_six) port = $p_bgp keep state
    pass in quick on $i_six inet6 proto tcp from $h6_six_bgp to ($i_six) port = $p_bgp keep state
    
    # Permit SSH from authorized...
    pass in quick on eo_egress inet proto tcp from <ssh_permitted_hosts> to $h_tor_relay port $p_ssh keep state
    pass in quick on eo_egress inet proto tcp from <ssh_permitted_hosts> to $a_wan port $p_ssh keep state
    pass out quick on ix0 inet proto tcp from <ssh_permitted_hosts> to $h_tor_relay port $p_ssh keep state
    # ...and block from anyone else
    block in quick on eo_egress inet proto tcp from any to any port $p_ssh 
    block in quick on eo_egress inet6 proto tcp from any to any port $p_ssh
    
    # Permit anything to the Tor servers
    pass in quick on eo_egress inet from any to <tor_instances> 
    pass out quick on ix0 inet from any to <tor_instances> 
    pass in quick on ix0 inet from <tor_instances> to !(ix0) 
    pass out quick on eo_egress inet from <tor_instances> to any 
    pass in quick on eo_egress inet6 from any to <tor_instances6> 
    pass out quick on ix0 inet6 from any to <tor_instances6> 
    pass in quick on ix0 inet6 from <tor_instances6> to !(ix0) 
    pass out quick on eo_egress inet6 from <tor_instances6> to any
    
    # Allow Tor traffic to transit the router but do not allow it towards our management network
    #pass in quick on ix0 from ix0:network to !(ix0) 
    #pass out quick on eo_egress from ix0:network to any 
    ## IPv6 address hasn't been configured on this interface yet
    #pass in quick on ix0 inet6 from ix0:network to !(ix0) 
    #pass out quick on $i_wan inet6 from ix0:network to any
    
    ## Permitted egress traffic
    # Hardened BSD Updates
    # Script provided to update <hardened_updates> and <hardened_updates6> tables on-demand although the addresses should be relatively stable
    # Any host is allowed to these servers on 80/443
    pass out quick on eo_egress inet proto tcp from $a_wan to <hardened_updates> port $p_hbsd_update keep state
    #pass out quick on eo_egress inet6 proto tcp from $a_wan to <hardened_updates6> port $p_hbsd_update keep state
    
    # Permit ICMP facilities
    pass out quick on eo_egress inet proto icmp all icmp-type $icmp_types keep state
    pass out quick on eo_egress inet6 proto ipv6-icmp all icmp6-type $icmp6_types keep state
    pass in quick on eo_egress inet proto icmp all icmp-type $icmp_types keep state
    pass in quick on eo_egress inet6 proto ipv6-icmp icmp6-type $icmp6_types keep state
    pass in quick on $i_six inet6 proto ipv6-icmp from any to { ($i_six) ff02::1/16 } icmp6-type $i_wan_icmp6_types keep state
    # And for internal services...
    pass quick on $i_lan inet proto icmp all icmp-type $icmp_types keep state
    pass quick on $i_lan inet6 proto ipv6-icmp all icmp6-type $icmp6_types keep state
    
    # Permit DNS and NTP requests to us from our internal interfaces
    pass in quick on $i_lan inet proto { tcp udp } from ix0:network to (ix0) port $p_internal_permitted keep state
    
    # Protect the router from internal users - block everything else (not DNS or NTP requests)
    block in quick on ix0 from ix0:network to (ix0)




# [Unbound](https://en.wikipedia.org/wiki/Unbound_(DNS_server)) - DNS configuration



    
    #
    # See unbound.conf(5) man page, version 1.7.0.
    #
    
    # The server clause sets the main parameters.
    server:
    # whitespace is not necessary, but looks cleaner.
    
    # verbosity number, 0 is least verbose. 1 is default.
    verbosity: 1
    
    # number of threads to create. 1 disables threading.
    num-threads: 16
    
    # specify the interfaces to answer queries from by ip-address.
    # The default is to listen to localhost (127.0.0.1 and ::1).
    # specify 0.0.0.0 and ::0 to bind to all available interfaces.
    # specify every interface[@port] on a new 'interface:' labelled line.
    # The listen interfaces are not changed on reload, only on restart.
    interface: 0.0.0.0
    
    # control which clients are allowed to make (recursive) queries
    # to this server. Specify classless netblocks with /size and action.
    # By default everything is refused, except for localhost.
    access-control: 23.129.64.1/24 allow
    
    # file to read root hints from.
    # get one from https://www.internic.net/domain/named.cache
    root-hints: "/usr/local/etc/unbound/root.hints"
    
    # enable to not answer id.server and hostname.bind queries.
    hide-identity: yes
    
    # enable to not answer version.server and version.bind queries.
    hide-version: yes
    
    # Sent minimum amount of information to upstream servers to enhance
    # privacy. Only sent minimum required labels of the QNAME and set QTYPE
    # to A when possible.
    qname-minimisation: yes
    
    # Use 0x20-encoded random bits in the query to foil spoof attempts.
    # This feature is an experimental implementation of draft dns-0x20.
    use-caps-for-id: yes
    
    # if yes, Unbound rotates RRSet order in response.
    rrset-roundrobin: yes
    
    # Python config section. To enable:
    # o use --with-pythonmodule to configure before compiling.
    # o list python in the module-config string (above) to enable.
    # o and give a python-script to run.
    python:
    
    # Remote control config section.
    remote-control:




# [OpenBGPD](https://en.wikipedia.org/wiki/OpenBGPD) - BGP configuration



    
    AS 396507
    
    fib-update yes
    holdtime 90
    
    router-id 206.81.81.158
    
    # IPv4 network
    network 23.129.64.0/24
    # IPv6 network
    network 2620:18C::/36
    
    #### IPv4 neighbors ####
    group "AS-SIXRSv4" {
    remote-as 33108
    neighbor 206.81.80.2 {
    descr "SIXRS_rs2v4"
    announce self
    local-address 206.81.81.158
    enforce neighbor-as no
    max-prefix 200000
    }
    neighbor 206.81.80.3 {
    descr "SIXRS_rs3v4"
    announce self
    local-address 206.81.81.158
    enforce neighbor-as no
    max-prefix 200000
    }
    }
    group "AS-HURRICANE-Transit-v4" {
    remote-as 6939
    neighbor 206.81.80.40 {
    descr "HE_transit_rs1v4"
    announce self
    local-address 206.81.81.158
    max-prefix 100000000
    }
    }
    group "AS-ALTOPIAv4" {
    remote-as 6456
    neighbor 206.81.80.10 {
    descr "ALT_rs1v4"
    announce self
    local-address 206.81.81.158
    max-prefix 20 restart 30
    }
    neighbor 206.81.81.41 {
    descr "ALT_rs2v4"
    announce self
    local-address 206.81.81.158
    max-prefix 20 restart 30
    }
    }
    group "AS-POCKETINETv4" {
    remote-as 23265
    neighbor 206.81.80.88 {
    descr "POK_rs1v4"
    announce self
    local-address 206.81.81.158
    max-prefix 600
    }
    }
    group "AS-DOOFv4" {
    remote-as 395823
    neighbor 206.81.81.125 {
    descr "DOOF_rs1v4"
    announce self
    local-address 206.81.81.158
    max-prefix 5
    }
    }
    group "AS-PCHv4" {
    remote-as 3856
    neighbor 206.81.80.81 {
    descr "PCH_rs1v4"
    announce self
    local-address 206.81.81.158
    max-prefix 600
    }
    }
    group "AS-PCHWNv4" {
    remote-as 42
    neighbor 206.81.80.80 {
    descr "PCHWN_rs1v4"
    announce self
    local-address 206.81.81.158
    max-prefix 600
    }
    }
    group "AS-WOBv4" {
    remote-as 64241
    neighbor 206.81.81.87 {
    descr "WOB_rs1v4"
    announce self
    local-address 206.81.81.158
    max-prefix 5
    }
    }
    group "AS-MISAKAv4" {
    remote-as 57695
    neighbor 206.81.81.161 {
    descr "MISAKA_rs1v4"
    announce self
    local-address 206.81.81.158
    max-prefix 300
    }
    }
    group "AS-RISUPv4" {
    remote-as 16652
    neighbor 206.81.81.74 {
    descr "RISUP_rs1v4"
    announce self
    local-address 206.81.81.158
    max-prefix 20
    }
    }
    group "AS-CLDFLRv4" {
    remote-as 13335
    neighbor 206.81.81.10 {
    descr "CLDFLR_rs1v4"
    announce self
    local-address 206.81.81.158
    max-prefix 1000
    }
    }
    group "AS-GITHUBv4" {
    remote-as 36459
    neighbor 206.81.81.89 {
    descr "GITHUB_rs1v4"
    announce self
    local-address 206.81.81.158
    max-prefix 100
    }
    neighbor 206.81.81.90 {
    descr "GITHUB_rs2v4"
    announce self
    local-address 206.81.81.158
    max-prefix 100
    }
    }
    group "AS-YAHOOv4" {
    remote-as 10310
    neighbor 206.81.80.98 {
    descr "YAHOO_rs1v4"
    announce self
    local-address 206.81.81.158
    max-prefix 2000
    }
    neighbor 206.81.81.50 {
    descr "YAHOO_rs2v4"
    announce self
    local-address 206.81.81.158
    max-prefix 2000
    }
    }
    group "AS-SYMTECv4" {
    remote-as 27471
    neighbor 206.81.81.169 {
    descr "SYMTEC_rs1v4"
    announce self
    local-address 206.81.81.158
    max-prefix 40
    }
    neighbor 206.81.81.170 {
    descr "SYMTEC_rs2v4"
    announce self
    local-address 206.81.81.158
    max-prefix 40
    }
    }
    group "AS-AKAMAIv4" {
    remote-as 20940
    neighbor 206.81.80.113 {
    descr "AKAMAI_rs1v4"
    announce self
    local-address 206.81.81.158
    max-prefix 200
    }
    }
    group "AS-WAVEv4" {
    remote-as 11404
    neighbor 206.81.80.56 {
    descr "WAVE_rs1v4"
    announce self
    local-address 206.81.81.158
    max-prefix 3000
    }
    }
    
    
    group "AS-GOOGLEv4" {
    remote-as 15169
    neighbor 206.81.80.17 {
    descr "GOOGLE_rs1v4"
    announce self
    local-address 206.81.81.158
    max-prefix 15000
    }
    }
    
    
    #### IPv6 neighbors ####
    group "AS-SIXRSv6" {
    remote-as 33108
    neighbor 2001:504:16::2 {
    descr "SIXRS_rs2v6"
    announce self
    local-address 2001:504:16::6:cdb
    enforce neighbor-as no
    max-prefix 60000
    }
    neighbor 2001:504:16::3 {
    descr "SIXRS_rs3v6"
    announce self
    local-address 2001:504:16::6:cdb
    enforce neighbor-as no
    max-prefix 60000
    }
    }
    group "AS-HURRICANE-Transit-v6" {
    remote-as 6939
    neighbor 2001:504:16::1b1b {
    descr "HE_transit_rs1v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 100000000
    }
    }
    group "AS-ALTOPIAv6" {
    remote-as 6456
    neighbor 2001:504:16::1938 {
    descr "ALT_rs1v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 20 restart 30
    }
    neighbor 2001:504:16::297:0:1938 {
    descr "ALT_rs2v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 20 restart 30
    }
    }
    group "AS-POCKETINETv6" {
    remote-as 23265
    neighbor 2001:504:16::5ae1 {
    descr "POK_rs1v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 600
    }
    }
    group "AS-DOOFv6" {
    remote-as 395823
    neighbor 2001:504:16::6:a2f {
    descr "DOOF_rs1v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 5
    }
    }
    group "AS-PCHv6" {
    remote-as 3856
    neighbor 2001:504:16::f10 {
    descr "PCH_rs1v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 600
    }
    }
    group "AS-PCHWNv6" {
    remote-as 42
    neighbor 2001:504:16::2a {
    descr "PCHWN_rs1v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 600
    }
    }
    group "AS-WOBv6" {
    remote-as 64241
    neighbor 2001:504:16::faf1 {
    descr "WOB_rs1v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 5
    }
    }
    group "AS-MISAKAv6" {
    remote-as 57695
    neighbor 2001:504:16::e15f {
    descr "MISAKA_rs1v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 300
    }
    }
    group "AS-RISUPv6" {
    remote-as 16652
    neighbor 2001:504:16::410c {
    descr "RISUP_rs1v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 10
    }
    }
    group "AS-CLDFLRv6" {
    remote-as 13335
    neighbor 2001:504:16::3417 {
    descr "CLDFLR_rs1v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 200
    }
    }
    group "AS-GITHUBv6" {
    remote-as 36459
    neighbor 2001:504:16::8e6b {
    descr "GITHUB_rs1v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 20
    }
    neighbor 2001:504:16::346:0:8e6b {
    descr "GITHUB_rs2v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 20
    }
    }
    group "AS-YAHOOv6" {
    remote-as 10310
    neighbor 2001:504:16::2846 {
    descr "YAHOO_rs1v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 200
    }
    neighbor 2001:504:16::306:0:2846 {
    descr "YAHOO_rs2v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 200
    }
    }
    group "AS-AKAMAIv6" {
    remote-as 20940
    neighbor 2001:504:16::51cc {
    descr "AKAMAI_rs1v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 40
    }
    }
    group "AS-WAVEv6" {
    remote-as 11404
    neighbor 2001:504:16::2c8c {
    descr "WAVE_rs1v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 250
    }
    }
    
    
    group "AS-GOOGLEv6" {
    remote-as 15169
    neighbor 2001:504:16::3b41 {
    descr "GOOGLE_rs1v6"
    announce self
    local-address 2001:504:16::6:cdb
    max-prefix 1000
    }
    }
    
    #### Filtering Rules ####
    
    deny from any
    deny to any
    
    # https://www.arin.net/announcements/2014/20140130.html
    # This block will be subject to a minimum size allocation of /28 and a
    # maximum size allocation of /24. ARIN should use sparse allocation when
    # possible within that /10 block.
    allow from any prefix 23.128.0.0/10 prefixlen 24 - 28 # ARIN IPv6 transition
    
    ## IPv4 ##
    # SIXRS_rs2v4
    allow from 206.81.80.2 inet prefixlen 8 - 24
    allow to 206.81.80.2 inet prefixlen 8 - 24
    # SIXRS_rs3v4
    allow from 206.81.80.3 inet prefixlen 8 - 24
    allow to 206.81.80.3 inet prefixlen 8 - 24
    # HE_transit_rs1v4
    allow from 206.81.80.40
    allow to 206.81.80.40
    # ALT_rs1v4
    allow from 206.81.80.10 inet prefixlen 8 - 24
    allow to 206.81.80.10 inet prefixlen 8 - 24
    # ALT_rs2v4
    allow from 206.81.81.41 inet prefixlen 8 - 24
    allow to 206.81.81.41 inet prefixlen 8 - 24
    # POK_rs1v4
    allow from 206.81.80.88 inet prefixlen 8 - 24
    allow to 206.81.80.88 inet prefixlen 8 - 24
    # DOOF_rs1v4
    allow from 206.81.81.125 inet prefixlen 8 - 24
    allow to 206.81.81.125 inet prefixlen 8 - 24
    # PCH_rs1v4
    allow from 206.81.80.81 inet prefixlen 8 - 24
    allow to 206.81.80.81 inet prefixlen 8 - 24
    # PCHWN_rs1v4
    allow from 206.81.80.80 inet prefixlen 8 - 24
    allow to 206.81.80.80 inet prefixlen 8 - 24
    # WOB_rs1v4
    allow from 206.81.81.87 inet prefixlen 8 - 24
    allow to 206.81.81.87 inet prefixlen 8 - 24
    # MISAKA_rs1v4
    allow from 206.81.81.161 inet prefixlen 8 - 24
    allow to 206.81.81.161 inet prefixlen 8 - 24
    # RISUP_rs1v4
    allow from 206.81.81.74 inet prefixlen 8 - 24
    allow to 206.81.81.74 inet prefixlen 8 - 24
    # CLDFLR_rs1v4
    allow from 206.81.81.10 inet prefixlen 8 - 24
    allow to 206.81.81.10 inet prefixlen 8 - 24
    # GITHUB_rs1v4
    allow from 206.81.81.89 inet prefixlen 8 - 24
    allow to 206.81.81.89 inet prefixlen 8 - 24
    # GITHUB_rs2v4
    allow from 206.81.81.90 inet prefixlen 8 - 24
    allow to 206.81.81.90 inet prefixlen 8 - 24
    # YAHOO_rs1v4
    allow from 206.81.80.98 inet prefixlen 8 - 24
    allow to 206.81.80.98 inet prefixlen 8 - 24
    # YAHOO_rs2v4
    allow from 206.81.81.50 inet prefixlen 8 - 24
    allow to 206.81.81.50 inet prefixlen 8 - 24
    # SYMTEC_rs1v4
    allow from 206.81.81.169 inet prefixlen 8 - 24
    allow to 206.81.81.169 inet prefixlen 8 - 24
    # SYMTEC_rs2v4
    allow from 206.81.81.170 inet prefixlen 8 - 24
    allow to 206.81.81.170 inet prefixlen 8 - 24
    # AKAMAI_rs1v4
    allow from 206.81.80.113 inet prefixlen 8 - 24
    allow to 206.81.80.113 inet prefixlen 8 - 24
    # WAVE_rs1v4
    allow from 206.81.80.56 inet prefixlen 8 - 24
    allow to 206.81.80.56 inet prefixlen 8 - 24
    # GOOG_rs1v4
    allow from 206.81.80.17
    allow to 206.81.80.17
    
    ## IPv6 ##
    # SIXRS_rs2v6
    allow from 2001:504:16::2 inet6 prefixlen 16 - 48
    allow to 2001:504:16::2 inet6 prefixlen 16 - 48
    # SIXRS_rs3v6
    allow from 2001:504:16::3 inet6 prefixlen 16 - 48
    allow to 2001:504:16::3 inet6 prefixlen 16 - 48
    # HE_transit_rs1v6
    allow from 2001:504:16::1b1b
    allow to 2001:504:16::1b1b
    # ALT_rs1v6
    allow from 2001:504:16::1938 inet6 prefixlen 16 - 48
    allow to 2001:504:16::1938 inet6 prefixlen 16 - 48
    # ALT_rs2v6
    allow from 2001:504:16::297:0:1938 inet6 prefixlen 16 - 48
    allow to 2001:504:16::297:0:1938 inet6 prefixlen 16 - 48
    # POK_rs1v6
    allow from 2001:504:16::5ae1 inet6 prefixlen 16 - 48
    allow to 2001:504:16::5ae1 inet6 prefixlen 16 - 48
    # DOOF_rs1v6
    allow from 2001:504:16::6:a2f inet6 prefixlen 16 - 48
    allow to 2001:504:16::6:a2f inet6 prefixlen 16 - 48
    # PCH_rs1v6
    allow from 2001:504:16::f10 inet6 prefixlen 16 - 48
    allow to 2001:504:16::f10 inet6 prefixlen 16 - 48
    # PCHWN_rs1v6
    allow from 2001:504:16::2a inet6 prefixlen 16 - 48
    allow to 2001:504:16::2a inet6 prefixlen 16 - 48
    # WOB_rs1v6
    allow from 2001:504:16::faf1 inet6 prefixlen 16 - 48
    allow to 2001:504:16::faf1 inet6 prefixlen 16 - 48
    # MISAKA_rs1v6
    allow from 2001:504:16::e15f inet6 prefixlen 16 - 48
    allow to 2001:504:16::e15f inet6 prefixlen 16 - 48
    # RISUP_rs1v6
    allow from 2001:504:16::410c inet6 prefixlen 16 - 48
    allow to 2001:504:16::410c inet6 prefixlen 16 - 48
    # CLDFLR_rs1v6
    allow from 2001:504:16::3417 inet6 prefixlen 16 - 48
    allow to 2001:504:16::3417 inet6 prefixlen 16 - 48
    # GITHUB_rs1v6
    allow from 2001:504:16::8e6b inet6 prefixlen 16 - 48
    allow to 2001:504:16::8e6b inet6 prefixlen 16 - 48
    # GITHUB_rs2v6
    allow from 2001:504:16::346:0:8e6b inet6 prefixlen 16 - 48
    allow to 2001:504:16::346:0:8e6b inet6 prefixlen 16 - 48
    # YAHOO_rs1v6
    allow from 2001:504:16::2846 inet6 prefixlen 16 - 48
    allow to 2001:504:16::2846 inet6 prefixlen 16 - 48
    # YAHOO_rs2v6
    allow from 2001:504:16::306:0:2846 inet6 prefixlen 16 - 48
    allow to 2001:504:16::306:0:2846 inet6 prefixlen 16 - 48
    # AKAMAI_rs1v6
    allow from 2001:504:16::51cc inet6 prefixlen 16 - 48
    allow to 2001:504:16::51cc inet6 prefixlen 16 - 48
    # WAVE_rs1v6
    allow from 2001:504:16::2c8c inet6 prefixlen 16 - 48
    allow to 2001:504:16::2c8c inet6 prefixlen 16 - 48
    # GOOG_rs1v6
    allow from 2001:504:16::3b41
    allow to 2001:504:16::3b41
    
    # filter bogus networks according to RFC5735
    deny from any prefix 0.0.0.0/8 prefixlen >= 8 # 'this' network [RFC1122]
    deny from any prefix 10.0.0.0/8 prefixlen >= 8 # private space [RFC1918]
    deny from any prefix 100.64.0.0/10 prefixlen >= 10 # CGN Shared [RFC6598]
    deny from any prefix 127.0.0.0/8 prefixlen >= 8 # localhost [RFC1122]
    deny from any prefix 169.254.0.0/16 prefixlen >= 16 # link local [RFC3927]
    deny from any prefix 172.16.0.0/12 prefixlen >= 12 # private space [RFC1918]
    deny from any prefix 192.0.2.0/24 prefixlen >= 24 # TEST-NET-1 [RFC5737]
    deny from any prefix 192.168.0.0/16 prefixlen >= 16 # private space [RFC1918]
    deny from any prefix 198.18.0.0/15 prefixlen >= 15 # benchmarking [RFC2544]
    deny from any prefix 198.51.100.0/24 prefixlen >= 24 # TEST-NET-2 [RFC5737]
    deny from any prefix 203.0.113.0/24 prefixlen >= 24 # TEST-NET-3 [RFC5737]
    deny from any prefix 224.0.0.0/4 prefixlen >= 4 # multicast
    deny from any prefix 240.0.0.0/4 prefixlen >= 4 # reserved
    
    # filter bogus IPv6 networks according to IANA
    deny from any prefix ::/8 prefixlen >= 8
    deny from any prefix 0100::/64 prefixlen >= 64 # Discard-Only [RFC6666]
    deny from any prefix 2001:2::/48 prefixlen >= 48 # BMWG [RFC5180]
    deny from any prefix 2001:10::/28 prefixlen >= 28 # ORCHID [RFC4843]
    deny from any prefix 2001:db8::/32 prefixlen >= 32 # docu range [RFC3849]
    deny from any prefix 3ffe::/16 prefixlen >= 16 # old 6bone
    deny from any prefix fc00::/7 prefixlen >= 7 # unique local unicast
    deny from any prefix fe80::/10 prefixlen >= 10 # link local unicast
    deny from any prefix fec0::/10 prefixlen >= 10 # old site local unicast
    deny from any prefix ff00::/8 prefixlen >= 8 # multicast




# [vnStat](https://en.wikipedia.org/wiki/Vnstat) - statistics gathering configuration



    
    # vnStat 1.15 config file
    ##
    
    # default interface
    Interface "eth0"
    
    # location of the database directory
    DatabaseDir "/var/lib/vnstat"
    
    # locale (LC_ALL) ("-" = use system locale)
    Locale "-"
    
    # on which day should months change
    MonthRotate 1
    
    # date output formats for -d, -m, -t and -w
    # see 'man date' for control codes
    DayFormat "%x"
    MonthFormat "%b '%y"
    TopFormat "%x"
    
    # characters used for visuals
    RXCharacter "%"
    TXCharacter ":"
    RXHourCharacter "r"
    TXHourCharacter "t"
    
    # how units are prefixed when traffic is shown
    # 0 = IEC standard prefixes (KiB/MiB/GiB/TiB)
    # 1 = old style binary prefixes (KB/MB/GB/TB)
    UnitMode 0
    
    # output style
    # 0 = minimal & narrow, 1 = bar column visible
    # 2 = same as 1 except rate in summary and weekly
    # 3 = rate column visible
    OutputStyle 3
    
    # used rate unit (0 = bytes, 1 = bits)
    RateUnit 1
    
    # try to detect interface maximum bandwidth, 0 = disable feature
    # MaxBandwidth will be used as fallback value when enabled
    BandwidthDetection 1
    
    # maximum bandwidth (Mbit) for all interfaces, 0 = disable feature
    # (unless interface specific limit is given)
    MaxBandwidth 1000
    
    # interface specific limits
    # example 8Mbit limit for 'ethnone':
    MaxBWethnone 8
    
    # how many seconds should sampling for -tr take by default
    Sampletime 5
    
    # default query mode
    # 0 = normal, 1 = days, 2 = months, 3 = top10
    # 4 = exportdb, 5 = short, 6 = weeks, 7 = hours
    QueryMode 0
    
    # filesystem disk space check (1 = enabled, 0 = disabled)
    CheckDiskSpace 1
    
    # database file locking (1 = enabled, 0 = disabled)
    UseFileLocking 1
    
    # how much the boot time can variate between updates (seconds)
    BootVariation 15
    
    # log days without traffic to daily list (1 = enabled, 0 = disabled)
    TrafficlessDays 1
    
    
    # vnstatd
    ##
    
    # switch to given user when started as root (leave empty to disable)
    DaemonUser ""
    
    # switch to given user when started as root (leave empty to disable)
    DaemonGroup ""
    
    # how often (in seconds) interface data is updated
    UpdateInterval 30
    
    # how often (in seconds) interface status changes are checked
    PollInterval 5
    
    # how often (in minutes) data is saved to file
    SaveInterval 5
    
    # how often (in minutes) data is saved when all interface are offline
    OfflineSaveInterval 30
    
    # how often (in minutes) bandwidth detection is redone when
    # BandwidthDetection is enabled (0 = disabled)
    BandwidthDetectionInterval 5
    
    # force data save when interface status changes (1 = enabled, 0 = disabled)
    SaveOnStatusChange 1
    
    # enable / disable logging (0 = disabled, 1 = logfile, 2 = syslog)
    UseLogging 2
    
    # create dirs if needed (1 = enabled, 0 = disabled)
    CreateDirs 1
    
    # update ownership of files if needed (1 = enabled, 0 = disabled)
    UpdateFileOwner 1
    
    # file used for logging if UseLogging is set to 1
    LogFile "/var/log/vnstat/vnstat.log"
    
    # file used as daemon pid / lock file
    PidFile "/var/run/vnstat/vnstat.pid"
    
    
    # vnstati
    ##
    
    # title timestamp format
    HeaderFormat "%x %H:%M"
    
    # show hours with rate (1 = enabled, 0 = disabled)
    HourlyRate 1
    
    # show rate in summary (1 = enabled, 0 = disabled)
    SummaryRate 1
    
    # layout of summary (1 = with monthly, 0 = without monthly)
    SummaryLayout 1
    
    # transparent background (1 = enabled, 0 = disabled)
    TransparentBg 0
    
    # image colors
    CBackground "FFFFFF"
    CEdge "AEAEAE"
    CHeader "606060"
    CHeaderTitle "FFFFFF"
    CHeaderDate "FFFFFF"
    CText "000000"
    CLine "B0B0B0"
    CLineL "-"
    CRx "92CF00"
    CTx "606060"
    CRxD "-"
    CTxD "-"




# [OpenNTPD](https://en.wikipedia.org/wiki/OpenNTPD) - network time protocol for system clock synchronization



    
    # Upstream Servers
    servers pool.ntp.org



