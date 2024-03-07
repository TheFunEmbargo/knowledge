# SQLAlchemy


SQLAlchemy is a useful ORM (Object Relational Model) for Python with useless documentation. As the name suggests this tool enables you to create Python objects to model relational databases.

## Tags

Python, SQLAlchemy, SQL, Database



## Defining models

Declarative mapping is intuitive.

```python
from sqlalchemy import Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()

class SomeTable(Base):
    __tablename__ = 'some_table'
    id = Column(Integer, primary_key=True)
    name =  Column(String(50))
    
    # business logic here
```


Imperative mapping allows better separation of the database model and business logic.


```Python
from sqlalchemy import Table, Column, Integer, String, ForeignKey
from sqlalchemy.orm import registry

mapper_registry = registry()

some_table = Table(
    "some_table",
    mapper_registry.metadata,
    Column("id", Integer, primary_key=True),
    Column("name", String(50)),
)

class SomeTable:
    # business logic here
    pass

mapper_registry.map_imperatively(SomeTable, some_table)
```

## Defining relationships 

...write some stuff...


## Writing queries with the ORM

Fetch the session object from an instance of a class 

```python
from sqlalchemy.orm.session import Session   

class SomeTable:

   # model definition if declarative

   def some_method(self):
       session = Session.object_session(self)
```


Stack these session object methods to construct queries 

pass in the entity you wish to return `.query()`

pass in the table you wish to select from `.select_from()`

corresponds to an outer join (either left or right, depending on the order of the tables) `.outerjoin()`   

corresponds to an inner join `.join()`

equivalent to SQL `.where()`       





