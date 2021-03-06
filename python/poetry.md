# Python Poetry

- For basics (initialize project, create a virtual env with poetry, etc.) see https://github.com/agisga/coding_notes/blob/master/python/hypermodern_python.org

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
