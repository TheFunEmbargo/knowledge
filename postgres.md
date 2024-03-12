# Postgres

## Tags

SQL, Postgres


## I want to

### _...connect to a databse_

`psql -h localhost -p 5432 -d database_name -U user_name`

### _...restore a backup_

`psql -U postgres -d database_name  | gzip > /home/path/to/backup.sql`

`pscp -i <path_to_pem_file> <username>@<hostname>:<remote_file_path> <local_file_path>`

NOTE windows / linux line endings are important. You will have to convert between / use the same OS (maybe via docker?)

