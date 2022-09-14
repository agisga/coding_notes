# Python Poetry

- For basics (initialize project, create a virtual env with poetry, etc.) see https://github.com/agisga/coding_notes/blob/master/python/hypermodern_python.org

## Poetry and Pyenv

Poetry may not respect the python selected with pyenv. A workaround is (<https://github.com/python-poetry/poetry/issues/5252#issuecomment-1194889183>):
```
pyenv local 3.7.12  # for example
poetry env use $(pyenv which python)
poetry install
```

## Mostly, what's needed to make poetry work for my setup at work

- To install Poetry in a directory other than $HOME, follow the instructions at <https://python-poetry.org/docs/>:
  ```                                                                                                   
  curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py > get-poetry.py 
  POETRY_HOME=/alexej.gossmann_applications/poetry python get-poetry.py                                 
  ``` 
  
- If for some reason Poetry cannot successfully create a virtual environment (it just hangs), a workaround that works is to interrupt poetry, and create the virtual environment manually with
  ```
  python -m venv /path/to/virtual/environment/that/poetry/just/failed/to/create/fully`.
  ```
   Then run `poetry install`, and it works.

- Install a package *manually* inside the virtual environment created by Poetry for the project. For example, install a newer version of pip when dealing with error messages such as "X is not a supported wheel on this platform" (from https://github.com/python-poetry/poetry/issues/2688):
  ```
  /Users/allen.c/Library/Caches/pypoetry/virtualenvs/app-6aZ328yD-py3.8/bin/python -m pip install --upgrade pip
  ```

- To get information about Poetry's virtual environments use `poetry env info` or `poetry env list`.
- To delete a virtual environment use `poetry env remove <my-env-id>`.

- Due to the issue discussed in <https://github.com/python-poetry/poetry/issues/2692#issuecomment-1235683370>, may need to run poetry with
  ```
  PYTHON_KEYRING_BACKEND="keyring.backends.null.Keyring" poetry <...>
  ```

## Mostly, what's needed to make poetry work for my Arch linux setup at home

- Use the *pipx* installation method: <https://python-poetry.org/docs/#installing-with-pipx> (although in the end I ended up using plain old `pip`; also may need to uninstall and re-install python versions with `pyenv`...).

- Sometimes you need use the `env use` command to tell Poetry which Python version to use for the current project (despite having `pyenv`.. not sure why...). See: <https://python-poetry.org/docs/managing-environments/#switching-between-environments>.
    ```
    poetry env use /full/path/to/python
    ```

