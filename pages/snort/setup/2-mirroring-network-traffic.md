---
layout: page
title: Setting up Snort - Part 2 - Mirroring Network Traffic
meta-description: iptables mangle tee route, prerouting, postrouting, openwrt, dd-wrt, snort network ids mirroring network traffic 
---
**Note: If you own a router or switch that has a built in SPAN or equivalent mirroring port, feel free to skip to (Part 3-TODO).**

The typical home network setup has a modem provided by the ISP connected to a broadband router, which provides wired and wireless internet access to home devices.

TODO diagram

As you can see in the diagram, in order for the machine where Snort lives to capture and process the network traffic zipping around the home, there needs to be a way to forward a copy of all the traffic there.

Enter the **iptables-extensions TEE module.**

This module will clone your packets and forward it to your specified destination host.

**But wait!** If the layer 3 IP header for every packet of traffic has a source and destination IP address, how can you send that packet to a specified host?! 

Good question and observation! TEE works by making a copy of the packet, modifying the layer 2 ethernet frame to the specified target host's MAC address.

**Ok. So how can I use it?**

You will need a router with DD-WRT or OpenWrt customer router firmware installed.

**Note: the following is based on OpenWrt, as the DD-WRT firmware for my router did not include support for the required ROUTE and TEE iptables-extensions modules.**

[Part 2a - Prepare your router for DD-WRT or OpenWrt readiness](/pages/snort/setup/2a-wrt-router)

Once you have DD-WRT or OpenWrt installed on your router, it is time to finally get port mirroring configured!

[Part 2b - Enable Port Mirroring](/pages/snort/setup/2b-enable-port-mirroring)

If all looks good, then congratulations!

... but that's not the end of it.

The above iptables configuration is temporary, your configuration will be lost when your router reboots. Scroll down below for a sample script to load this on router startup.

[Part 2c - Configure iptables on Router Startup](/pages/snort/setup/2c-configure-port-mirroring-router-startup)

## LIMITATIONS
- TEE will only send the cloned packet to the next hop. i.e., destination should be the Snort host. If it is not, then you will need to repeat the TEE configuration to relay onto subsequent hops where the Snort host lives.
- It only works if both the source and destination host lives on the same subnet.
- Because TEE modifies the ethernet frame destination MAC address, you lose the original data.

## Alternatives to iptables-extensions TEE

[https://code.google.com/p/port-mirroring](https://code.google.com/p/port-mirroring)

This project is an alternative to iptables TEE.

It also supports TZSP, which encapsulates the entire packet, making sure the data down to the ethernet frame layer is preserved.

... TODO ...

Next > Part 3

