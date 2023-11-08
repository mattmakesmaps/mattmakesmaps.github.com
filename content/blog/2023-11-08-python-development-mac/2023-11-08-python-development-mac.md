---
title: Python Dev Setup on Mac | Gotchas Documented
draft: false
description: Notes on setting up VS Code for Python on Mac
date: 2023-11-08
tags: python
---

# Tool Installation

## Python Base Install

- Install [Homebrew](https://brew.sh/#install)
- Install Python 3 via homebrew: `brew install python`

At this point, you can use `python3` and `pip3` commands, but for `pipenv` you'll need
to symlink `pip` and `python`.

To do this, note the `caveat` message produced by `brew install python`:

```
Python has been installed as
  /opt/homebrew/bin/python3

Unversioned symlinks `python`, `python-config`, `pip` etc. pointing to
`python3`, `python3-config`, `pip3` etc., respectively, have been installed into
  /opt/homebrew/opt/python@3.11/libexec/bin
```

We need to add the following to the end our `~/.zshrc` file (Per this
[SO Post](https://stackoverflow.com/questions/11530090/adding-a-new-entry-to-the-path-variable-in-zsh)):

```
# point python to python3
path+=('/opt/homebrew/opt/python@3.11/libexec/bin')
export PATH
```

## Pipenv

Get `pipenv` setup to manage dependencies and virtual environments.
Reference [Hitchiker's Guide to Python](https://docs.python-guide.org/dev/virtualenvs/)

**NOTE:** The link above contains a previous section on Python base install. These instructions didn't
address symlinking `python` to `python3`. Instead of using these, follow notes in `Python Base Install`
section above.

## VS Code

- Linter: [Pylint Extension](https://marketplace.visualstudio.com/items?itemName=ms-python.pylint)
- Code formatter: [Black Formatter Extension](https://marketplace.visualstudio.com/items?itemName=ms-python.black-formatter)

# Project Setup

## Python Environment With Pipenv

- Cloning an existing project with a `Pipfile`, run `pipenv install --dev`. This creates
a new virtualenv and installs dependencies.
- Create a new project, just install the first dependency with `pipenv install <package foo>`
from the project's root dir. That will generate the new virtual env.

## Visual Studio Code Launch Configuration

The following is an example script launch configuration. Note the relative path
to the script file in `program` and the command line args in `args`.

```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "mp3-convert.py with test args",
            "type": "python",
            "request": "launch",
            "program": "${workspaceFolder}/mp3-convert/mp3-convert.py",
            "console": "integratedTerminal",
            "justMyCode": true,
            "args": [
                "${workspaceFolder}/test_data/source",
                "${workspaceFolder}/test_data/dest"
            ]
        }
    ]
}
```