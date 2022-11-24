# pipx

From <https://pycon.switowski.com/02-packages/pipx/>:

> [pipx] installs pip packages in separate environments, but at the same time, those packages act as if they were installed globally. You don’t need to activate any virtualenv to run them.
> Now, each time you want to install a Python package on your computer (outside of a virtual environment specific to a project), do `pipx install my_package` instead of `pip install my_package`.


- Install pipx (<https://pypa.github.io/pipx/installation/>):
```
python3 -m pip install --user -U pipx
```

- list installed apps:

```
pipx list
```

- run a program without installing it:

```
pipx run pycowsay moooo!
```

- note that pipx can only be used to install apps (such as, flake8, black), but not libraries (such as pynvim, jedi).
