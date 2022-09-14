#  2022-09-13


can table partitioning be configured in RDS?
can tablespaces be confgured on RDS?

look up Db ACID

validate replication/migration?

measure performance?

care adn feeding -  indices, etc

postgres llogging in aws?

recovery/availability:
 - streaming replication
 - replication slots
 - hot backup: pg_basebackup
 - PITR


 read replicas?
  ow to monitor to see if read replicas would help

  https://docs.google.com/document/d/16gabGHZKeAAlDBvC-TBi2zwATDxxSiWIjiPVI_AtY4M/edit

  suggestion: the installation should be scripts/pre-executed with the ijnstallation script shared as a reference

install script
```
yum install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
yum install -y postgresql14-server

mkdir -p /db/pgsql
usermod -d /db/pgsql postgres
cp /root/.bash_profile /db/pgsql/
echo "export PATH=/usr/pgsql-14/bin:$PATH" >> /db/pgsql/.
chown postgres:postgres /usr/pgsql-14/ -R
chown postgres:postgres /db/pgsql/ -R
```

check installation
```bash
su - postgres
Last login: Tue Sep 13 14:36:15 IST 2022 on pts/1
-bash-4.2$ 
-bash-4.2$ pwd
/db/pgsql
-bash-4.2$ 
-bash-4.2$ psql --version
psql (PostgreSQL) 14.5
-bash-4.2$ 
-bash-4.2$ initdb --version
initdb (PostgreSQL) 14.5

```
```bash
cd /db/pgsql
mkdir data
initdb -D data
cd data
pg_ctl -D /db/pgsql/data/  start

psql -p 5432  -U postgres -d postgres
select datname from pg_database;
```

```sql
create database edbstore;
select oid, datname from pg_database;
```

```bash
createdb -p 5432 -D postgres salesdb
```

```
select current_schemas(true);
select current_database();
show search_path;
create table sample (id int, name carchar(10));
```


==============================

CREATING NEW INSTANCE / CLUSTER

Steps :

-bash-4.2$ pwd
/db/pgsql
-bash-4.2$ 
-bash-4.2$ mkdir data
-bash-4.2$ 
-bash-4.2$ initdb --version
initdb (PostgreSQL) 14.5
-bash-4.2$ 
-bash-4.2$ initdb -D data


-bash-4.2$ cd data/
-bash-4.2$ 
-bash-4.2$ ls

-bash-4.2$ vi postgresql.conf 

port = 5432

:wq

-bash-4.2$ pg_ctl -D /db/pgsql/data/ start
waiting for server to start....2022-09-13 21:21:11.829 IST [10936] LOG:  redirecting log output to logging collector process
2022-09-13 21:21:11.829 IST [10936] HINT:  Future log output will appear in directory "log".
 done
server started
-bash-4.2$ 
-bash-4.2$ netstat -pant | grep postgres
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
tcp        0      0 127.0.0.1:5432          0.0.0.0:*               LISTEN      10936/postgres      
tcp6       0      0 ::1:5432                :::*                    LISTEN      10936/postgres   


-bash-4.2$ psql -p 5432 -U postgres -d postgres
psql (14.5)
Type "help" for help.

postgres=# show data_directory;
 data_directory 
----------------
 /db/pgsql/data
(1 row)

postgres=# select datname from pg_database;
  datname  
-----------
 postgres
 template1
 template0
(3 rows)

postgres=# \conninfo
You are connected to database "postgres" as user "postgres" via socket in "/var/run/postgresql" at port "5432".
postgres=# 






=======================CREATING AND MANAGING DATABASE 

postgres=# \conninfo
You are connected to database "postgres" as user "postgres" via socket in "/var/run/postgresql" at port "5432".
postgres=# 
postgres=# 
postgres=# 
postgres=# 
postgres=# create database edbstore;
CREATE DATABASE
postgres=# 
postgres=# select datname from pg_database;
  datname  
-----------
 postgres
 edbstore
 template1
 template0
(4 rows)

postgres=# \q
-bash-4.2$ 
-bash-4.2$ createdb -p 5432 -U postgres salesdb
-bash-4.2$ 
-bash-4.2$ 
-bash-4.2$ psql -p 5432 -U postgres -d salesdb
psql (14.5)
Type "help" for help.

 
 select datname from pg_database;
  datname  
-----------
 postgres
 edbstore
 template1
 template0
 salesdb
(5 rows)

 show data_directory;
 data_directory 
----------------
 /db/pgsql/data
(1 row)


 select oid,datname from pg_database;
  oid  |  datname  
-------+-----------
 14486 | postgres
 16384 | edbstore
     1 | template1
 14485 | template0
 16385 | salesdb
(5 rows)

 \q
