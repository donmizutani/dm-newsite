---
layout: page
title: Setting up Snort - Part 5 - Installing Barnyard2 and MySQL
meta-description: installing barnyard2, configure mysql, snort
---

[< Previous: Part 4 - Installing PulledPork](/pages/snort/setup/3-installing-pulledpork)

As discussed [here](/pages/snort/setup/4a-system-separation), having everything on the same VM or machine is not desirable. This page will be a modification of the steps [from the official home page](https://www.snort.org/documents).

Essentially, Barnyard2 and MySQL live on separate VMs and machines.

Because of this, you have to be more careful when it comes to connecting it all together.
- How to access and retrieve the snort logs for Barnyard2 to process.
- Set up a MySQL account for Barnyard2 use, with only the required privileges.
- Setup proper account privileges in MySQL - remove default created anonymous accounts, limit root account to localhost only, and so on.

Another difference to the steps [from the official home page](https://www.snort.org/documents) is that on my home setup, Barnyard2 and MySQL will not be running 24/7.

I do not see a need for that, and will only do batch processing of Snort logs when needed. Saves on the electricity bill :)

There are three parts to this section, of which the third part is optional.

1. Configuring the MySQL server instance for Barnyard2 use.

[Part 5a - Configuring MySQL for Barnyard2](/pages/snort/setup/5a-configuring-mysql-for-barnyard2)

2. Installing and configuring Barnyard2 with a sample shell script to trash the database and insert data rows with the Barnyard2  "batch mode".

[Part 5b - Installing and Configuring Barnyard2](/pages/snort/setup/5b-installing-barnyard2)

3. Sample script to trash the database, process and insert all Snort log files in a specified directory with Barnyard2 "Batch Mode".

As mentioned above, I did not care to run this 24/7, so I wrote a script to automate all this.

[Part 5c - Automate Barnyard2 with Batch Mode](/pages/snort/setup/5c-automate-barnyard2-batch)

