## tablespaces
You can change the tablespace of an existing table and PG will move
it for you but the table will be loicked during the move

database and tables can be in different tablespaces

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


### TABLESPACE LAB TWO WITH DB OBJECTS

```console
postgres=# \c postgres
You are now connected to database "postgres" as user "postgres".

postgres=# select relfilenode,reltablespace,relname from pg_Class where relname='sample';
 relfilenode | reltablespace | relname 
-------------+---------------+---------
       16429 |             0 | sample
(1 row)

postgres=# select oid,spcname,pg_tablespace_location(oid) from pg_tablespace;
select relfilenode,reltablespace,relname from pg_Class where relname='sample';
  oid  |  spcname   | pg_tablespace_location 
-------+------------+------------------------
  1663 | pg_default | 
  1664 | pg_global  | 
 16428 | tblspc     | /ssd_drive/base2
(3 rows)

 relfilenode | reltablespace | relname 
-------------+---------------+---------
       16429 |             0 | sample
(1 row)

postgres=# create table sales_2021(id int);
create table sales_2022(id int) tablespace tblspc ;
select relfilenode,reltablespace,relname from pg_Class where relname like 'sales%';
CREATE TABLE
CREATE TABLE
 relfilenode | reltablespace |  relname   
-------------+---------------+------------
       16432 |             0 | sales_2021
       16435 |         16428 | sales_2022
(2 rows)

postgres=# create database inventory tablespace tblspc ;
 select oid,dattablespace, datname from pg_database;
 select relfilenode,reltablespace,relname from pg_Class where relname like 'sales%';
CREATE DATABASE
  oid  | dattablespace |  datname  
-------+---------------+-----------
 13806 |          1663 | postgres
     1 |          1663 | template1
 13805 |          1663 | template0
 16392 |          1663 | salesdb
 16387 |          1663 | edbstore
 16412 |          1663 | fdb
 16438 |         16428 | inventory
(7 rows)

 relfilenode | reltablespace |  relname   
-------------+---------------+------------
       16432 |             0 | sales_2021
       16435 |         16428 | sales_2022
(2 rows)




postgres=# create index sales_idx_2021 on sales_2021(id) tablespace tblspc ;
select relfilenode,reltablespace,relname from pg_Class where relname like 'sales%';
CREATE INDEX
 relfilenode | reltablespace |    relname     
-------------+---------------+----------------
       16432 |             0 | sales_2021
       16435 |         16428 | sales_2022
       16439 |         16428 | sales_idx_2021
(3 rows)


postgres=# alter table sales_2021 set tablespace tblspc ;
select relfilenode,reltablespace,relname from pg_Class where relname like 'sales%';
ALTER TABLE
 relfilenode | reltablespace |    relname     
-------------+---------------+----------------
       16440 |         16428 | sales_2021
       16435 |         16428 | sales_2022
       16439 |         16428 | sales_idx_2021
(3 rows)


alter database salesdb tablespace tblspc;
```


```bash
[root@ip-172-31-80-69 ec2-user]# find /ssd_drive/ -type d
/ssd_drive/
/ssd_drive/base2
/ssd_drive/base2/PG_14_202107181
/ssd_drive/base2/PG_14_202107181/13806
/ssd_drive/base2/PG_14_202107181/16438
/ssd_drive/base2/PG_14_202107181/16392
```


### LAB# move the table spoace


```console
postgres=# \db
           List of tablespaces
    Name    |  Owner   |     Location     
------------+----------+------------------
 pg_default | postgres | 
 pg_global  | postgres | 
 tblspc     | postgres | /ssd_drive/base2
(3 rows)

```


