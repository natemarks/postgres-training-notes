## tablespaces

pg_global  and pg_default
## create tablespace to customize the on-disk location 


filesystem dirs and permisisons must exist before creating teh tablespace
```
sudo su
mkdir -p /ssd_drive/base2
chown -R postgres:postgres  /ssd_drive
su - postgres

```


###  check tablespace name for databases
```console

postgres=# select oid, spcname, pg_tablespace_location(oid) from pg_tablespace;
 oid  |  spcname   | pg_tablespace_location 
------+------------+------------------------
 1663 | pg_default | 
 1664 | pg_global  | 
(2 rows)
postgres=# select oid, dattablespace, datname from pg_database ;
  oid  | dattablespace |  datname  
-------+---------------+-----------
 13806 |          1663 | postgres
     1 |          1663 | template1
 13805 |          1663 | template0
 16392 |          1663 | salesdb
 16387 |          1663 | edbstore
 16412 |          1663 | fdb

postgres=# create tablespace tblspc location '/ssd_drive/base2';
CREATE TABLESPACE
postgres=# create tablespace tblspc location '/ssd_drive/base2';
CREATE TABLESPACE
postgres=# select oid, spcname, pg_tablespace_location(oid) from pg_tablespace;
  oid  |  spcname   | pg_tablespace_location 
-------+------------+------------------------
  1663 | pg_default | 
  1664 | pg_global  | 
 16428 | tblspc     | /ssd_drive/base2
(3 rows)

postgres=# select oid, dattablespace, datname from pg_database ;
  oid  | dattablespace |  datname  
-------+---------------+-----------
 13806 |          1663 | postgres
     1 |          1663 | template1
 13805 |          1663 | template0
 16392 |          1663 | salesdb
 16387 |          1663 | edbstore
 16412 |          1663 | fdb
(6 rows)

postgres=# \db
           List of tablespaces
    Name    |  Owner   |     Location     
------------+----------+------------------
 pg_default | postgres | 
 pg_global  | postgres | 
 tblspc     | postgres | /ssd_drive/base2
(3 rows)

```


```bash
[postgres@ip-172-31-80-69 ~]$ find /ssd_drive/
/ssd_drive/
/ssd_drive/base2
/ssd_drive/base2/PG_14_202107181

```