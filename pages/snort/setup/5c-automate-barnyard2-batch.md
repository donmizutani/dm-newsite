---
layout: page
title: Setting up Snort - Part 5c - Automate Barnyard2 with Batch Mode
meta-description: automate barnyard2 batch mode
---

[< Back: Part 5 - Installing Barnyard2 and MySQL](/pages/snort/setup/5-installing-barnyard2-mysql)

This will allow you to run Barnyard2 on demand on on a schedule.

In order to automate this, we need a script that can do the following steps.

1. Drop the Barnyard2 snort database.
2. Create the Barnyard2 snort database.
3. Import the Barnyard MySQL schema.
4. Copy all files from the specified Snort log folder to a temporary folder that Barnyard2 will process from.
5. Loop through every Snort log file copied to the temporary folder and run with Barnyard2 with -o [_logfile_]

Note that, although the author's [documentation](https://github.com/firnsy/barnyard2) says it supports specifying multiple files with the -o parameter, I have found that the latest version will not process more than one file.

I have also noticed segfaults occuring in this tool, so calling Barnyard2 for one file at a time ensures that subsequent files get processed even if the tool segfaults on a previous file.

Regarding segfaults, there seems to be no solution other than downgrading to a version before 2-1.10 and before.


```
#!/bin/bash

echo "Dropping existing snort db..."
echo "drop database snort;" | mysql --host=[_mysql_server_ipaddress] --user=barnyard2 --password=[_password_]

echo "Creating snort db..."
echo "create database snort;" | mysql --host=[_mysql_server_ipaddress] --user=barnyard2 --password=[_password_]

echo "Import snort table schema..."
mysql --host=[_mysql_server_ipaddress] --user=barnyard2 --password=[_password_] -D snort < ~/snort_src/barnyard2-master/schemas/create_mysql

echo "Copying snort logs to local work folder..."
timestamp=`date +%s`
timestampDir=tmp$timestamp
mkdir $timestampDir
cp /mnt/nfs/media/usb0/snort/* $timestampDir

echo "Collating together log files to process..."
logFiles=`ls $timestampDir -1 | tr "\\n" " "`
echo "Snort logs to process: " $logFiles

echo "Running Barnyard2..."
cd $timestampDir

# Loop through all $logFiles and process one-by-one in "Batch Mode"
for f in $logFiles
do
    sudo barnyard2 -c /etc/snort/barnyard2.conf -o $logFiles
done

echo "Cleaning up..."
cd ..
#rm -rf $timestampDir

echo "Barnyard2 batch run finished."
```

[< Back: Part 5 - Installing Barnyard2 and MySQL](/pages/snort/setup/5-installing-barnyard2-mysql)

