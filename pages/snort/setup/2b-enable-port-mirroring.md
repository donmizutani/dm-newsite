---
layout: page
title: Setting up Snort - Part 2b - Enable Port Mirroring
meta-description: port mirroring, iptables, tee, home router, dd-wrt, openwrt
---

[<- Back: Part 2 - Mirroring Network Traffic](/pages/snort/setup/2-mirroring-network-traffic)

**Note: The rest of this guide will be based on OpenWrt, as the DD-WRT for my router did not include support for the required ROUTE and TEE iptables modules.**

### Turning on port mirroring

1. SSH into your router.
2. Run the following commands to install the iptables-mod-tee module.

    ```
# opkg update
# opkg list | grep -i tee
# opkg install iptables-mod-tee
    ```

3. Reboot the router.

4. Once rebooted, execute the following commands to mirror a copy of the data to your device with Snort installed.

    ```
# iptables -t mangle -A PREROUTING -j TEE --gateway [_ipaddress_]
# iptables -t mangle -A POSTROUTING -j TEE --gateway [_ipaddress_]
    ```

    where [\_ipaddress\_] is the IP address where your Snort host is located.

5. If all the previous commands executed without error, then you have successfully set up port mirroring.

### Verify port mirroring
1. Log into the web UI for your router.

2. Highlight the **Status** menu, and select **Firewall** from the drop-down menu list.

3. Scroll down to the **Table: Mangle** section and make sure the **Chain PREROUTING** and **Chain POSTROUTING** sections has an entry for TEE and your specified Snort host IP.

![Sample TEE configuration](/pages/snort/setup/2b-tee.png)

In the sample screenshot, "TEE gw:192.168.2.12" denotes the IP address of my Snort host where I am forwarding a copy of my packets to.

Note also that Source and Destination of 0.0.0.0/0 means the router is cloning all ingress and egress packets on the network.

If you want to limit to certain IP address, IP ranges and/ or subnets, you can use the -d and -s parameters to limit to single IP host or subnet, for destination and source respectively.

Please consult the iptables documentation for more info.

### Turning off port mirroring

    # iptables -F -t mangle

[<- Back: Part 2 - Mirroring Network Traffic](/pages/snort/setup/2-mirroring-network-traffic)

