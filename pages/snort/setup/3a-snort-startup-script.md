---
layout: page
title: Setting up Snort - Part 3a - Snort Startup Script
meta-description: run snort on startup, etc init, snort daemon,  ubuntu 14.04 lts
---

[< Back: Part 3 - Installing Snort](/pages/snort/setup/3-installing-snort)

Add the following script to have Snort daemon run on OS startup.

Create /etc/init/snort.conf with your favourite editor.

    ```
# sudo vi /etc/init/snort.conf
    ```

Contents of /etc/init/snort.conf

    ```
description "Snort NIDS Service"
stop on runlevel [!2345]
start on runlevel [2345]
script
        exec /usr/sbin/snort -q -u snort -g snort -c /etc/snort/snort.conf -i eth0 -D
end script
    ```

Replace ***eth0** if that is not the ethernet interface.

Make script executable.

    ```
sudo chmod +x /etc/init/snort.conf
    ```

Register script to run on OS startup.

    ```
# initctl list | grep snort
# /var/log/snort$ initctl list | grep snort
    ```

Restart Ubuntu and confirmt that snort ran on startup as daemon. 

    ```
# ps -e|grep -i snort
    ```

If ps returns a snort instance, then we are done!

    ```
  860 ?        00:20:48 snort
    ```

[< Back: Part 3 - Installing Snort](/pages/snort/setup/3-installing-snort)


