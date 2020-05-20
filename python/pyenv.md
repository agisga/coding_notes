# pyenv

Partially based on <https://pycon.switowski.com/02-packages/pyenv/>.

- Install pyenv:
  ```
  curl https://pyenv.run | bash
  ```
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
