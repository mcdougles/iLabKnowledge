Configure scripts in `pyproject.toml`.

The static site pipeline uses Poetry's script interface to run step-specific tasks on your project. It will run these tasks using [`poetry run <script-name>`](https://python-poetry.org/docs/cli/#run). Therefore, your `pyproject.toml` must be configured with a `[tool.poetry.scripts]` section that defines these required scripts.

- `package`: This script must generate the static site into a **distribution directory** such as `site` or `dist` at the root of your repo. The directory used should match the `distribution-directory` (default: `site`) specified in your repository configuration (`build.yml`) file.
- `lint`: This script should run the project's linter.
- `unit-test`: This script should run your unit tests.

Note that the target of each script must be an [entry point](https://setuptools.pypa.io/en/latest/pkg_resources.html#entry-points) - a dotted path to a [package](https://docs.python.org/3/reference/import.html#regular-packages) followed by a `:` and a function (or other callable) name.

The following is an example of the `[tool.poetry.scripts]` section of `pyproject.toml`, and a simple package (`scripts/__init__.py`) that provides the implementations.

**`pyproject.toml`**:

```toml
[tool.poetry.scripts]
package = "scripts:package"
unit-test = "scripts:unit_test"
lint = "scripts:empty"
```

**`scripts/__init__.py`**:

```python
import subprocess

def package():
  subprocess.run(['mkdocs','build'])

def unit_test():
  subprocess.run(['poetry','run','test'])

def empty():
  pass
```

If you do not currently write unit tests or lint, you may use a placeholder such as the `empty()` function above.
