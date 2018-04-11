# Python Fundamentals

## `__new__` vs `__init__`

|       | `__new__` | `__init__` |
| ----- | --------- | ---------- |
| Definition | handles object creation | handles object initialization |
| High level difference | More flexibility: can do everything, especially pre/post operations | More limitation |
| Called when | Create a new instance | After `__new__` but before `__call__` |
| Argument | `cls [, ...]` | `self [, ...]` |
| Return value | typically `super(ThisClass, cls).__new__(cls)`, but can be customized | None |

## `self` vs `cls`

|       | `self` | `cls` | N/A |
| ----- | ------ | ----- | --- |
| As method argument | For instance methods | For `@classmethod` | `@staticmethod` don't need to implicitly pass any of them |

## Instance variable vs class variable

|       | Instance variable | class variable |
| ----- | ----------------- | -------------- |
| Defined | Inside of methods with prefix `self`. Note that they may or may not be defined in `__init__`. | Defined in the class scope without prefix. |
| Used as default value of method argument | N/A | without prefix |
| Used in class method | With prefix `self.` | With prefix `self.` |
| Used in `@classmethod` | N/A | with prefix `cls.` |
| Used in `@staticmethod` | N/A | with prefix `ClassName.` |
| Usage | | Constants: capitalized<br>Default attribute value<br>Variable shared by all instances. |