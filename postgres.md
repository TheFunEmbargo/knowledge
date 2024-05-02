# Postgres

## Tags

SQL, Postgres


## I want to

### _...connect to a databse_

`psql -h localhost -p 5432 -d database_name -U user_name`

### _...create a backup_

`psql -U postgres -d database_name  | gzip > /home/path/to/backup.sql`

### _...restore a backup_

`psql -d database_name -f /home/path/to/backup.sql -U postgres`


NOTE windows / linux line endings are important. You will have to convert between / use the same OS (maybe via docker?)

