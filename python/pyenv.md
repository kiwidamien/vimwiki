# Pyenv and Virtual Environments

I much prefer conda myself, but some places prefer us to work with pyenv. Work with what you have.

## Getting a virtual environment

There are two steps to getting your virtual environment setup:
1. Installing the version of python you want to use (done by pyenv)
2. Installing the environement / packages you want (done by virtualenv)

To accomplish step one (not needed it you are _just_ creating an environment)
```bash
$ pyenv install 3.XX.YY  # install python 3.XX.YY
```

To accomplish the second:
```bash
$ pyenv virtualenv 3.XX.YY name-of-env
$ pyenv activate name-of-env
```

## Listing environment

```bash
# Shows the current python versions on the machine
$ pyenv versions

# Shows the current virtual envs available
$ pyenv virtualenvs
```

Just typing `pyenv` in the shell gives a pretty good overview of the commands available.

## Getting jupyter setup

Same as in conda -- long used [blog post](https://kiwidamien.github.io/save-the-environment-with-conda-and-how-to-let-others-run-your-programs.html)
```bash
$ pip install ipykernel

$ python -m ipykernel install --user --name name-of-env --display-name name-to-show-in-jupyter
```
If you leave the display name off, it will show as `name-of-env`, which is (almost) always what you want.


## Why conda is different (and I prefer it)

By design, `conda` installs an environment where many different programs can run (including Python). Conda will duplicate Python binary across all the different environments, but you can also install C / other things into a conda environment.

Pyenv + virtualenv work by having a pyenv manage the binaries, and then virtualenv changing the Python path. Being in two different virtualenvs, you may use the same binary (e.g. `3.11.12`) but have access to a completely different set of packages.
