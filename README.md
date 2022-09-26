# Postgres Training

This project is about learning how to take ownership of an RDS/Aurora postgres database.


## Partner with engineering
- it's not possible to reverse engineer data protection. Engineering has to tell us when the assets change
- Continuously explain what we can and can't do for them
- make sure they understand limitations (DDL inventory, large objects,
  credential rotation, minor upgrade resilience) 
- rely on engineering for guidance about what to protect and to keep us informed when that changes
- (re)organize instances into high nines and low nines instances to right size major upgrade effort
## Discovery
- find what we can find
- partenr wtih enginering
- bring in an SME
- accept that we may miss something

We don't know what is in the databases, so we can't protect the data.
- rely upon engineering
- DDL
- stored procedures
- users, roles, grants
- Create engineering engagement checklist
- Keep engineering teasm educated on DB requirements/limitations (DDL inventory, large objects)
## Booststrapping

## Upgrades
- pglogical replication
- data inventory
- instance compare
- pg_dump compare
- DDL is not included in pglogical
## Disaster Recovery

- Continuous Backup/PITR
- Snapshots
- Compare instances
- Compare instance to dump


## Data management for Automation testing
Data quality tests to make sure applications and maintenance automation works correctly

- Continuous Backup/PITR
- Snapshots
- Compare instances
- Compare instance to dump
- generate test data
- rollback test data for each execution
- compare pre/post data


## monitoring

## Profile and synthesize application activity

- pgaudit

## Troubleshooting

- performance measurement
- perforamnce comparison
- explain
- can table partitionin

## Routing maintenance
- minor upgrades
- vacuum
- partition tables at 5GB limit (how does this affect indices)