-bash-4.2$ 
-bash-4.2$ cd /db/pgsql/data/base/
-bash-4.2$ 
-bash-4.2$ ls
1  14485  14486  16384  16385
-bash-4.2$ 
-bash-4.2$ 
-bash-4.2$ 
-bash-4.2$ 




























==========================DATAFILE 

-bash-4.2$ whoami 
postgres
-bash-4.2$ 
-bash-4.2$ psql -p 5432 -U postgres -d postgres
psql (14.5)
Type "help" for help.

postgres=# select current_schemas(true);
   current_schemas   
---------------------
 {pg_catalog,public}
(1 row)

postgres=# select current_database();
 current_database 
------------------
 postgres
(1 row)

postgres=# show search_path ;
   search_path   
-----------------
 "$user", public
(1 row)

postgres=# create table sample(id int,name varchar(10));
CREATE TABLE
postgres=# 
postgres=# \dt
         List of relations
 Schema |  Name  | Type  |  Owner   
--------+--------+-------+----------
 public | sample | table | postgres
(1 row)

postgres=# select relfilenode,relname from pg_class where relname='sample';
 relfilenode | relname 
-------------+---------
       16386 | sample
(1 row)

postgres=# 
postgres=# select oid,datname from pg_database;
  oid  |  datname  
-------+-----------
 14486 | postgres
 16384 | edbstore
     1 | template1
 14485 | template0
 16385 | salesdb
(5 rows)

postgres=# \q
-bash-4.2$ 
-bash-4.2$ cd /db/pgsql/data/base/14486/
-bash-4.2$ 
-bash-4.2$ du -sh 16386
0    16386
-bash-4.2$ 
-bash-4.2$ 
-bash-4.2$ psql -p 5432 -U postgres -d postgres -c "insert into sample values(1,'postgres');"
INSERT 0 1
-bash-4.2$ 
-bash-4.2$ du -sh 16386
8.0K    16386
-bash-4.2$ 
-bash-4.2$ psql -p 5432 -U postgres -d postgres -c "insert into sample values(generate_Series(2,200),'postgres');"
INSERT 0 199
-bash-4.2$ 
-bash-4.2$ 
-bash-4.2$ du -sh 16386
16K    16386
-bash-4.2$ psql -p 5432
psql (14.5)
Type "help" for help.

postgres=# 
postgres=# select relpages,reltuples from pg_Class where relname='sample';
 relpages | reltuples 
----------+-----------
        0 |        -1
(1 row)

postgres=# 
postgres=# analyze sample ;
ANALYZE
postgres=# 
postgres=# select relpages,reltuples from pg_Class where relname='sample';
 relpages | reltuples 
----------+-----------
        2 |       200
(1 row)


```sql
create user user01 password '123456';

create user user02 password '123456';

grant create on database salesdb to user01;

create schema accounts authorization user02;
 
 \dn


 \c salesdb user01
You are now connected to database "salesdb" as user "user01".
salesdb=> 
salesdb=> create schema hr;
CREATE SCHEMA
salesdb=> 
salesdb=> 
salesdb=> \dn
   List of schemas
   Name   |  Owner   
----------+----------
 accounts | user02
 hr       | user01
 public   | postgres
(3 rows)

salesdb=> 
salesdb=> \c salesdb user01
You are now connected to database "salesdb" as user "user01".
salesdb=> 
salesdb=> show search_path ;
   search_path   
-----------------
 "$user", public
(1 row)

salesdb=> set search_path to hr;
SET
salesdb=> 
salesdb=> create table empinfo(empid int, empname varchar(10));
CREATE TABLE
salesdb=> 
salesdb=> \dt
         List of relations
 Schema |  Name   | Type  | Owner  
--------+---------+-------+--------
 hr     | empinfo | table | user01
(1 row)

salesdb=> \c - postgres
You are now connected to database "salesdb" as user "postgres".
 
 alter user user01 set search_path to hr;
ALTER ROLE
 
 
 \c - app01 
You are now connected to database "salesdb" as user "app01".
salesdb=> 
salesdb=> show search_path ;
   search_path   
-----------------
 "$user", public
(1 row)

salesdb=> 
salesdb=> \c - user01
You are now connected to database "salesdb" as user "user01".
salesdb=> 
salesdb=> 
salesdb=> show search_path ;
 search_path 
-------------
 hr
(1 row)

salesdb=> 
salesdb=> 
salesdb=> create table inventory(prodid int,prodname varchar(10));
CREATE TABLE
salesdb=> 
salesdb=> \dt
          List of relations
 Schema |   Name    | Type  | Owner  
--------+-----------+-------+--------
 hr     | empinfo   | table | user01
 hr     | inventory | table | user01
(2 rows)

salesdb=> 


























```


```sql
create user edbstore password '123456';
ALTER USER edbstore CREATEDB;
\c - edbstore;

```