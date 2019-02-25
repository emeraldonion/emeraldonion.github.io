---
author: undervillain
comments: false
date: 2018-04-06 10:20:20+00:00
layout: post
link: https://emeraldonion.org/introducing-gibson/
slug: introducing-gibson
title: Introducing gibson
wordpress_id: 730
categories:
- Community
- Education
- HardenedBSD
- Infrastructure
---

[caption id="attachment_745" align="aligncenter" width="1600"]![](https://emeraldonion.org/wp-content/uploads/2018/04/Outline-Long.jpg) Artwork by Mike Finch (CC BY 4.0)[/caption]



As you may know, Emerald Onion systems run [HardenedBSD](https://hardenedbsd.org/). BSD systems in general, and HBSD in particular, provide numerous advantages to our team in operating secure and highly performant Tor relays. But BSD systems make up only a very small percentage of the Tor network. There are many similarities between BSD and Linux, with which many users may be more familiar, but the differences can be intimidating. We’re addressing this by launching gibson, a project to develop a suite of tools to address the needs of Tor service operators. The Tor network is more robust when it is diverse, and this is one way that we can encourage a more diverse Tor network and enhance our community.

It is important to say that while our initial focus is on BSD systems, our plan is to extend gibson to serve the Tor community regardless of platform. We’re starting with HBSD because it’s an obvious and natural choice for us; we believe in "[dogfood](https://en.wikipedia.org/wiki/Eating_your_own_dog_food)", and we want you to be assured that the code we share is used by our team in real deployments. In our mind it isn’t enough to make running Tor services easy, our tools must also help make services secure and reliable. At Emerald Onion, we do this by example.


# What is gibson?


A generous description is that gibson is a no-dependency suite of cross-functional tools for creating and maintaining secure and robust Tor services. We currently support HardenedBSD systems, but plan to extend our support to FreeBSD, OpenBSD, and Linux in future releases.

We say that this is a generous description because we want our tools to take as light a touch as possible. To an experienced user, gibson may not appear to do much of anything at all. This is an intentional design decision. An experienced user might say, “Using gibson to do an update achieves the same outcome as just running these three commands I already know”. Truly, nothing would make us happier than this outcome. We want for everyone in the Tor community to be an expert of the tools that make the network operate, from Tor itself to the operating system, hardware, and everything in between. That said, we hope that experienced users will continue to use gibson, and that they will propose new solutions and new functionality.

The goal of gibson isn’t to make difficult Tor management tasks trivial. Rather, the goal of gibson is to make Tor management tasks consistent. We believe that secure systems are built around reproducible, auditable processes. Most of our maintenance is simple and mundane, but a small configuration error or a missed security patch could be catastrophic to the anonymity and security of Tor users. We want to eliminate possible sources of human error and to share our best practices with the community.

We also believe that users should be able to adopt gibson on existing systems without onerous effort, and should also be able to walk away from it whenever they wish without any lock-in. We want our tools to promote the adoption of correct processes and best practices. We also hope that they will be educational. Users new to BSD systems, new to Linux, and new to Tor should be able to look at our code and with minimal effort be able to understand what it does and why.

Finally, we say that gibson is cross-functional because our solution space is defined by the needs of a secure and robust Tor deployment. We do not seek to replace virtualization and jail tools (for instance, bhyve, virtualbox, ezjail, iocell, etc.). We do not seek to replace disk encryption tools (geli, LUKS, etc.). We’re not replacing any web servers, either (nginx, Apache, Caddy, etc.). What we are doing is providing tools to streamline the implementation of these other projects into a complete solution which addresses the needs of Tor administrators.


# What does gibson do now?


Currently, gibson updates and controls Tor services running in HBSD jails. A simple and mundane task, but one that we want to make sure is done consistently during each of our maintenance windows. Our initial release of gibson is version 0.1, which is derived from a handful of scripts currently used in maintenance of Emerald Onion systems.



 	
  * 0.0.1 Initial scripts used for EmeraldOnion system maintenance

 	
  * 0.1.0 First version 0.0.1 scripts refactored into gibson: apply system and package updates in jails; start, stop, restart tor services in jails;




# Where can I get gibson?


We're working on getting gibson into the HardenedBSD ports repository, and it should land there shortly.  It will take a little more time for binary packages to become available for users who prefer to use pkg.

In the meantime, gibson is available [at our GitHub](https://github.com/emeraldonion/gibson), as is the files you need to sideload it as a port.  Detailed installation instructions are available there.


# Roadmap (or: what will gibson do in the future?)




## 0.2 -> 0.5





 	
  * Create and maintain template jail(s); clone templates into new jails and deploy tor services:

 	
    * middle relays

 	
    * exit relays

 	
    * bridges

 	
    * onion services (with nginix, initially)




 	
  * Create and manage geli-encrypted ZFS pools

 	
  * Initial creation of encrypted providers and pool members from specified devices

 	
  * Replacement of devices due to failure or capacity expansion

 	
  * Good ideas suggested (or implemented and submitted) by our community!




## 0.6 -> 0.9





 	
  * Support for FreeBSD systems

 	
  * Good ideas suggested (or implemented and submitted) by our community!




## 1.5+





 	
  * Support for Linux systems

 	
  * Support for non-geli encrypted filesystems

 	
  * Support for non-ZFS storage pools

 	
  * Good ideas suggested (or implemented and submitted) by our community!




# House Style


gibson is always written in all-lowercase.


# Logo


The gibson logo was generously donated by [Mike Finch](https://twitter.com/mkfnch) and is licensed by Emerald Onion as [Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/).

[![](https://emeraldonion.org/wp-content/uploads/2018/04/Outline-Short-300x300.jpg)](https://emeraldonion.org/wp-content/uploads/2018/04/Outline-Short.jpg)

[![](https://emeraldonion.org/wp-content/uploads/2018/04/Outline-Long-300x98.jpg)](https://emeraldonion.org/wp-content/uploads/2018/04/Outline-Long.jpg)
