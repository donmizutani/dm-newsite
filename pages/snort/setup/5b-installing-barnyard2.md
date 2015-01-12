

### Install MySQL client (Ubuntu)

The MySQL client must be installed on the **same VM or machine** as Barnyard2, as Barnyard2 will use the MySQL client to insert data into the specified MySQL server instance.

```
# sudo apt-get install mysql-client autoconf libtool libmysqlclient-dev
```

Because it will be using the "batch mode" of Barnyard2, the MySQL database will be designed so it can be trashed (deleted) and re-created (inserted).

TODO: barnyard to iterate over each file with one -o param because it can segfauly on certain logs, and also fail to continue onto the 2nd file onwards...

