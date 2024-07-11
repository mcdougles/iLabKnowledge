
1. CIO CI/CD uses `pytest` to run unit-tests, you can configure pytest by adding configuration options to a `pytest.ini` file or to the `pyproject.toml`. [pytest](https://docs.pytest.org/en/7.1.x/). The `pytest` is invoked with [`python3 -m pytest`](https://docs.pytest.org/en/7.1.x/how-to/usage.html#calling-pytest-through-python-m-pytest) to simplify finding the files to test.

1. CIO CI/CD uses `flake8` for linting, you can configure `flake8` by adding a `.flake8` file to your repository.