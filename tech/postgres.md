# Postgres

## Tags

SQL, Postgres


## I want to

Note: preface these commands with `docker run -it --rm postgres:latest ` to run psql from anywhere

### _...connect to a databse_

`psql -h localhost -p 5432 -d database_name -U user_name`

### _...create a backup_

`psql -U postgres -d database_name  | gzip > /home/path/to/backup.sql`

### _...restore a backup_

`psql -d database_name -f /home/path/to/backup.sql -U postgres`

NOTE windows / linux line endings are important. You will have to convert between / use the same OS (maybe via docker?)

Use [pgloader](https://pgloader.readthedocs.io/en/latest/index.html) for more complex data migrations

define migration instruction set file

```migration_instructions.load
LOAD DATABASE
    FROM sqlite:///home/user/path/to/database.db
    INTO postgresql://USERNAME:PASSWORD@HOST:PORT/DATABASE?sslmode=allow

 WITH data only;
```

execute the migration

```
docker run -it --rm \
  -v "/var/app/prod/migration_instructions.load:/data/db.load" \
  -v "/var/app/prod/ibex_staging.db:/data/sqlite.db" \
  dimitri/pgloader:latest \
  pgloader /data/db.load
``` 



### _...kill a lock_

Tread lightly!

Determine which lock is blocking

```
SELECT 
    blocked_locks.pid AS blocked_pid,
    blocked_activity.query AS blocked_query,
    blocking_locks.pid AS blocking_pid,
    blocking_activity.query AS blocking_query
FROM pg_catalog.pg_locks blocked_locks
JOIN pg_catalog.pg_stat_activity blocked_activity ON blocked_activity.pid = blocked_locks.pid
JOIN pg_catalog.pg_locks blocking_locks ON blocking_locks.locktype = blocked_locks.locktype
    AND blocking_locks.DATABASE IS NOT DISTINCT FROM blocked_locks.DATABASE
    AND blocking_locks.relation IS NOT DISTINCT FROM blocked_locks.relation
    AND blocking_locks.page IS NOT DISTINCT FROM blocked_locks.page
    AND blocking_locks.tuple IS NOT DISTINCT FROM blocked_locks.tuple
    AND blocking_locks.virtualxid IS NOT DISTINCT FROM blocked_locks.virtualxid
    AND blocking_locks.transactionid IS NOT DISTINCT FROM blocked_locks.transactionid
    AND blocking_locks.classid IS NOT DISTINCT FROM blocked_locks.classid
    AND blocking_locks.objid IS NOT DISTINCT FROM blocked_locks.objid
    AND blocking_locks.objsubid IS NOT DISTINCT FROM blocked_locks.objsubid
JOIN pg_catalog.pg_stat_activity blocking_activity ON blocking_activity.pid = blocking_locks.pid
WHERE NOT blocked_locks.granted;
```

kill [`SELECT pg_terminate_backend(pid:integer)`](https://www.postgresql.org/docs/current/functions-admin.html#FUNCTIONS-ADMIN-SIGNAL)
