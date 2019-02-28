---
author: xanaduregio
date: 2017-08-02 06:22:23+00:00
layout: post
title: We're back after a 6.5 day outage
tags:
- Infrastructure
---

Today (8/1/2017) @ 23:22 Pacific Time, we came back online after being down for 6 days and 12 hours. Our previous configuration where we had two physically separate systems (1 x pfSense router and 1 x Tor router) is gone. The server that was running the Tor router started to experience hardware errors, as reported by kern.log. These errors were traced back to the system board, which eventually caused issues with the disk.

While all of this was happening, we were also down an admin as he was out at defcon. So, juggling that, wanting to restore service and limited funds because to replace the Tor router, we would've had to wait for our refund check from the RMA before buying a new system board, we decided to virtualize our infrastructure.

We are now operating on a single server (12 x Core Intel + 32GB RAM) with 1 x pfSense 2.3.4 VM and 1 x Tor Ubuntu 16.04 VM. The system is up and passing Tor traffic.
