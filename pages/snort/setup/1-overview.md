---
layout: page
title: Setting up Snort - Part 1 - Overview
---
This guide is for the majority of us who have a normal home broadband setup and want to be able to effectively monitor their network traffic.

It is also for people like us who either

- Do not know where to start in setting an NIDS up
- Cannot afford to buy a dedicated commercial grade switch or router (Cisco, Juniper etc), or is worried about the ongoing electricity bill (and heat issues) of owning said appliance.

Once you have the ability to monitor your own home network traffic, what you can do with the data collected is limitless.

Some of the things I'm interested in are:

- Analyze top ingress and egress sources and destinations
- What traffic your devices are transmitting when it is idle
- Whether you have an malware outbreak or infection in your home devices


Steps required to get Snort installed and capturing your traffic.

- Modify your existing network setup to allow mirroring of all network traffic to the interface that Snort listens to
- Install Snort on a linux based VM
- Getting logging setup for Snort
- Installing an SIEM or Barnyard2 to make sense of the Snort logs

... TODO ...

Next: Setting up a home NIDS (Snort) - Part 2
