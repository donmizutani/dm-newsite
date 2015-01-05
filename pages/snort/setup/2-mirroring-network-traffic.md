---
layout: page
title: Setting up Snort - Part 2 - Mirroring Network Traffic
meta-description: iptables mangle tee route, prerouting, postrouting, openwrt, dd-wrt, snort network ids mirroring network traffic 
---
**If you own a router or switch that has a built in SPAN or equivalent mirroring port, feel free to skip to (Part 3-TODO)** 

The typical home network setup has a modem provided by the ISP connected to a broadband router, which provides wired and wireless internet access to home devices.

TODO diagram

As you can see in the diagram, in order for the machine where Snort lives to capture and process the network traffic zipping around the home, there needs to be a way to forward a copy of all the traffic there.

Enter the iptables TEE extension module. This module will clone your packets and forward it to your specified destination host.

But wait! If the layer 3 IP header of every packet of traffic has a source and destination IP address, how can you send that packet to a specified host?! 

Good question and observation! The way TEE works is that it makes a copy of the packet and modifies the layer 2 ethernet frame to the specified target host's MAC address.

**Ok. So what is this, and how can I use it?**

You will need a router with DD-WRT or OpenWrt customer router firmware installed.

[Bonus Stage! Part 2a - Prepare your router for DD-WRT or OpenWrt readiness](pages/snort/setup/2a-wrt-router)

Once you have installed DD-WRT or OpenWrt on your router, it is time to finally get port mirroring configured!

** The rest of this guide will be based on OpenWrt, as the DD-WRT for my router did not include support for the required ROUTE and TEE iptables modules.**

### Turning on port mirroring

1. SSH into your router.
2. Run the following 3 commands to install the iptables-mod-tee module
```
# opkg update
# opkg list | grep -i tee
# opkg install iptables-mod-tee
```
3. Reboot the router
4. Once rebooted, execute the following to mirror a copy of the data to your device with Snort installed
```
# iptables -t mangle -A PREROUTING -j TEE --gateway [_ipaddress_]
# iptables -t mangle -A POSTROUTING -j TEE --gateway [_ipaddress_]
```
where [_ipaddress_] is the IP address where your Snort host is located.

5. If all the previous commands executed without error, then you have successfully set up port mirroring. To verify,
- Log into the web UI for your router
- Highlight the **Status** menu, and select **Firewall** from the drop-down menu list.
- Scroll down to the **Table: Mangle** section and make sure the **Chain PREROUTING** and **Chain POSTROUTING** sections has an entry for TEE and your speficied Snort host IP.

TODO screenshot of tee

If all looks good, then congratulations!

... but that's not the end of it.

The above iptables configuration is temporary, it is lost when your router reboots. Scroll down below for a sample script to load this on router bootup.

### Turning off port mirroring

iptables -F -t mangle

### Loading iptables on router bootup
1. SSH into your router
2. Create a new file at /etc/init.d/ eg /etc/init.d/port-mirroring
3. Write your startup script there. Here is a sample of what I use:
```
#!/bin/sh /etc/rc.common
 
START=99
start() {
  sleep 30 # Make sure FW and iptables have loaded
  echo '### STARTING PORT MIRRORING TO [_ipaddress_] ###'
  iptables -t mangle -A POSTROUTING -j TEE --gateway [_ipaddress_]
  iptables -t mangle -A PREROUTING -j TEE --gateway [_ipaddress_]
}                                                               
```

Replace [_ipaddress_] with your Snort host IP address.

START=99 denotes the order in which scripts execute on boot. On my OpenWrt firmware, 95 is "done", so 99 ensures the script will load after all other system critical startup scripts run and load.

4. Save the file
5. Make the script executable and enabled on startup
```
# chmod +x /etc/init.d/[_script_file_]
# /etc/init.d/[_script_file_] enable  #places entry S99weather in /etc/rc.d/
```

Replace [_script_file_] with the name of the script
The second line will place a new symlink in /etc/rc.d - e.g., S99[_script_file_]]

6. ls /etc/rc.d and make sure the symlink is created.
7. Reboot the router and check the **Table: Mangle** section of the router web UI to make sure it is inserted.

Further reading: [OpenWrt Init Scripts Reference](http://wiki.openwrt.org/doc/techref/initscripts)

LIMITATIONS
- TEE will only send the cloned packet to the next hop
- It only works if both the source and destination host lives on the same subnet
- Because TEE modifies the ethernet frame destination MAC address, you lose the original data

Alternatives
https://code.google.com/p/port-mirroring/ 
This project is an alternative to iptables TEE. It also supports TZSP, which encapsulates the entire packet, making sure the data down to the ethernet frame layer is preserved.

... TODO ...
Next: Part 3

