# Unit Test Notes

Aim to only test my code, rather than the "dirty services" associated with it.

## Mock and Patch

- Mock: replace part of the system under test with mock .
	- `patch()` decorator: mock classes/objects under test.
- Host
- Stubs

Mock benefits:

- Further isolate unit tests
- Increase speed
- Avoid undesired side effective (especially functions interact with the outside)

### Usage

#### Mock

Mock class instances can either be initialized by (1) initialize a new class:

```python
mock_instance_1 = Mock()
mock_instance_1.side_effect = ...
```

(2) through `MagicMock`:

```python
...
```

or (3) through `@patch` -- 

```python
@patch('ClassName')
def test(self, MockClassName):
	mock_instance_name = MockClassName()
```

#### Patch

May setup fixed return value:

```python
@patch('method_name', return_value=...)
def test(self, mock_method_name):
```

or

```python
@patch('method_name')
def test(self, mock_method_name):
    mock_method_name.return_value = ...
```

May setup mock methods to be called through `side_effect`:

```python
def mock_method_side_effect(...):
	...

@patch('method_name', side_effect=mock_method_side_effect)
def test(self, mock_method_name):
```

For mocked classes, return values of its associated methods is defined inside:

```python
@patch('ClassName')
def test(self, MockClassName):
	mock_instance_name = MockClassName()
	mock_instance_name.method_name.return_value = ...
```

To mock an instance method, one can either mock the instance method itself

```python
@patch.object(ClassName, 'instance_method_name', autospec=True)
def test(self, mock_instance_method_name):
    ...
```

or create mock instance in code

```python
def test(self):
    mock_instance = mock.create_autospec(ClassName)
    ...
```

**Mock an item where it is *used*, not where it came from:** in case the patched method or class is (in the to-be-tested code) import from somewhere else, patch path should be `to_be_tested_module.ClassName` rather than `original_module.ClassName`. The reason is python modules are imported into their own scope, so if we mock the original code path, we won't see the effect in the mocked module.

Path goes bottom up for the arguments to go left to right.

```python
@patch('ClassName2')
@patch('ClassName1')
def test(self, MockClassName1, MockClassName2):
```

Further check of calling status:

```python
mock_instance_name.method_name.called => True/False

mock_instance_name.method_name.assert_called_with()
mock_instance_name.method_name.assert_not_called()
mock_instance_name.method_name.assert_called_once_with()
mock_instance_name.reset_mock()
```

## References

1. [Official python manual for `unittest.mock`](https://docs.python.org/3/library/unittest.mock.html)
1. [Getting Started with Mocking in Python](https://semaphoreci.com/community/tutorials/getting-started-with-mocking-in-python)
1. [An Introduction to Mocking in Python](https://www.toptal.com/python/an-introduction-to-mocking-in-python)