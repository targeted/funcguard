# Function guard for Python 3

## Rationale and thoughts:

See http://requires-thinking.blogspot.com/2016/05/python-function-guards.html

## Samples:

```python
from funcguard import guard
```

```python
@guard
def abs(a, _when = "a >= 0"):
    return a

@guard
def abs(a, _when = "a < 0"):
    return -a

assert abs(1) == abs(-1) == 1
```

```python
@guard
def factorial(n): # no _when expression => default
   return 1

@guard
def factorial(n, _when = "n > 1"):
   return n * factorial(n - 1)

assert factorial(10) == 3628800
```

```python
class TypeTeller:
    @staticmethod
    @guard
    def typeof(value, _when = "isinstance(value, int)"):
        return int
    @staticmethod
    @guard
    def typeof(value, _when = "isinstance(value, str)"):
        return str

assert TypeTeller.typeof(0) is int
TypeTeller.typeof(0.0) # throws
```

```python
class AllowedProcessor:
    def __init__(self, allowed):
        self._allowed = allowed
    @guard
    def process(self, value, _when = "value in self._allowed"):
        return "ok"
    @guard
    def process(self, value): # no _when expression => default
        return "fail"

ap = AllowedProcessor({1, 2, 3})
assert ap.process(1) == "ok"
assert ap.process(0) == "fail"
```

```python
guard.default_eval_args( # values to insert to all guards scopes
    office_hours = lambda: 9 <= datetime.now().hour < 18)

@guard
def at_work(*args, _when = "office_hours()", **kwargs):
    print("welcome")

@guard
def at_work(*args, **kwargs):
    print("come back tomorrow")

at_work() # either "welcome" or "come back tomorrow"
```