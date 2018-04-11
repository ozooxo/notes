# Unit Test Notes

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
def test(self, method_name):
```

May setup mock methods to be called through `side_effect`:

```python
def mock_method_name(...):
	...

@patch('method_name', side_effect=mock_method_name)
def test(self, method_name):
```

For mocked classes, return values of its associated methods is defined inside:

```python
@patch('ClassName')
def test(self, MockClassName):
	mock_instance_name = MockClassName()
	mock_instance_name.method_name.return_value = ...
```

Path goes bottom up for the arguments to go left to right.

```python
@patch('ClassName2')
@patch('ClassName1')
def test(self, MockClassName1, MockClassName2):
```

Further check of calling status:

```python
mock_instance_name.method_name.assert_called_with()
mock_instance_name.method_name.assert_not_called()
mock_instance_name.method_name.assert_called_once_with()
mock_instance_name.reset_mock()
```

## References

1. [Official python manual for `unittest.mock`](https://docs.python.org/3/library/unittest.mock.html)
1. [Getting Started with Mocking in Python](https://semaphoreci.com/community/tutorials/getting-started-with-mocking-in-python)