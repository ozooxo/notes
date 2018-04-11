# Django Notes

## Version

Django 2.0 -> Python 3.4

## General

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

## ORM

- Database-access API
- Database generator

Setup:

Alter `INSTALLED_APPS` in `project_name/project_name/settings.py` to add the corresponding app into it.

General design rules:

- Model classes represent SQL tables. (Generally) it maps to *a single* database table.
  - Model classes are subclasses `django.db.models.Model`. 
  - By default table name `appName_className`.
- Class attributes may represent field/column of SQL tables.
  - Fields are instances of `models.Field` class. Django has a lot of predefined subclasses to decide the database column type.
  - Customized SQL column name goes as the first argument of the constructor.
  - Field properties go as optional arguments of constructors. 
    - (Similar to JPA annotations)
    - `primary_key`, ...
    - They'll be write to the database when migrating.
  - Field constrains go as optional arguments of constructors. 
    - (Similar to Java Validation API annotations)
    - `unique`, `null`, `blank`, `choice`, `default`, `max_length`
    - *(At least some like `unique` need to write as SQL constrain. Do these constrains go all the way down to SQL check conditions when doing migration? Or these constrains stay in application layer -- like Java Validation API?)*
  - Primary key is automatically generated to the `id` field. No need to define a class attribute called `id`.
    - May be customized by `primary_key = models.AutoField(primary_key=True)`. It will not be auto-incrementing. *(But auto-incrementing should be the SQL job -- SQL may choice another ways to generate unique primary key. This is only triggered when insert with a leak of primary key value. Why mentioned auto-incrementing in here?)*
  - Class attributes are the only required part of a model class.
- Class attributes may represent SQL relations.
  - Fields are instances of `models.ForeignKey`, `models.ManyToManyField`, `models.OneToOneField`.
  - Class name goes as the first argument of the constructor.

## References

1. Official [Writing your first Django app](https://docs.djangoproject.com/en/2.0/intro/tutorial01/). It is actually a quite long tutorial that tell you in depth how to use django (in its design way to make a startard web app). Most paragraphs are about how to setup the views.
1. General [Django documentation](https://docs.djangoproject.com/en/2.0/), especially the part related to [data models](https://docs.djangoproject.com/en/2.0/topics/db/models/).