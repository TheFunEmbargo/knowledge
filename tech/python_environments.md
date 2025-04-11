# Python Environments


### Pyenv & Poetry
- Pyenv manages Python versions. To install: [Linux/Mac](https://github.com/pyenv/pyenv#installation) / [Windows](https://github.com/pyenv/pyenv#installation).
- Poetry manages dependencies. Install globally with `pip install --upgrade pip && pip install poetry`
- Life is easier with project directory environments `poetry config virtualenvs.in-project true`
  
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
