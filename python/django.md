# Django Notes

## Version

Django 2.0 -> Python 3.4

## General/web application

- MVC framework
- On top of a light-weight python Web Server Gateway Interface (WSGI)-compatible web server.

Code base templates are generated automatically: 

- Projects
  - Initialization by `django-admin startproject project_name`. 
  	- Defines the outside container/directory's name, which can be renamed anytime. 
  	- Defines the inner project's name, which is used with internal `import`.
- Apps
  - Provide several default apps (admin, authorization, ...). Initialize them by `python manage.py migrate`, which added e.g. potential database tables.
  - `python manage.py startapp app_name` for initialize a customized web application.

Database deployment:

- Initialize user defined apps by `python manage.py makemigrations app_name`. Generated codes stays at `app_name/migrations/`.
  - Historical database states stay at `django_migrations` table of the database. *(So effectively if I first reset the entire database, deployment is just making new empty tables.)*  
- Show (and edit) migration SQL through `python manage.py sqlmigrate ...`. May double-check `python manage.py check` after editing.
- Actual migration done by `python manage.py migrate`, they also help migrate database content.
  - Although generating/writting migration codes is done locally in the development machine, actual migration may be done multiple times in multiple product machines.

Project structure:

```
project_name/
    manage.py
    project_name/
        __init__.py
        settings.py  <-- SQL database setting goes here
                         timezone
                         installed apps 
                          + django default apps
                          + user defined apps (path in apps.py)
        urls.py      <-- table of contents/URL dispatcher
        wsgi.py      <-- web server on top of WSGI
```

App structure (stays anywhere inside of the project folder):

```
app_name/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py  <-- django generated code goes here
    models.py        <-- build-in ORM layer and SQL generator
    tests.py
    views.py
    (urls.py)        <-- modify project urls.py accordingly
```

Run applications:

- `python manage.py runserver` for the web service. Visit the sites from `http://localhost:8000/app_name/`
- `python manage.py shell`: interactive shell to interact with the codebase (model, ...)

### Non-web application

Add Django management command by adding a python script in `app_name/management/commands/my_command.py`.

```
app_name/
    ...
    management/
        __init__.py
        commands/
            __init__.py
            _private.py   <-- not available as management command
            my_command.py <-- available for any project which 
                              include this app in its 
                              `INSTALLED_APPS` of settings.py
```

`my_command.py` should looks like:

```python
from django.core.management.base import BaseCommand
from app_name... import ...

class Command(BaseCommand):
    def handle(self, *args, **kwargs):
        print "Hello, world"
```

It can be executed by `./manage.py my_command args`.

## ORM

- Database-access API
- Database generator

### Setup

Alter `INSTALLED_APPS` in `project_name/settings.py` to add the corresponding app into it.

### ORM model definition

#### Model classes

Model classes represent SQL tables. (Generally) it maps to *a single* database table.

- Model classes are subclasses `django.db.models.Model`. 
- By default table name `appName_className`.

```python
class ModelClass(models.Model):
```

#### Model attributes

Class attributes may represent field/column of SQL tables.

- Fields are instances of `models.Field` class. Django has a lot of predefined subclasses to decide the database column type.
- Verbose name goes as the optional first argument of the constructor.
  - Verbose name is not the customized SQL column name (as it mostly have space and special characters in it).
- SQL column name is specified by the optional named argument `db_column`. By default is is the name of the class attribute.
- Field properties go as optional arguments of constructors. 
  - (Similar to JPA annotations)
  - `primary_key`, ...
  - They'll be write to the database when migrating.
- Field constrains go as optional arguments of constructors. 
  - (Similar to Java Validation API annotations)
  - `unique`, `null`, `blank`, `choice`, `default`, `max_length`
  - *(At least some like `unique` need to write as SQL constrain. Do these constrains go all the way down to SQL check conditions when doing migration? Or these constrains stay in application layer -- like Java Validation API?)*
- Primary key is automatically generated to the `id` field. No need to define a class attribute called `id`.
  - May be customized by `primary_key_class_attribute = models.AutoField(primary_key=True)`. It will not be auto-incrementing. *(But auto-incrementing should be the SQL job -- SQL may choice another ways to generate unique primary key. This is only triggered when insert with a leak of primary key value. Why mentioned auto-incrementing in here?)*
- Class attributes are the only required part of a model class.

#### Relations

Class attributes may represent SQL relations.

- Fields are instances of `models.ForeignKey` (for many-to-one relationship), `models.ManyToManyField`, `models.OneToOneField`.
- Class name in relation goes as the first argument of the constructor.
- `verbose_name` is an optional named argument to describe the relationship.
- For many-to-may relationship, the relation table can have other columns to describe the properties of the relation. In that case, we should define a intermediate model `RelationClassName`. Use the `through='RelationClassName'` optional named argument in the `models.ManyToManyField` constructor, and in `RelationClassName` class define both foreign keys.
  - This may be generated to a relation table with not exactly two foreign keys.
- For foreign key (many-to-one relationship), there is no corresponding one-to-many relationship written in the one-class. Just query by `one_instance_name.many_instance_name_set.all()`.

Also, notice that since python classes cannot be used before being defined, definition can only go one way. So for one-to-many relationship, the one-class need to go before the many-class. For many-to-many relationship, the `models.ManyToManyField` class attribute can only be set for the later class.

