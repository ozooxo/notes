# Bottle Notes

A light-weighted python web framework.

## Technical

### Start

```python
from bottle import run

run(host='localhost', port=8080, debug=True)
```

No setup. `Ctrl-C` to quit.

### Syntax

#### Routing

Routing by the `@route` decorator. One function may have multiple corresponding routes. Routes may contain wildcards. Wildcards may contains filter (`int`, `float`, `path`, and `re`) and config in the form of `<name[:filter[:config]]>`.

```python
from bottle import route

@route('/hello')
@route('/hello2')
@route('/wildcardhello/<name>')
def hello():
```

If multiple routes satisfy the pattern, the most specific one will be chosen *(Need to check more carefully if it is true.)*

HTTP methods can be specified by either `@route(..., method='...')`, or simply `@get()`, `@post()`, `@put()`, `@delete()`, `@patch()` (no HEAD nor ANY).

Route static fields by `static_file(filename, root='...')` method.

Error page using `@error(error_code)` decorator.

## Comparison

|            | Django | Flask | bottle | 
| ---------- | ------ | ----- | ------ | 
| Rank       | 1      | 2     | 4      | 
| Production | Yes    |       |        |
| Small project | No  |       | Yes    | 
| Support    |        |       | routing, template |
| Not support|        |       | ORM, XSS, CSRF |

## References

1. Official [tutorial](https://bottlepy.org/docs/dev/tutorial.html)
1. Office [API reference](https://bottlepy.org/docs/dev/api.html)