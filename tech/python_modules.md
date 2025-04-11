# Shared repository

Use a shared repository containing the shared modules.
```
company_shared/
├── company_database/
│   ├── company_database/
│   │   ├── tests/
│   │   │   └── test_model.py
│   │   ├── models.py
│   │   └── __init__.py
│   └── pyproject.toml
├── company_infra/
│   ├── company_infra/
│   ├── pyproject.toml
│   └── README.md
```

An example pyproject.toml, showing the dev depdency "company-infra" with the source defined.

```
[project]
name = "company-database"
version = "0.1.0"
description = "Package for database models"
readme = "README.md"
requires-python = ">=3.13"
dependencies = [
    "sqlalchemy"
]

[dependency-groups]
dev = [
    "company-infra"
]

[tool.uv.sources]
company-infra = { path = "../company_infra", editable = true }

```

Add the submodule to projects requiring the shared code.

```
git submodule add <git-url-to-company_shared> company_shared
git submodule update --init --recursive
```

Fetch submodule code in a cloned repo.

```
git submodule update --init --recursive
```

Add a module from the shared submodule using poetry. This will result in the source & dev dependency added to the pyproject.toml as shown above.

```
poetry add ./company_shared/company_infra --group dev
```