#### Inheritance

For inheritance mapping, compare to all what can be done in Hibernate:

- MappedSuperclass: The shared (abstract) parent classes can't be entities, and every subclass is a table.
  - Done through [abstract base classes](https://docs.djangoproject.com/en/2.0/topics/db/models/#abstract-base-classes). Set `abstract = True` through the `Meta` of the shared parent class.
- Single Table: The entities from different classes with a common ancestor are placed in a single table with several nullable columns.
  - Not supported.
- Table-Per-Class: All the properties of a class, are in its table, so no join is required.
  - Supported through [multi-table inheritance](https://docs.djangoproject.com/en/2.0/topics/db/models/#multi-table-inheritance)
  - This is the by-default case without extra code.
- Joined Table: each class has its table and querying a subclass entity requires joining the tables.
  - Seems not supported. Workaround is maybe explicitly write one-to-one relationships?

Class inheritance only used in the Django layer, are setup through [proxy model](https://docs.djangoproject.com/en/2.0/topics/db/models/#proxy-models):

```
class DjangoLayerExtendedModelClass(ModelClass):
  class Meta:
    proxy = True
```

### ORM usage

Notice that `ModelClass.objects` is the model manager.

#### `INSERT`

```python
one_model_instance = OneModelClass(class_attribute_1=..., ...)
one_model_instance.save()

many_model_instance = ManyModelClass(foreign_key_attribute=one_model_instance, ...)
manu_model_instance.save() # one_model_instance.save() will not refresh/insert the many instances

many_model_instance.many_to_many_class_attribute.add(another_many_model_instance)
```

#### `SELECT`

`SELECT * FROM model_class;` => `all_model_instances = ModelClass.objects.all()`

`SELECT * FROM model_class WHERE attribute=value;` =>  `model_instance = ModelClass.objects.get(attribute=value)`: Raise `DoesNotExist` or `MultipleObjectsReturned` in corresponding cases.

Can use `pk=` so Django automatically know which field is the primary key.

`SELECT * FROM model_class WHERE attribute=value;` => `model_instances = ModelClass.objects.filter(attribute=value)`

`filter()` and `exclude()` can be chained multiple times. `QuerySet` are only executed lazily when it is evaluated. For values in foreign keys, use `.select_related()` if want to eagerly query nested values.

For multiple constrains, just include multiple arguments in `filter()`.

`LIMIT a_number` => `[:a_number]`

`OFFSET a_number LIMIT another_number` => `[a_number:a_number+another_number]`

Unlike normal python, negative indexes are not supported.

`field__lookuptype=value` with possible lookup types include:

- `JOIN`: `foreignKeyName__foreignClassAttributeName`
- String operations:
  - `exact`
  - `iexact`
  - `contains`
  - `startswith`, `endswith`

Refer fields in the same model by F-expression `F('refereed field name')`. Also can do basic arithmetic operations in the F side. Operations includes

- `+`, `-`, `*`
- `timedelta()`
- `bitand()`, `.bitor()`, `.bitrightshift()`, and `.bitleftshift()`

Q-operations for more complicated queries, which can work together with `|` and `&`.

#### `DELECT`

`[result SELECT QuerySet].delete()`. No need to `save()` after the operation.

#### `UPDATE`

Update one instance (it can also be used to remove relationship for null-able foreign keys):

```python
model_instance = ModelClass.objects.get(...)
model_instance.class_attribute = new_value
model_instance.save()
```

Update multiple instances:

`[result SELECT QuerySet].update(attribute=value)`. No need to `save()` after the operation.

#### Interacting with the database

- Save to database by `model_instance.save()`.

### Manager

Manager is the interface to save common user queries. One model should have at least one manager. Manager is similar to data access object (DAO) and/or repository in Java/Hibernate/Spring data.

By default, `model_instance.objects` return a manager. To overwrite it by `another_name = models.Manager()` in the model definition.

Manager can:

- Add extra query methods.
- Customize/overwrite existing methods (then you don't want to overwrite the existing manage, but to let the model to have multiple managers).

```python
class ExtendedModelManager(models.Manager):
  def customized_new_query(self, ...):
    ...

class OverwrittenModelManager(models.Manager):
  def get_queryset(self): # get_queryset() is defined in models.Manager
    ... 

class ModelClass(models.Model):
  objects = ExtendedModelManager()
  customized_manager = OverwrittenModelManager()
```

If overwrite `get_queryset()`, then don't rewrite default manager (`Meta.default_manager_name`) or it may results in an inability to retrieve objects.

In `customized_manager` (the one to overwrite `get_queryset()`), one may consider using a customized/extended `models.QuerySet`.

## References

1. Official [Writing your first Django app](https://docs.djangoproject.com/en/2.0/intro/tutorial01/). It is actually a quite long tutorial that tell you in depth how to use django (in its design way to make a startard web app). Most paragraphs are about how to setup the views.
1. General [Django documentation](https://docs.djangoproject.com/en/2.0/), especially the part related to [data models](https://docs.djangoproject.com/en/2.0/topics/db/models/).
1. [Writing custom django-admin commands](https://docs.djangoproject.com/en/2.0/howto/custom-management-commands/)