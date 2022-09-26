Data discovery - what data to protyect in an instnace (pg_dump -v)
 - 
Data checkpointing -  how to roll back/recover data for migration testing 
 - test  by firing indexed data in that has timestamps
How to manage test data for migration and upgrade testing
How to profile query activity then simulate it. at 10x? at 100x (pgaudit)
How to monitor performance
can/should pgbouncer be used in rds?
routine maintenance and monitoring thereof
Can we use pgbackrest for the upgrades using incremental/differential backups
https://pgbackrest.org/
Can we use pgdump to analyuze the data that needs to be protected?
How to minimize mdown time for major engine upgrades
How to inventory DDL for manual migration using pglogical
drop/recreate index

Is it possible to automatically profile the DB  activities (queries) of an application, then run that activity 10x and 100x against a test db

How to compare data in 1 instance vs another? assuming we have to replicate with a combination of pg_logical and pg_dump?


How to inventory an instnace for assests to archive, backup, replicate?
S3 transaction log export:
https://aws.amazon.com/blogs/database/automate-postgresql-log-exports-to-amazon-s3-using-extensions/
can table partitioning be configured in RDS?
can tablespaces be confgured on RDS?

map on prem AAA (postgresql.confg and pg_hba.conf) to RDS AAA:
 - log attempts
 - SG instead of RDS security config?
validate replication/migration?

measure performance?

care adn feeding -  indices, etc

postgres llogging in aws?

recovery/availability:
 - streaming replication
 - replication slots
 - hot backup: pg_basebackup
 - PITR - force rollbacl for everythign? or just a single table




 read replicas?
  ow to monitor to see if read replicas would help

  module10 -  routine maintenance


