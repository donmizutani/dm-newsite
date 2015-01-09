--
layout: page
title: Setting up Snort - Part 4a - Separate out support systems
meta-description: separate snort support systems, ubuntu 14.04 lts
---

[< Back: Part 4 - Installing PulledPork](/pages/snort/setup/4-installing-pulledpork)

As a bit of advice, the guide on the homepage has everything installed on the same machine/VM.

There are possible scaling issues with this, especially with MySQL.

Databases are extremely resource intensive systems, especially on RAM/memory. No memory is ever enough for a database, **they tend to gobble up as much RAM as you give it!**

This is because in order for databases to search and return data quickly, indexed table data is preferably all stored in RAM.

In a tug of war for resources between MySQL and Snort on the same system, MySQL will always win.

This then means Snort will perform inefficiently or even lose record of packets.

If you are running this setup under VMs, and have a multi-core CPU e.g. ESXi, consider giving a dedicated core to the MySQL VM.

Of course, when you start out, and do not have much data it would not matter - but the end game is all about seamless scalability!

[< Back: Part 4 - Installing PulledPork](/pages/snort/setup/4-installing-pulledpork)