```console
[root@ip-172-31-80-69 ec2-user]# su - postgres
Last login: Wed Sep 14 14:27:42 UTC 2022 on pts/0
[postgres@ip-172-31-80-69 ~]$ pg_ctl -D /opt/edbstore/data stop
waiting for server to shut down.... done
server stopped
[postgres@ip-172-31-80-69 ~]$ exit
logout
[root@ip-172-31-80-69 ec2-user]# mkdir /certfirst_drive
[root@ip-172-31-80-69 ec2-user]# chown -R postgres:postgres /certfirst_drive/
[root@ip-172-31-80-69 ec2-user]# su - postgres 
Last login: Wed Sep 14 15:31:54 UTC 2022 on pts/1
[postgres@ip-172-31-80-69 ~]$ ls /ssd_drive/
base2
[postgres@ip-172-31-80-69 ~]$ ls /ssd_drive/base2/
PG_14_202107181
[postgres@ip-172-31-80-69 ~]$ ls -lrt /opt/edbstore/data/pg_tblspc/
total 0
lrwxrwxrwx. 1 postgres postgres 16 Sep 14 14:27 16428 -> /ssd_drive/base2
[postgres@ip-172-31-80-69 ~]$ mv /ssd_drive/base2/ /certfirst_drive/
[postgres@ip-172-31-80-69 ~]$ cd /opt/edbstore/data/pg_tblspc/
[postgres@ip-172-31-80-69 pg_tblspc]$ ll
total 0
lrwxrwxrwx. 1 postgres postgres 16 Sep 14 14:27 16428 -> /ssd_drive/base2
[postgres@ip-172-31-80-69 pg_tblspc]$ ln -fs  /certfirst_drive/base2/ 16428 
[postgres@ip-172-31-80-69 pg_tblspc]$ ll
total 0
lrwxrwxrwx. 1 postgres postgres 23 Sep 14 15:36 16428 -> /certfirst_drive/base2/
[postgres@ip-172-31-80-69 pg_tblspc]$ pg_ctl -D /opt/edbstore/data -l /opt/edbstore/logfile start
waiting for server to start.... done
server started


```


```console
postgres=# \db
               List of tablespaces
    Name    |  Owner   |        Location         
------------+----------+-------------------------
 pg_default | postgres | 
 pg_global  | postgres | 
 tblspc     | postgres | /certfirst_drive/base2/
(3 rows)
```


### LAB4 Tablesoaces
```console
[postgres@ip-172-31-80-69 pg_tblspc]$ exit
logout
[root@ip-172-31-80-69 ec2-user]# mkdir -p /home/tbl1
[root@ip-172-31-80-69 ec2-user]# mkdir -p /home/tbl2
[root@ip-172-31-80-69 ec2-user]# chown -R postgres:postgres /home/tbl1
[root@ip-172-31-80-69 ec2-user]# chown -R postgres:postgres /home/tbl2
[root@ip-172-31-80-69 ec2-user]# su - postgres 
Last login: Wed Sep 14 15:33:41 UTC 2022 on pts/1

```

```console
postgres=# create tablespace tbl1 location '/home/tbl1';
create tablespace tbl2 location '/home/tbl2';
CREATE TABLESPACE
CREATE TABLESPACE


postgres=# select oid,spcname,pg_tablespace_location(oid) from pg_tablespace;
  oid  |  spcname   | pg_tablespace_location  
-------+------------+-------------------------
  1663 | pg_default | 
  1664 | pg_global  | 
 16428 | tblspc     | /certfirst_drive/base2/
 16441 | tbl2       | /home/tbl2
 16442 | tbl1       | /home/tbl1
(5 rows)

postgres=# create database db1 tablespace tbl1 ;
create database db2 tablespace tbl2 ;
CREATE DATABASE
CREATE DATABASE


postgres=# \c db1
You are now connected to database "db1" as user "postgres".
db1=# create table employee(id int) tablespace tbl2 ;
CREATE TABLE
db1=# create index employee_idx on employee(id) tablespace tbl1 ;
CREATE INDEX
db1=# 


\c db2;

create table hr(id int) tablespace tbl1;
create table accounts(id int) tablespace tbl2;
create index accounts_idx on accounts(id) tablespace tbl2 ;
db1=# \c db2
You are now connected to database "db2" as user "postgres".
db2=# create table hr(id int) tablespace tbl1;
CREATE TABLE
db2=# 
db2=# create table accounts(id int) tablespace tbl2;
CREATE TABLE
db2=# create index accounts_idx on accounts(id) tablespace tbl2 ;
CREATE INDEX
db2=# 
db2=# select oid,spcname,pg_tablespace_location(oid) from pg_tablespace;
  oid  |  spcname   | pg_tablespace_location  
-------+------------+-------------------------
  1663 | pg_default | 
  1664 | pg_global  | 
 16428 | tblspc     | /certfirst_drive/base2/
 16441 | tbl2       | /home/tbl2
 16442 | tbl1       | /home/tbl1
(5 rows)


```