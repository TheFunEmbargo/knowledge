# Python Tests

Tests ensure your code does what you think it does and, if run on push, continues to do so through CICD.

## Tags

Python, Tests, Unit Test, pytest 


## Pytest

### Run

Run all tests `pytest`

...or be specific about the test you're running: `pytest tests_folder/test_file.py::TestClass::test_function`

### Fixtures
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


### Mocking
Mocking prevents undesirable behavoir during testing, such as calling an API.

Given a class `Foo` we wish to mock...

*/workspace/utils/foo.py*
```python
class Foo():
    def call_api(self):
    	return requests.get("https://stuff.com/api/some/stuff")
```

...and where foo is used...

*/workspace/run/bar.py*
```python
from workspace.utils.foo import Foo

def bar():
    foo = Foo()
    return foo.call_api()
```

...create a fixture to mock `Foo`

*/workspace/tests/conftest.py*
```python
from unittest.mock import MagicMock

@fixture
def mock_foo():
    mock_foo_instance = MagicMock()
    mock_foo_instance.call_api.return_value = {"data": "mocked data"}
    
    mock_Foo_class = MagicMock(return_value=mock_foo_instance)
    yield mock_Foo_class
```

Ensure to mock `Foo` where it is used `workspace.run.bar`, **not** where it is defined `workspace.utils.foo`. The class may have already been imported by the time we patch it.

*workspace/tests/test_foo.py*
```python
from unittest.mock import patch
from workspace.run.bar import bar

def test_foo(mock_foo):
   with patch.object(workspace.run.bar, "Foo", mock_foo):
       result = bar()
       assert result['data'] == "mocked data"

```

To patch an object whilst retaining original methods

*workspace/tests/test_foo.py*
```python
def test_foo():
    import workspace 
    
    with patch.object(
        workspace.run.Foo, "print_blah"
    ) as mock_print_blah:
        mock_print_blah.return_value = "not printing blah"
	# test whatever needs testing
```


Now go forth and Unit Test.
