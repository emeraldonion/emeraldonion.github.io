---
author: yawnbox@gmail.com
date: 2018-04-23 12:00:49+00:00
layout: post
title: 'Tor as infrastructure: challenges and opportunities'
tags:
- Abuse Complaints
- BGP
- DNS
- Education
- Infrastructure
- Internet Exchange Points
- The Tor Project
---

Infrastructure as a Service (IaaS) is commonly used within enterprise environments whereby organizations pay for someone else's physical or virtual resources, including networking resources. Individuals also pay for IaaS when, for example, they need to deploy a VPS or VPN for personal use. From [Gartner](https://www.gartner.com/it-glossary/infrastructure-as-a-service-iaas):


<blockquote>**Infrastructure as a service (IaaS)** is a standardized, highly automated offering, where compute resources, complemented by storage and networking capabilities are owned and hosted by a service provider and offered to customers on-demand.</blockquote>




Tor, the network of operators that make up the network, is IaaS. What is unusual about this terminology is that the Tor network is distributed, and utilization of this networking resource is free. Companies like Duck Duck Go, Facebook, New York Times, and [every organization utilizing SecureDrop](https://securedrop.org/directory) including the Associated Press, is using this IaaS in order to best support their users' privacy rights.

A problem that Tor network operators have always faced is that setting up and maintaining the network is not free. Tor is free-to-use IaaS on purpose -- people and services need to be able to use the network without attribution in order for Tor to provide specific guarantees of privacy.

If privacy infrastructure operators had better funding, we would be in a better position to support larger infrastructure needs. For example, if Mozilla or Brave ever wanted to collect browser telemetry over Tor onion services to best support the privacy of their users, the Tor network would likely need to pivot towards larger and more stable network operators. In this article, we will look at some of the challenges and opportunities for operators of privacy infrastructure.


# Challenges


Emerald Onion was created, in part, to be able to demonstrate a successful Transit Internet Service Provider that only supports privacy IaaS. It was designed to best leverage existing laws in the United States in tandem with operational designs that require privacy-focused security properties. There have been and continue to be serious challenges facing Emerald Onion along with any other organization that is dedicated to privacy IaaS.


## IP transit service is exclusively a for-profit business


IP transit is a required part of an ISP. Emerald Onion, Riseup, Calyx, Quintex, etc, need to pay upstream providers who provide the physical transport of the encrypted packets that we transit as part of the Tor network. This transit service is expensive. For example, 1Gbps of service in a residential setting can cost around $80 per month in Seattle, and 1Gbps of service in a datacenter can easily cost $800 per month. This dramatic cost difference is because of capitalism -- it is presumed that a service provider in a datacenter environment is going to be profiting off of this service. Upstream providers don't care one bit that Emerald Onion is a 501(c)3 not-for-profit supporting human rights.


## Little options for trustworthy, open source hardware, particularly networking equipment


Emerald Onion is using general-purpose computing devices (currently low-power Intel Xeon D) with BSD operating systems. It is a priority for us to be using trustworthy compute infrastructure, so we are at least ensuring that the kernels and applications that we use are free/libre open source software. We hope to transition to free/libre open source hardware and firmware as soon as we can, but we also have to be concerned with compatibility and stability with HardenedBSD/FreeBSD, and the cost of this hardware. We know that options exist for free/libre open source hardware, but this is still a very new and maturing market. To further complicate this prioritized need for trustworthy compute infrastructure, Emerald Onion has particular interest in 10Gbps networking for both the LAN and WAN.

One day, we'd like to be able to support 40Gbps and 100Gbps; however, we are not aware of any free/libre open source hardware and firmware that supports 40Gbps or 100Gbps networking.


## High cost of network redundancy


Our proof-of-concept work has focused on low-cost options. This means we do not currently have redundancy at our LAN or WAN layers. Network redundancy for Emerald Onion, at minimum, would entail having not just one expensive IP transit link, but two, ideally from different upstream providers, which means two edge routers and two links to each of our Internet Exchange Points. This would also mean that we have to add redundant LAN switching, and all of this means increasing our rack space and power requirements. Basically, we would have to more than double our recurring costs to be able to have this level of infrastructure stability. While Tor itself is highly resistant to network changes, the more capacity that Emerald Onion, and other large Tor operators support, the greater the negative impact we would impose on the Tor network whenever we have to perform hardware, firmware, kernel, and application updates.


## IPv4 scopes for exit operators


As an exit relay operator, Emerald Onion must own and operate our own IPv4 address space for efficiently handling abuse communications from other service providers and law enforcement. Additionally, relay operators who peer directly with other service providers in Internet Exchange Points (IXPs) who have their own Autonomous System Number (ASNs) also require their own IP space. The entire world ran out of IPv4 address scopes to hand out to new and existing service providers a few years ago, and this is a blocker for any new Tor operator who is working to achieve the same level of stability that Emerald Onion is working to achieve.

Tor exit relaying currently depends in IPv4 connectivity between Tor routers (middle relay to exit relay traffic, for example). To be given an exit flag, a static IPv4 address is ideal for the Tor network (dynamic IP addresses would require a few hours delay of client discovery in the consensus).

Tor exit operators would not need their own Regional Internet Registry (RIR) -provisioned IPv4 address space if the exit flag could be given to IPv6-only operators, but this is not currently possible. ORPort connections (inter-tor circuit connections from middle relays, for example) cannot usually generate abuse, so IPv4 scope leasing is an easier option if IPv6-only exiting was a possibility.

One idea that Emerald Onion has had is that it may be possible to make proposals to large organizations, including universities, that are sitting on very large IPv4 scopes. We think that these organizations might be willing to donate small (/24) scopes to not-for-profit Tor network operators.


# Opportunities




## Surveillance and latency minimization


Seattle is home to a very large telecommunications hub called the [Westin Building Exchange](https://en.wikipedia.org/wiki/Westin_Building) (WBE). We know that this building has National Security Agency (NSA) taps on I/O connections that are likely to facilitate traffic to regions like China and Russia. Additionally, WBE hosts several of the Internet's DNS [Root Servers](https://www.iana.org/domains/root/servers), several of which are part of the Seattle Internet Exchange (SIX).

Emerald Onion went through the process of securing our own ASN, IPv6, and IPv4 scopes from American Registry of Internet Numbers (ARIN). We needed these things to connect to the SIX. Connecting to the SIX means that we are physically and directly connected to as many as [280 other service providers](https://www.seattleix.net/participants/). We made this a priority because direct peering, using Border Gateway Protocol (BGP), minimizes the amount of clear-net switches and routers that a Tor user's exit traffic has to travel through to reach its final destination. Every switch or router that Tor traffic has to traverse is an opportunity for surveillance and adds latency.

This strategy for Tor exit router placement is also ideal considering DNS. Being that multiple DNS Root Servers are directly peered with Emerald Onion, this further minimizes a global persistent adversary's ability to spy on what Tor users are doing.

Statistically, due to requirements in the Tor protocol, individual Tor circuits bounce around multiple countries before they exit the network. This means that a non-trivial amount of the traffic that Emerald Onion, and any other United States exit operator facilitates, comes from a middle relay not within the United States. In tandem, generally speaking, a non-trivial amount of Tor exit traffic is destined to American services like Akamai, Cloudflare, Facebook, Google, and DNS Root Servers. These two likelihoods, together, means the following:

Tor exit traffic that is destined to service providers in the United States is best served, in terms of surveillance and latency minimization, by Tor exit operators that have exit relays connected to IXPs in datacenters along the coasts of the United States where undersea cables physically terminate, presuming that popular service providers like Akamai, Cloudflare, Facebook, Google, and DNS Root Servers are direct peers. This, in theory, minimizes the opportunity for American-sponsored traffic analysis, data retention, and surveillance, in addition to any other global persistent adversary who may have compromised network equipment within IXPs.

Emerald Onion has already compiled an initial list of [IXPs around the United States](https://emeraldonion.org/internet-exchange-points-in-the-united-states/). We continue to work on a list of qualities that an IXP should have that is a ideal for a Tor network operator:



 	
  * Number of participants -- This is important because if there is a peering link (using BGP) with another service provider, the opportunities for surveillance are minimized. For obvious reasons, the amount of direct peers is as important as the popularity of said services.

 	
  * Access to specific participants -- This is important because, for example, peering agreements with large providers such as Akamai, Cloudflare, Facebook, Google, and DNS Root Servers minimize the opportunity for surveillance while minimizing latency.

 	
  * Nonprofit and affordable -- A large number of IXPs are for-profit and thus have high up-front and high recurring costs for connectivity, in addition to setup fees and recurring fees for copper or fiber maintenance.

 	
  * Geo-location -- This is important because of, at least, location diversity, peer diversity, and direct connections with international undersea cables are focal points for the facilitators of global passive surveillance.

 	
  * Prohibits network surveillance -- The Seattle Internet Exchange, for example, has a stated policy that prohibits surveillance on peering links. One day, we hope that the SIX, and other public-benefit IXPs, will also publish a regular [transparency report](https://emeraldonion.org/transparency/).




## Funding


Emerald Onion has been in operation for 10 months. We wouldn't exist, as we are today, without the generous startup grant of $5,000 from Tor Servers. We also would not still be around without the continuous donations from our Directors who personally donate as much as $350 each month. We currently require roughly $700 per month to operate, largely due to our service contract with our co-location provider who is also our upstream transit ISP.

Going back to the beginning of this article, the Tor network is a privacy-focused IaaS. Sustainability is a constant issue for Tor network operators, especially for operators who preemptively tackle legal and long-term operational challenges. We need help. There is no easy answer for funding. Grant writing and grant management is not a trivial task, nor is sustaining a 501(c)3 not-for-profit purely based on part-time volunteer work. Emerald Onion is incredibly lucky to have a few people who regularly donate large amounts of money and time to keep the organization online, but this is not sustainable.

The operations model that Emerald Onion has created, however, is scalable if properly funded. If we were provided between between $7,000 to $10,000 per month, we could multiply our capacity by a factor of 10. If we had a pool of funding that supported 10 independent Tor network operators in the United States (there are over 100 IXPs in the United States), we could dramatically bolster the capacity and stability of the Tor network while also minimizing network surveillance opportunities and network latency.


# Conclusion


I hope that this article begins to shed light on the challenges facing privacy IaaS providers like the thousands of operators that make up the Tor network. Emerald Onion is going to continue to educate others on these topics, attempt to find and create solutions for these challenges, and continue to encourage hacker communities around the United States to build their own privacy-focused not-for-profit ISP.
