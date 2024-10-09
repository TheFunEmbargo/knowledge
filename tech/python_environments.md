# Python Environments


### Pyenv & Poetry
- Pyenv manages Python versions. To install: [Linux/Mac](https://github.com/pyenv/pyenv#installation) / [Windows](https://github.com/pyenv/pyenv#installation).
- Poetry manages dependencies. Install globally with `pip install --upgrade pip && pip install poetry`

Install python version from `.python-version` file

```shell
pyenv install
```

...[or install a new python version & set as global python](https://github.com/pyenv/pyenv?tab=readme-ov-file#usage)

```shell
pyenv install 3.10
pyenv global 3.10
```

Local sets the python to use to a specific dir, global ... globally 


Install python dependencies from `pyproject.toml` file

```shell
poetry install
```

...or start a fresh .venv

```shell
poetry init
```

Activate the virtual environment

```shell
poetry shell
```





Programmatically manipulate a environments dependencies

```python
import os
import sys
import subprocess
from glob import glob
from importlib import import_module


class InstallDependencies:
    """Installs dependencies at runtime

    1) will create a .venv copied from current python runtime interpreter
    2) install dependencies into .venv
    3) add .venv site-packages to path, making dependencies availble to runtime interpreter
    """

    def __call__(self, dependencies: list[str]) -> list[str]:
        # create venv if not there
        venv_path = os.path.join(os.getcwd(), ".venv")
        if not os.path.isdir(venv_path):
            self.create_venv(venv_path)

        # locate pip & add site-packages to path
        venv_pip_path = glob(os.path.join(venv_path, "*", "pip"))[0]
        self.add_site_packages_to_path(venv_path)

        # attempt import, return if imported
        failed_imports = self.import_dependencies(dependencies)
        if len(failed_imports) == 0:
            return

        # incase of failed imports, add dependencies, add path & import
        self.add_dependencies(venv_pip_path, dependencies)
        self.add_site_packages_to_path(venv_path)
        failed_imports = self.import_dependencies(dependencies)
        if failed_imports:
            raise ImportError(f"Couldn't import dependencies {', '.join(failed_imports)}")

    def create_venv(self, venv_path):
        try:
            subprocess.run([sys.executable, "-m", "venv", "--copies", venv_path], check=True)
        except subprocess.CalledProcessError as e:
            raise Exception("Couldn't create .venv", e)

    def add_dependencies(self, venv_pip_path: str, dependencies: list[str]):
        try:
            subprocess.run([venv_pip_path, "install", *dependencies], check=True)
        except subprocess.CalledProcessError as e:
            raise Exception("Couldn't install dependency", e)

    def import_dependencies(self, dependencies: list[str]) -> list[str]:
        failed = []
        for dependency in dependencies:
            try:
                import_module(dependency)
            except ImportError:
                failed.append(dependency)
        return failed

    def find_folder(self, path: str, folder: str) -> str | None:
        for root, dirs, files in os.walk(path):
            if folder in dirs:
                return os.path.join(root, folder)
        return None

    def add_site_packages_to_path(self, venv_path):
        venv_packages_path = self.find_folder(path=venv_path, folder="site-packages")
        sys.path.insert(
            0,
            venv_packages_path,
        )


install = InstallDependencies()
install(dependencies=["geopandas"])

```
