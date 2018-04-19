# Python Fundamentals

## `__new__` vs `__init__`

|       | `__new__` | `__init__` |
| ----- | --------- | ---------- |
| Definition | handles object creation | handles object initialization |
| High level difference | More flexibility: can do everything, especially pre/post operations | More limitation |
| Called when | Create a new instance | After `__new__` but before `__call__` |
| Argument | `cls [, ...]` | `self [, ...]` |
| Return value | typically `super(ThisClass, cls).__new__(cls)`, but can be customized | None |

## Method vs `@classmethod` vs `@staticmethod`

|       | Method | `@classmethod` | @staticmethod |
| ----- | ------ | -------------- | ------------- |
| Argument | `self` | `cls` | None |
| Call from outside | `instance_name.method_name()` | `ClassName.method_name()`<br>`instance_name.method_name()` | `ClassName.method_name()`<br>`instance_name.method_name()` |
| Call in method | `self.method_name()` | `ClassName.method_name()` | `ClassName.method_name()` |
| Call in `@classmethod` | N/A | `cls.method_name()` (if being called by a subclass, `cls` is the subclass) | `ClassName.method_name()` |
| Call in `@staticmethod` | N/A | N/A | `ClassName.method_name()` |
| Usage | | Special in python. Useful for creating alternate class constructors. | Although similar to static method in C++/Java, it is not quite useful in Python, because you can simply define functions out of a class scope. |

## Instance variable vs class variable

|       | Instance variable | class variable ~ class attribute |
| ----- | ----------------- | -------------- |
| Defined | Inside of methods with prefix `self`. Note that they may or may not be defined in `__init__`. | Defined in the class scope without prefix. |
| Used as default value of method argument | N/A | without prefix |
| Used in class method | With prefix `self.` | With prefix `self.` |
| Used in `@classmethod` | N/A | with prefix `cls.` |
| Used in `@staticmethod` | N/A | with prefix `ClassName.` |
| Usage | | Constants: capitalized<br>Default attribute value<br>Variable shared by all instances. |