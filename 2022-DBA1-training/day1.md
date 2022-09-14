## Installation

### install script
```bash
yum install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
yum install -y postgresql14-server

mkdir -p /db/pgsql
# set postgres home dir to /db/pgsql
usermod -d /db/pgsql postgres
cp /root/.bash_profile /db/pgsql/
echo "export PATH=/usr/pgsql-14/bin:$PATH" >> /db/pgsql/.bash_profile
chown -R postgres:postgres /usr/pgsql-14/
chown -R postgres:postgres /db/pgsql/
```

### check installation
```console
foo@bar:~$ su - postgres
Last login: Tue Sep 13 14:36:15 IST 2022 on pts/1
foo@bar:~$ pwd
/db/pgsql
foo@bar:~$ psql --version
psql (PostgreSQL) 14.5
foo@bar:~$ initdb --version
initdb (PostgreSQL) 14.5
```

init the database files and uncomment default port 5432 in the config
```console
foo@bar:~$ cd /db/pgsql
foo@bar:~$ mkdir data
foo@bar:~$ initdb -D data
foo@bar:~$ cd data
foo@bar:~$ sed '/\port\ =\ 5432/s/^#//' -i postgresql.conf

pg_ctl -D /db/pgsql/data/  start

psql -p 5432  -U postgres -d postgres
```

SQL console to check instance details
```console

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

```
## Creating and Managing Databases

Create a database in SQL
```sql

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

```


From the shell using createdb
```bash
createdb -p 5432 -D postgres salesdb
psql -p 5432 -U postgres -d salesdb
```



#### Locate postgres object locations on the file system
```console

salesdb=# select datname from pg_database;
  datname  
-----------
 postgres
 edbstore
 template1
 template0
 salesdb
(5 rows)

salesdb=# show data_directory;
 data_directory 
----------------
 /db/pgsql/data
(1 row)


salesdb=# select oid,datname from pg_database;
  oid  |  datname  
-------+-----------
 14486 | postgres
 16384 | edbstore
     1 | template1
 14485 | template0
 16385 | salesdb
(5 rows)


```
```console
foo@bar:~$ ls /db/pgsql/data/base/
1  14485  14486  16384  16385

```

#### locate table data fiel on file sys6tem
```console

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

```

note the table data is stored in /db/pgsql/data/base/14486/16386 and the block size is 8k

```console

-bash-4.2$ cd /db/pgsql/data/base/14486/
du -sh 16386
0    16386
-bash-4.2$ psql -p 5432 -U postgres -d postgres -c "insert into sample values(1,'postgres');"
INSERT 0 1
-bash-4.2$ du -sh 16386
8.0K    16386
-bash-4.2$ psql -p 5432 -U postgres -d postgres -c "insert into sample values(generate_Series(2,200),'postgres');"
INSERT 0 199
-bash-4.2$ du -sh 16386
16K    16386

```

## LAB: create new instance in /opt/edbstore

1. CREATE edbstore DIRECTORY under /opt

2. chown to postgres to edbstore

3. create cluster using edbstore directory

4. change the edbstore cluster port to 5433 (postgresql.conf => "port=5432" remove #)

5. start the edbstore cluster

6. connect to edbstore cluster

7. create database edbstore;

8. connect to edbstore as postgres user

9. create table employee(id int) under edbstore 

10. find the physical location of edbstore database and employee table

```bash
# as super user
mkdir -p /opt/edbstore/data
chown -R postgres:postgres /opt/edbstore
cd /opt/edbstore
initdb -D data
cd data
sed '/\port\ =\ 5432/s/^#//' -i postgresql.conf
pg_ctl -D /opt/edbstore/data -l /opt/edbstore/logfile start
psql -p 5432 -U postgres
```

```sql
create database edbstore;
\c edbstore
create table employee(id int);
```

Based on this, the table filesystem path should be
/opt/edbstore/data/base/16387/16388
```console
postgres=# \c edbstore
You are now connected to database "edbstore" as user "postgres".
edbstore=# select oid,datname from pg_database;
  oid  |  datname  
-------+-----------
 13806 | postgres
     1 | template1
 13805 | template0
 16392 | salesdb
 16387 | edbstore
 16412 | fdb
(6 rows)

edbstore=# select relfilenode,relname from pg_class where relname='employee';
 relfilenode | relname  
-------------+----------
       16388 | employee
(1 row)


```

## Case Study on SCHEMA


Lab Exercise - 1

1. A new website is to be developed for an online music store.
− Create a database user edbstore in your existing cluster
− Create an edbstore database with ownership of edbstore user
− Login to the edbstore database using the edbstore user and create the edbstore schema

Lab Exercise - 2

1. create database fdb
2. create three users f01,f02,f03
3. connect to fdb database with f01 user and create below objects

SCHEMAS: f01_hr, f01_accounts

TABLES :connect to f01_hr schema and create emp_hr(id int) table;

TABLES :connect to f01_hr schema and create emp_admin(id int) table;

TABLES : connect to f01_accounts schema and create emp_accounts(id int) table;

4. GRANT PERMISSION TO f02 user to access only emp_hr table

5. GRANT PERMISSION TO f03 user to access all tables in  f01_hr & f01_accounts 


```sql
\c - postgres;
create database fdb;
create user f01 password '123456';
GRANT CREATE ON DATABASE fdb TO f01;
create user f02 password '123456';
create user f03 password '123456';
ALTER DATABASE fdb OWNER TO f01;

\c fdb f01;

create schema f01_hr;
create schema f01_accounts;
SET search_path TO f01_hr;
create table emp_hr(id int);
create table emp_admin(id int);
SET search_path TO f01_accounts;
create table emp_accounts(id int);

GRANT SELECT, INSERT, UPDATE, DELETE, USAGE
ON f01_hr.emp_hr 
TO f02;

GRANT SELECT, INSERT, UPDATE, DELETE, USAGE
ON ALL TABLES IN SCHEMA f01_hr 
TO f03;

GRANT SELECT, INSERT, UPDATE, DELETE, USAGE
ON ALL TABLES IN SCHEMA f01_accounts 
TO f03;

```


Check results
```sql
SET search_path TO f01_hr;
\dt;

SELECT grantee, privilege_type 
FROM information_schema.role_table_grants 
WHERE table_name='emp_hr';

SELECT grantee, privilege_type 
FROM information_schema.role_table_grants 
WHERE table_name='emp_admin';

SET search_path TO f01_accounts;
\dt;

SELECT grantee, privilege_type 
FROM information_schema.role_table_grants 
WHERE table_name='emp_accounts';

```


## Snippets

### Show/List/Print
```sql
-- list databases
\l
-- list tables
\dt
```

### check database OID
```sql
select oid,datname from pg_database;
```

###  Show the owner of database 'edbstore'
```sql
SELECT d.datname as "Name",
pg_catalog.pg_get_userbyid(d.datdba) as "Owner"
FROM pg_catalog.pg_database d
WHERE d.datname = 'edbstore'
ORDER BY 1;
```


### Change owner of edbstore database
```sql
\c - postgres
create user edbstore password '123456';
ALTER DATABASE edbstore OWNER TO edbstore;
\c edbstore edbstore
create schema edbstore;
```

```sql
SET search_path TO f01_accounts;
SELECT grantee, privilege_type 
FROM information_schema.role_table_grants 
WHERE table_name='emp_hr';

```


### show users
```sql
\du
```