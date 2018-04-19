# SQLAlchemy Notes

SQLAlchemy is a python ORM framework.

Compare to Django which does ORM but also web (controller, viewer) parts, SQLAlchemy is just a ORM framework. Also, (compare to Django which helps you to generate all SQL code, doing SQL migration, and hide all SQL from the user) SQLAlchemy seems working better if you have your own SQL schema.

## ORM model definition

### Model class

All models extend `Base = declarative_base()`. User need to define the `__tablename__` attribute of that class.

### Model attribute

Class variable is used to represent field/column of SQL table. Instance variables are used to represent row values. Base class is in charge of mapping class variable to instance variable which actually persistent the data.

Base class also makes a constructor that all the instance variables can be setup through python keyword arguments.

- Fields are instance of `Column` class.
- Field type is the first argument of the `Column` constructor.
- Optional descriptions:
    - Sequence name
    - Primary key or not

### Migration

SQLAlchemy can help writing DDL (creating SQL tables, ...) through `Base.metadata.create_all(engine)`.

## Data manipulation

```
Session = sessionmaker(bind=engine)
session = Session()
```

`SELECT` (return a sequential data type which can be iterated)

```
session.query(ClassName).order_by(class_instance_name)
```

`INSERT`/`UPDATE`

```
session.add()
session.add_all()

session.commit()
session.rollback()
```

## References

1. [Official Tutorial](http://docs.sqlalchemy.org/en/latest/orm/tutorial.html).
1. [auth0 Tutorial](https://auth0.com/blog/sqlalchemy-orm-tutorial-for-python-developers/)