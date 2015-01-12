---
layout: page
title: Setting up Snort - Part 5a - Configuring MySQL for Barnyard2
meta-description: configure mysql server for barnyard2, snort
---

[< Back: Part 5 - Installing Barnyard2 and MySQL](/pages/snort/setup/5-installing-barnyard2-mysql)

This guide will not explain how to install the MySQL server. It is up to you to decide Where the MySQL server is installed.

For reference, I installed my MySQL server instance on a Mac Mini.

MySQL packages can be downloaded [here](http://dev.mysql.com/downloads).

Recommended packages:

- MySQL Community Server (server, must)
- MySQL Workbench (GUI client, optional, depending on your environment)

### Remove unnecessary default accounts

If you are installing Barnyard2 and the MySQL client on a VM or machine that is separate from the MySQL server, then it is recommended to

- Remove all anonymous accounts created by default on installation
- Restrict the "root" account to localhost login only

### Create an account for Barnyard2

As the Barnyard2 instance is installed on a different VM or machine to the MySQL server, you want to limit the access privilege on the account.

- Required table operations
  - Insert
  - Select
  - Delete
  - Alter
  - Update

- Required database operations:
  - Create
  - Drop

- Limit account login from the Barnyard2 host IP. For this to work, make sure you have set the Barnyard2 host to a static IP.

- Operations not required on this account
  - MySQL admin operation related privileges.

Log into the MySQL instance and execute the following query, to create a
```
2. grant create, insert, select, delete, alter, update, drop on snort.* to barnyard2@ip-address identified by 'passwordhere'
```

Replace **ip-address** with the Barnyard2 host IP, **passwordhere** with the desired password for this account.

When the above query successfully executes, a new account **barnyard2** will be created, with the above required operation privileges and limited access to the Barnyard2 host IP.

Below is a sample of the created account I use, taken from  Mac OS MySQL Workbench.

This user should have not be allowed any "Administrative Roles".

![Administrative Roles](/pages/snort/setup/5a-sample-admin-roles.png)

This user should have the appropriate database operation privileges.

![Schema Privileges](/pages/snort/setup/5a-sample-schema-privileges.png)

Once this account is setup, we are ready to install and configure Barnyard2 to use this account!

[< Back: Part 5 - Installing Barnyard2 and MySQL](/pages/snort/setup/5-installing-barnyard2-mysql)
