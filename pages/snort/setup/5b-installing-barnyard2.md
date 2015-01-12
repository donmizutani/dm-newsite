---
layout: page
title: Setting up Snort - Part 5b - Installing and Configuring Barnyard2
meta-description: install and configure barnyard2, snort, ubuntu 14.04 lts
---

[< Back: Part 5 - Installing Barnyard2 and MySQL](/pages/snort/setup/5-installing-barnyard2-mysql)

### Install MySQL client

The MySQL client must be installed on the **same VM or machine** as Barnyard2, as Barnyard2 will use the MySQL client to insert data into the specified MySQL server instance.

```
# sudo apt-get install mysql-client autoconf libtool libmysqlclient-dev
```


### Install prerequisite libraries

```
# sudo apt-get install checkinstall
# wget http://libdnet.googlecode.com/files/libdnet-1.12.tgz
# tar zxvf libdnet-1.12.tgz
# ./configure CFLAGS=-fPIC; make
# sudo checkinstall
# sudo dpkg -i libdnet_1.12-1_amd64.deb
# sudo ln -s /usr/local/lib/libdnet.1.0.1 /usr/lib/libdnet.1
```

### Create unprivileged account

If Barnyard2 is to be installed on a VM or machine separate to the Snort host, then you will need to create a "snort" user and group that can be used below.

To be honest, any user and group is fine. Just for convenience, let's reuse the commands.

```
# sudo groupadd snort
# sudo useradd snort -r -s /sbin/nologin -c BARNYARD2 -g snort
```

### Build and install Barnyard2

```
# cd ~/snort_src
# wget https://github.com/firnsy/barnyard2/archive/master.tar.gz -O barnyard2.tar.gz
# tar zxvf barnyard2.tar.gz 
# cd barnyard2-master
# autoreconf -fvi -I ./m4
```

Run one of the following 2 commands.

If you are running a 64-bit OS:

```
# ./configure --with-mysql --with-mysql-libraries=/usr/lib/x86_64-linux-gnu
```

**else***, for 32-bit OS:

```
# ./configure --with-mysql --with-mysql-libraries=/usr/lib/i386-linux-gnu
```

If no errors show up, build the Barnyard2 binaries.

```
# make
# sudo make install
# sudo cp etc/barnyard2.conf /etc/snort
# sudo mkdir /var/log/barnyard2
```

Change user and group ownership to the "snort" user and group.

```
# sudo chown snort.snort /var/log/barnyard2
# sudo touch /var/log/snort/barnyard2.waldo
# sudo chown snort.snort /var/log/snort/barnyard2.waldo
# sudo touch /etc/snort/sid-msg.map
```

### Creating the Barnyard2 database

Now we need to create the db/schema for Barnyard2.


```
# echo "create database snort;" | mysql --host=[_mysql_server_ipaddress_] --user=barnyard2 --password=[_password_]
# mysql --host=[_mysql_server_ipaddress_] --user=barnyard2 --password=[_password_] -D snort < ~/snort_src/barnyard2-master/schemas/create_mysql
```

Replacing
- [\_mysql\_server\_ipaddress\_] with the MySQL ip address.
- [\_password\_] with the barnyard2 account password.
  - The above assumes you have created an account "barnyard2" per [Part 5a](/pages/snort/setup/5a-configuring-mysql-for-barnyard2).

### Configuring Barnyard2 to use your MySQL server instance

At the following line to the end of /etc/snort/barnyard2.conf

```
output database: log, mysql, user=barnyard2 password=[_password_] dbname=snort host=[_mysql_server_ipaddress_]
```

Replacing
- [\_mysql\_server\_ipaddress\_] with the MySQL ip address.
- [\_password\_] with the barnyard2 account password.
  - The above assumes you have created an account "barnyard2" per [Part 5a](/pages/snort/setup/5a-configuring-mysql-for-barnyard2).

### Securing MySQL account passwords

Because the MySQL account password is stored in barnyard2.conf, we should secure this file so only root can read it.

```
# sudo chmod o-r /etc/snort/barnyard2.conf
```

### Running Barnyard2

We can finally test out Barnyard2!

```
sudo barnyard2 -c /etc/snort/barnyard2.conf -o [_snort_log_file_]
```

Replacing
- [\_snort\_log\_file\_] with a Snort log filename

Once Barnyard2 runs in "Batch Mode" and completes, query your MySQL server instance to make sure rows were inserted successfully.

```
SELECT COUNT(*) FROM snort.event;
```

You should see it return 1 or more.

If you get error messages, double check all steps up til now for errors.

### Barnyard2 "Batch Mode" automation

As mentioned previously, I did not care to run Barnyard2 24/7 and in real time, inserting the data into MySQL.

[Part 5c](/pages/snort/setup/5c-automate-barnyard2-batch) will introduce a sample script to automate trashing the database, feed all Snort log files in a specified directory through Barnyard2 "Batch Mode" and have it insert into the database.

[< Back: Part 5 - Installing Barnyard2 and MySQL](/pages/snort/setup/5-installing-barnyard2-mysql)
