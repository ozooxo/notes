# Django Notes

- MVC framework
- On top of a light-weight python Web Server Gateway Interface (WSGI)-compatible web server.

Code base templates are generated automatically: 

- `django-admin startproject project_name` for project. 
	- Defines the outside container/directory's name, which can be renamed anytime. 
	- Defines the inner project's name, which is used with internal `import`.
- Provide several default apps (admin, authorization, ...). Initialize them by `python manage.py migrate`, which added e.g. potential database tables.
- Initialize user defined apps by `python manage.py makemigrations app_name`. Generated codes stays at `app_name/migrations/`. Actual migration done by `python manage.py sqlmigrate ...`; they help migrate database content.
- `python manage.py startapp app_name` for a web application.

Run by: `python manage.py runserver`

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

## Version

Django 2.0 -> Python 3.4