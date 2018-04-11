# Meta Programming Notes

## Python fact

Class (static) variables and methods can be modified after the class is defined. The modification affects all class instances, include the ones instantiated before.

Class is a `type` instance to forbidden creating a new instance if it already exists.

```python
class SingletonMeta(type):
    instance = None
    def __call__(cls, *args, **kw):
        if not cls.instance:
             cls.instance = super(Singleton, cls).__call__(*args, **kw)
        return cls.instance
```


```python
ClassName = type(
		'ClassName', 
		(SuperClass, ...), 
		{variable_name : variable_value, method_name : method(), ...}
	)
```

Meta classes extend `type`, so can hook customized operations into the creation of class objects.

`type` and/or meta classes create/construct classes, classes create instances. 

### Scope

For `type` and `exec` calls inside of a method, they need to be inserted into the global namespace to be seen:

```python
global()['ClassName'] = type('ClassName', ...)
exec 'python code string' in globals()
```

## Meta class setup

(The below ones are Python 2 setups)

Separated definition:

```python
class MetaClassName(type):

	def __init__(cls, name, bases, nmspc):
        super(MetaClassName, cls).__init__(name, bases, nmspc)
        cls.method_name = ...
        ...


class ClassName():

	__metaclass__ = MetaClassName
	...
```

Inline definition:

```python
class ClassName():

	class __metaclass__(type):
		def __init__(cls, name, bases, nmspc):
	        type.__init__(name, bases, nmspc)
	        cls.method_name = ...
	        ...

    ...
```

Or defined as a function:

```python
class ClassName():

	def __metaclass__(cls, name, bases, nmspc):
	    cls = type(name, bases, nmspc)
	    cls.method_name = ...
	    ...
	    return cls

    ...
```

Notes:

- Meta class method uses `cls` (`__new__()` uses `mcl`) as the first argument.

Sometimes, class decorators may do similar jobs.

## Benefits and comparison

### Benefits

- Meta class may have a variable to save the information of all the subclasses.
	- The variable is defined as `cls.variable_name` which is per its instance (for each class with this as the meta class).
- Define **meta methods** for which the class itself is the argument.
	- Notice that meta methods can be called from meta classes and classes, but not from instances.
	- Examples:
		- `__str__()` in meta class defines `print(ClassName)`.
		- `__iter__()` in meta class defines `for c in ClassName`.

### Implementations

#### Final class

Forbidden class inheritance: In meta class `__init__()` check if the superclass has a meta class of itself, then raises error (as subclass inheritance meta class).

```python
class FinalMeta(type):
    def __init__(cls, name, bases, namespace):
        super(final, cls).__init__(name, bases, namespace)
        for klass in bases:
            if isinstance(klass, FinalMeta):
                raise TypeError(str(klass.__name__) + " is final")
```

Can't be done by class decorator, because action happens when class is created.

#### Singleton

Override `__call__()` in meta class to forbidden creating a new instance if it already exists.

```python
class SingletonMeta(type):
    instance = None
    def __call__(cls, *args, **kw):
        if not cls.instance:
             cls.instance = super(SingletonMeta, cls).__call__(*args, **kw)
        return cls.instance
```

### w/ class decorator

## Reference

1. [Metaprogramming](http://python-3-patterns-idioms-test.readthedocs.io/en/latest/Metaprogramming.html)