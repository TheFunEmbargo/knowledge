# [pre-commit](https://pre-commit.com/)


Pre-commit runs on commit, the commit does not take place if the checks it runs fails, fix the issues then commit again!

## install

On a fresh project follow the [docs](https://pre-commit.com/#installation)

On a newly cloned project ensure to run `pre-commit install` to install the hooks

## Usage

`.pre-commit-config.yaml` defines your pre-commit hooks

```
repos:
-   repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v2.3.0
    hooks:
    -   id: check-yaml
    -   id: end-of-file-fixer
    -   id: trailing-whitespace
-   repo: https://github.com/psf/black
    rev: 22.10.0
    hooks:
    -   id: black
```

Upon commit `git commit -m 'TICKETID my changes'` or running `pre-commit run --all-files` the checks will be perfomed

```
trim trailing whitespace.................................................Passed
isort....................................................................Failed
- hook id: isort
- files were modified by this hook

Fixing /home/you/dev/app/file.py

black....................................................................Passed
```

Oh no! isort has failed... so the commit won't have taken place (your changes are still staged, not committed. Pre-commit is a gate keeper to committing).

1. Fix the issue `isort --fix /home/you/dev/app/file.py`
1. Stage changes `git add /home/you/dev/app/file.py`
1. Commit changes `git commit -m 'TICKETID my changes'` 

```
trim trailing whitespace.................................................Passed
isort....................................................................Passed
black....................................................................Passed
```

4. Changes are now committed and can be pushed
