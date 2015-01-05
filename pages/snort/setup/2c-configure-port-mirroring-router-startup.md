---
layout: page
title: Setting up Snort - Part 2c - Configure iptables on Router Startup
meta-description: openwrt, iptables, init 
---

[< Back: Part 2 - Mirroring Network Traffic](/pages/snort/setup/2-mirroring-network-traffic)

1. SSH into your router.
2. Create a new file at /etc/init.d/ eg /etc/init.d/port-mirroring.
3. Write your startup script there. Here is a sample of what I use.

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

    START=99 denotes the order in which scripts execute on boot.

    On my OpenWrt firmware, 95 is "done", so 99 ensures the script will load after all other system critical startup scripts run and load.

4. Save the file.

5. Make the script executable and enabled on startup.

    ```
# chmod +x /etc/init.d/[_script_file_]
# /etc/init.d/[_script_file_] enable  #places entry S99weather in /etc/rc.d/
    ```

    Replace [\_script\_file\_] with the name of the script.

    The second line will place a new symlink in /etc/rc.d - e.g., S99[\_script\_file\_].

    Replace [\_script\_file\_] with the name of the script.

    The second line will place a new symlink in /etc/rc.d - e.g., S99[\_script\_file\_].

6. ls /etc/rc.d and make sure the symlink is created.

7. Reboot the router and check the **Table: Mangle** section of the router web UI to make sure it is inserted.

Further reading: [OpenWrt Init Scripts Reference](http://wiki.openwrt.org/doc/techref/initscripts)

[< Back: Part 2 - Mirroring Network Traffic](/pages/snort/setup/2-mirroring-network-traffic)

