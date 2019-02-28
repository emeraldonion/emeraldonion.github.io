---
author: xanaduregio
date: 2018-10-06 23:49:34+00:00
layout: post
title: We're back up after a 3 week outage
tags:
- BGP
- DNS
- Infrastructure
- Internet Exchange Points
---

On Wednesday (10/3/2018) @ around 5:45pm Pacific Time we came back up after being down for about 3 weeks starting on 9/11/2018.

The initial reason we went down was because the [Seattle Internet eXchange (SIX)](https://www.seattleix.net/) administratively shut BGP access to our port because of a violation. We were transmitting DNS requests from an IP Address that we do not own and so the SIX took steps to protect the SIX fabric and shutdown our BGP access. That was an acceptable response and is consistant with the SIX rules. The root cause as to why we were transmitting DNS requests from an IP Address we do not own is still unknown. Due to the fact we don't log firewall actions or collect netflow statistics makes it technically impossible for us to establish an empirical RCA with facts and numbers.

The contributing factor to the length of our downtime is directly related to the fact that we are a volunteer led and run organization. Nobody gets paid. And so we have to rely on our volunteers' free time and good will. Also, we do not yet have an Out of Band (OOB) solution in place, meaning that both service and remote management of the environment were lost at the same time. This meant physically traveling to the data center, gathering current configs for analysis. We analyzed our current config and found no reason for this to happen. Beyond that, any other statements would be a pure guess without logs.

In response to the fact we have low resources, we are now soliciting additional volunteer help to add another BSD Network Admin to the team so that we aren't relying on or putting stress on just one individual.

We are happy to be back up and running. We are currently reviewing our infrastructure architecture to find ways to reduce outages both of this kind and duration.

We apologize to those who have donated for running exit relays and are going to extend the lifetime of the relays by this downtime and any further potential downtime.
