# pyenv

Partially based on <https://pycon.switowski.com/02-packages/pyenv/>.

- Install pyenv:
  ```
  curl https://pyenv.run | bash
  ```
  * If you with to install it at a non-standard location (e.g., there may be issues installing in `~/.pyenv` when `~/` is a directory on a remote/network drive), then follow the instructions given under "Basic GitHub Checkout" at <https://github.com/pyenv/pyenv#installation>.
- Install a new version of Python with pyenv:
  ```
  pyenv install --list
  # for example:
  pyenv install 3.8.3
  pyenv install anaconda3-2020.02
  ```
- Switch Python version *globally*:
  ```
  pyenv versions
  # for example:
  pyenv global 3.8.3
  ```
- Switch Python version *locally*, i.e., change Python version only for the current directory and its subdirectories:
  ```
  pyenv versions
  # for example:
  pyenv local anaconda3-2020.02
  ```

## pyenv-virtualenv

- Installation on Ubuntu:
  ```
  git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
  ```

  add
  ```
  eval "$(pyenv virtualenv-init -)"
  ```
  to `~/.bashrc` (it should already contain `eval "$(pyenv init -)"`).

- Create a virtual environment named "my-venv" that uses Python 3.8.5
  ```
  pyenv virtualenv 3.8.5 my-venv
  ```
- List available virtual environments
  ```
  pyenv virtualenvs
  ```
  There are two entries for each virtualenv, and the shorter one is just a symlink.
- Activate virtual environment "my-venv"
  ```
  pyenv activate my-venv
  ```
- You can use `pyenv local` to set a virtual environment to be activated/deactivated automatically on entering/leaving a directory.
- Deactivate
  ```
  pyenv deactivate
  ```


