# Asynchronous Testing Decorator

Provides a Python decorator, which you can incorporate into your
project, that allows you to easily test code that uses Python's
`asyncio` infrastructure with the standard `unittest` library.

## Installation

Presuming your Python project is under Git source control:

    git remote add async-test https://github.com/wtsi-hgi/async-test.git
    git fetch async-test
    git merge --allow-unrelated-histories --no-commit async-test/src
    git mv async.py wherever/you/want/it/to/go
    git commit --no-edit
    git remote remove async-test

This merges the decorator's Git history into your repository's current
branch, from the `src` branch which contains just `async.py` in its
root. (Note that if your repository contains an `async.py` in its root,
you will have to sort out merge conflicts.) The `git mv` step then moves
the module to wherever is convenient for you within your repository.

## Usage

Import the `async_test` function into any testing module and use it as a
decorator for any test cases you may have:

```python
import unittest
from .async import async_test

class TestMyAsyncThingy(unittest.TestCase):
  @async_test
  async def test_something(self):
    foo = await something()
    self.assertEqual(foo, "bar")
```

The decorator can be used bare, as in the above example, or it can be
parametrised with a specific event loop:

```python
import asyncio
import unittest
from .async import async_test

my_event_loop = asyncio.get_event_loop()

class TestMyAsyncThingy(unittest.TestCase):
  @async_test(my_event_loop)
  async def test_something(self):
    foo = await something()
    self.assertEqual(foo, "bar")
```

The decorator works by wrapping the test case function up as a coroutine
and then running it until completion within the (optionally specified)
event loop.
