# Python Tests

Tests ensure your code does what you think it does and, if run on push, continues to do so through CICD.

## Tags

Python, Tests, Unit Test, pytest 


## Pytest

Fixtures create reusable test state, write them in a conftest.py

Its a good idea to follow some consistent naming scheme when writing fixtures.
```python
import pytest

@pytest.fixture
def foo1():
    return [1, 2, 3, 4, 5]

# define foo2, foo3, bar1, bar2, bar3 ...
```

Parameterise test inputs to test may situations with a single generic test. The first argument is the name used to fetch the respective fixture in the second arg. The second arg is the fixture to be fetched.

The comment string after the assert will be printed if the test fails, which makes for easier debugging if written well.

```python
@pytest.mark.parametrize(
    [
        "foo_fixture",
        "bar_fixture",
    ],
    [
        pytest.param("foo1", "bar1"),
        pytest.param("foo2", "bar2"),
        pytest.param("foo3", "bar3"),
    ],
)
def test_foo_and_bar(
    request: "pytest.FixtureRequest",
    foo_fixture: str,
    bar_fixture: str,
):
    foo = request.getfixturevalue(foo_fixture)
    bar = request.getfixturevalue(bar_fixture)

    # test logic

    assert foo == bar, """Foo should equal bar"""
```

Now go forth and Unit Test.
