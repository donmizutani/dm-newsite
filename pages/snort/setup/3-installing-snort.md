---
layout: page
title: Setting up Snort - Part 3 - Installing Snort
meta-description: installing snort, ubuntu 14.04 lts
---

[< Previous: Part 2 - Mirroring Network Traffic](/pages/snort/setup/2-mirroring-network-traffic)

This will cover installing Snort on Ubuntu 14.04 LTS specifically. You can find other guides for your OS / distribution [on the official home page](https://www.snort.org/documents).

There is an excellent guide for installing [Snort 2.9.6.2 on Ubuntu 12 LTS and 14 LTS](https://www.snort.org/documents). However while working through this guide myself, I have found there were several incorrect or extra required steps. Due to this, I decided to write this purely on my personal experience.

## 1. Disable LRO and GRO
The guide on the official home page recommends disabling these two features the ethernet interface.

```
# sudo apt-get install -y ethtool
# sudo ethtool -K eth0 gro off
# sudo ethtool -K eth0 lro off
```

Replace **eth0** if that is not the ethernet interface.

## 2. Setting up Snort

At the time of writing, Snort 2.9.7.0 and DAQ 2.0.4 are the latest stable releases. Where there is a new version released, please adjust as required.

### 2.1 Install build dependencies.

```
# sudo apt-get install -y build-essential
# sudo apt-get install -y libpcap-dev libpcre3-dev libdumbnet-dev
# sudo apt-get install -y bison flex
# sudo apt-get install -y zlib1g-dev
```

Create a workspace directory to compile sources. We will follow the same directory as the official guide - under ~/snort_src

````
# mkdir ~/snort_src
# cd ~/snort_src
```

### 2.2 Build and install DAQ from source

At the time of writing, 2.0.4 is the latest version. Modify the wget link as required when a new version is released.

```
# wget https://www.snort.org/downloads/snort/daq-2.0.4.tar.gz
# tar zxvf daq-2.0.4.tar.gz
# cd daq-2.0.4
# ./configure
# make
# sudo make install
```

### 2.3 Build and install Snort from source

At the time of writing, 2.9.7.0 is the latest version. Modify the wget link as required when a new version is released.

One tweak I have added is passing **--enable-reload** to the configure script.

By including this, Snort will restart on the fly for such operations as updated rules files.

This is especially valuable when combined with PulledPork [covered in part 4](/pages/snort/setup/4-installing-pulledpork) on a cron schedule - Snort will automatically restart if PulledPork updates the rules.

```
# cd ~/ snort_src
# wget https://www.snort.org/downloads/snort/snort-2.9.7.0.tar.gz
# tar zxvf snort-2.9.7.0.tar.gz
# cd snort-2.9.7.0
# ./configure --enable-sourcefire --enable-reload
# make
# sudo make install
# sudo ldconfig
# sudo ln -s /usr/local/bin/snort /usr/sbin/snort
```

Once Snort is built and installed properly, let's run it to make sure it starts.

```
# snort -V
```

You should see output similar to below (adjust for newer versions)

```
   ,,_     -*> Snort! <*-
  o"  )~   Version 2.9.7.0 GRE (Build 149) 
   ''''    By Martin Roesch & The Snort Team: http://www.snort.org/contact#team
           Copyright (C) 2014 Cisco and/or its affiliates. All rights reserved.
           Copyright (C) 1998-2013 Sourcefire, Inc., et al.
           Using libpcap version 1.5.3
           Using PCRE version: 8.31 2012-07-06
           Using ZLIB version: 1.2.8
```

### 2.4 Initial Snort Configuration

We will setup an initial configuration for Snort.

- Setup unprivileged account
- "touch" default files
- All configuration files will be saved to /etc/snort
- Logging is to /var/log/snort directory.
  - My recommendation for logging is to move this to a central logging system.
  - My setup currently stores files in an NFS location (upgrading to NAS in the future). syslog also works great for this.

### 2.4.1 Create unprivileged Snort  account and required initial files

The commands below are direct copy and paste from the guide on the official page. Run these one at a time and if you run into errors, action them accordingly.

```
# sudo groupadd snort
# sudo useradd snort -r -s /sbin/nologin -c SNORT_IDS -g snort
# sudo mkdir /etc/snort
# sudo mkdir /etc/snort/rules
# sudo mkdir /etc/snort/preproc_rules
# sudo mkdir /var/log/snort
# sudo mkdir /usr/local/lib/snort_dynamicrules
# sudo touch /etc/snort/rules/white_list.rules /etc/snort/rules/black_list.rules /etc/snort/ rules/local.rules
# sudo chmod -R 5775 /etc/snort
# sudo chmod -R 5775 /var/log/snort
# sudo chmod -R 5775 /usr/local/lib/snort_dynamicrules
# sudo chown -R snort:snort /etc/snort
# sudo chown -R snort:snort /var/log/snort
# sudo chown -R snort:snort /usr/local/lib/snort_dynamicrules
# sudo cp ~/snort_src/snort-2.9.7.0/etc/*.conf* /etc/snort
# sudo cp ~/snort_src/snort-2.9.7.0/etc/*.map /etc/snort
```

### 2.4.2 Update snort.conf

Next, we will disable (comment out) all the default rule files included in snort.conf. This is because we will use PulledPork to update the rules.

```
# sudo sed -i ’s/include \$RULE\_PATH/#include \$RULE\_PATH/’ /etc/snort/snort.conf
```

Edit the snort.conf and set your network configuration.

**Note: these line numbers may be off if future releases of Snort add and/ or remove certain configuration values.**

- Line 45: update **HOME_NET** to your internal subnet
- Line 48: set to **!$HOME_NET** - i.e., every other network range not part of your home network

E.g., if your home network is in the 192.168.1.0/24 subnet,

```
ipvar HOME_NET 192.168.1.0/24
ipvar EXTERNAL_NET !$HOME_NET
```

From line 104 onwards, update the following variables.

```
var RULE_PATH /etc/snort/rules
var SO_RULE_PATH /etc/snort/so_rules
var PREPROC_RULE_PATH /etc/snort/preproc_rules
var WHITE_LIST_PATH /etc/snort/rules
var BLACK_LIST_PATH /etc/snort/rules
```

Uncomment line 541 to allow you to add your own custom rules to a local.rules file.

```
include $RULE_PATH/local.rules
```

### 2.5 Testing snort.conf

Once the changes in section 2.4 has been made, let us make sure there are no errors.

It is good practice to verify your configuration after any change, no matter how small the update may be.

```
# sudo snort -T -c /etc/snort/snort.conf
```

If all goes well, a lot of text will scroll by and Snort should exit with the last 2 lines as follows.

```
Snort successfully validated the configuration!
Snort exiting
```

If Snort outputs an error message, please go over your snort.conf and double check for errors.

### 2.5.1 Creating a custom Snort rule to test Snort

Add the following rule to a new line in /etc/snort/rules/local.conf

```
alert icmp any any -> any any (msg:"ICMP packet from all, to all"; sid:10000001; rev:001;)
```

This rule simply raises an alert if Snort detects an ICMP packet sent from any IP, any port, to any IP, any port. These 4 fields reflects the 4 "any" words respectively in the rule above. 

### 2.5.2 Testing Snort with the new custom rule

Verify there are no errors with the snort.conf.

```
# sudo snort -T -c /etc/snort/snort.conf
```

If successful, let us run Snort in console mode.

```
# sudo /usr/local/bin/snort -A console -q -g snort -u snort -c /etc/snort/snort.conf -i eth0
```

Replace **eth0** if that is not the ethernet interface.

Ping a machine on your home network.

To also test that port mirroring, which was set up in [part 2](/pages/snort/setup/2-mirroring-network-traffic), is working as intended, ping a machine on the network where Snort is not installed on.

You should see Snort displaying alerts for this.

Example (output will differ depending on logging format):

```
[**] [1:10000001:1] ICMP packet from all, to all [**]
[Priority: 0]
1/7-21:00:39.652451 192.168.1.2 -> 192.168.1.3
ICMP TTL:51 TOS:0x0 ID:0 IpLen:20 DgmLen:32 DF
Type:8  Code:0  ID:13158   Seq:4508  ECHO

[**] [1:10000001:1] ICMP packet from all, to all [**]
[Priority: 0]
1/7-21:00:39.652604 192.168.1.3 -> 192.168.1.2
ICMP TTL:64 TOS:0x0 ID:54714 IpLen:20 DgmLen:32 DF
Type:0  Code:0  ID:13158  Seq:4508  ECHO REPLY
```

If you got this far, congratulations! You have a working Snort setup.

Just one more step! We don't want to manually run Snort like this, we want it to run in daemon mode, and on OS startup, so you don't have to manually intervene.

[Part 3a - Snort Startup Script](/pages/snort/setup/3a-snort-startup-script)

We are not done yet! All rules are currently disabled due to the operations in section 2.4.2.

The next part will cover setting up PulledPork, which will automatically update Snort rules.

[Next > Part 4 - Installing PulledPork](/pages/snort/setup/4-installing-pulledpork)

