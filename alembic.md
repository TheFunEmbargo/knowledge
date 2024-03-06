# Alembic
[Alembic](https://alembic.sqlalchemy.org/en/latest/) is a Python tool for SQLAlchemy database migrations and versioning. Git for your DB.


## Tags
Python, SQLAlchemy, SQL, Database



## Notes

Initialising [creates an alembic folder](https://alembic.sqlalchemy.org/en/latest/tutorial.html#creating-an-environment) storing revisions. In addition an _alembic_version_ table is added to your database storing the current revision hex code in _verion_num_ column.

`$ alembic init`

  

Add an [Alembic revision](https://alembic.sqlalchemy.org/en/latest/tutorial.html#create-a-migration-script)

`$ alembic revision -m "create account table"`

  

The revision will appear in the alembic/versions folder. There will be two functions to flesh out. Write both your upgrade (the changes) and downgrade (undoing the changes).  
  
[Docs for DDL (Data Definition Language - DB migration related SQL) commands](https://alembic.sqlalchemy.org/en/latest/api/ddl.html) 

The docstrings are very good so read them. Example for [add\_column](https://github.com/sqlalchemy/alembic/blob/1b0e4bcd99c83bcce89ec6dae92c8deaafc7f8b5/alembic/op.pyi#L46-L125):  
```
from alembic import op
from sqlalchemy import Column, String

 
op.add_column("organization", Column("name", String()))
```
To update the database run `$ alembic upgrade head`

To downgrade run `$ alembic downgrade -1`

Best practice is to run up down up to ensure a traversable revision tree.
