# SQLAlchemy Notes

SQLAlchemy is a python ORM framework.

Compare to Django which does ORM but also web (controller, viewer) parts, SQLAlchemy is just a ORM framework. Also, (compare to Django which helps you to generate all SQL code, doing SQL migration, and hide all SQL from the user) SQLAlchemy seems working better if you have your own SQL schema.

## ORM model definition

### Model class

All models extend `Base = declarative_base()`. User need to define the `__tablename__` attribute of that class.

### Model attribute

Class variable/attribute is used to represent field/column of SQL table. Instance variables are used to represent row values. Base class is in charge of mapping class variable to instance variable which actually persistent the data.

Base class also makes a constructor that all the instance variables (with the same name of the class variable for field names) can be setup through python keyword arguments. For instance variable name (=SQL field name) *not* the same as class variable name, setup `ModelClass.model_attribute.label("customized_model_attribute")` in `query()`, and then queries by `model_instance.customized_model_attribute`.

- Fields are instance of `Column` class.
- Field type is the first argument of the `Column` constructor.
- Optional descriptions:
    - Sequence name
    - Primary key or not

### Relationship

#### Many-to-one

```
from sqlalchemy import ForeignKey
from sqlalchemy.orm import relationship

class ManyModelClass(Base):
    ...

    one_model_attribute_id = Column(Integer, ForeignKey('one_model_attribute.id'))
    one_model_attribute = relationship("OneModelClass", back_populates="list_of_many_model_attributes")

class OneModelClass(Base):
    ...

    list_of_many_model_attributes = relationship("ManyModelClass", backref="one_model_attribute")
```

May define `primaryjoin` as a keyword argument in `relationship()` if one want to a specify *alternative* join condition.

`relationship` options:

+ `cascade`

#### Many-to-many

The relationship table is not a concrete class.

```
many_to_many_relation = Table('many_to_many_relation', Base.metadata,
    Column('class1_id', ForeignKey('list_of_class1.id'), primary_key=True),
    Column('class2_id', ForeignKey('list_of_class2.id'), primary_key=True)
)

class Class1(Base):
    ...

    list_of_class2 = relationship('Class2',
                             secondary=post_keywords,
                             back_populates='list_of_class1')

class Class2(Base):
    ...

    list_of_class1 = relationship('Class1',
                             secondary=post_keywords,
                             back_populates='list_of_class2')
```

### Migration

SQLAlchemy can help writing DDL (creating SQL tables, ...) through `Base.metadata.create_all(engine)`.

## Data manipulation

```
Session = sessionmaker(bind=engine)
session = Session()
```

### `SELECT` 

Return a sequential data type which can be iterated by for loop.

```
session.query(ModelClass).from_statement(text(SELECT * FROM ...)).params(...)
session.query(ModelClass).from_statement(text(SELECT ... FROM ...).columns(...)).params(...)

session.query(ModelClass).order_by(model_attribute)         # `SELECT * FROM`
                                                            # return model instance

session.query(ModelClass.model_attribute, ...)              # `SELECT model_atrtribute, ... FROM`
                                                            # return tuples

session.query(ModelClass)[start:end]                        # `OFFSET ... LIMIT ...`

session.query(ModelClass).filter_by(model_attribute = ...)  # `WHERE`
                                                            # more than one filter means `AND`

session.query(ModelClass).count()
```

Other filter options includes: 

+ `text(...write SQL in it...)`
    + Support parameters by `filter(text("id<:value and name=:name")).params(value=224, name='fred')`
+ `!=`
+ `like()`: `LIKE`
+ `ilike()`: case-sensitive `LIKE` for which most RMDBs don't support directly
+ `in_()`
+ `~ModelClass.model_attribute.in_()` by using `~` for not.
+ `and_()`
+ `or_()`
+ `match()`: `MATCH`

```
session.query(...).all()          # return list
session.query(...).first()        # same as [0:1] ???
session.query(...).one()          # raise exception MultipleResultsFound/NoResultFound
                                  # if not the case that exactly one result satisfied
session.query(...).one_or_none()
session.query(...).scalar()       # ???
```

#### `JOIN`

All the ones below are (semi-)equivalent.

```
session.query(ModelClass1, ModelClass2).filter(...==...)
session.query(OneModelClass).join(ManyModelClass)
session.query(OneModelClass).join(ManyModelClass, ...==...)
session.query(OneModelClass).join(OneModelClass.list_of_many_model_attribute)
```

`OUTER JOIN`:

```
session.query(...).outerjoin(...)
```

`LEFT OUTER JOIN`: `joinedload()`

Eager/lazy: `contains_eager()`

#### Sub queries

```
subquery()
```

### `INSERT`/`UPDATE`

```
session.add()
session.add_all()

session.commit()
session.rollback()
```

### `DELETE`

```
session.delete(model_instance)
```

## References

1. [Official Tutorial](http://docs.sqlalchemy.org/en/latest/orm/tutorial.html).
1. [auth0 Tutorial](https://auth0.com/blog/sqlalchemy-orm-tutorial-for-python-developers/)