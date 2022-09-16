# Maintain AWS RDS Postgres instnace
https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html

# Check for 5GB tables and partition them

## Manage  Autovacuum

[Auto vacuum management in RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.PostgreSQL.CommonDBATasks.Autovacuum.html)

The settings seem to be in the parameter group
https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.PostgreSQL.CommonDBATasks.Parameters.html

The work_mem needs to be monitored and managed for everything autovacuum, sorting, etc:
https://aws.amazon.com/blogs/database/tune-sorting-operations-in-postgresql-with-work_mem/


[Optimizing queries](https://aws.amazon.com/blogs/database/optimizing-and-tuning-queries-in-amazon-rds-postgresql-based-on-native-and-external-tools/)


[Common Tasks including pgBadger](https://docs.amazonaws.cn/en_us/AmazonRDS/latest/UserGuide/Appendix.PostgreSQL.CommonDBATasks.html)