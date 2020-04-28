# Using Python Virtual Environments

Python virtual environments are super handy. They prevent new packages that you install from interfering with old ones and allow you to you install things if you're not a system administrator.

## Making a virtualenv

```bash
# This will build a blank 'python' at $DESTDIR
virtualenv $DESTDIR

# This will do the same, but will use a specific version of python
# (in this case the system's installation of python3)
virtualenv -p python3 $DESTDIR
```

Note: make sure you know which python is being used to make your environment! If you have a python module loaded your environment may use a different python than you expect. To check which one will be used you can use `python --version` and `which python` to make sure it's the right version and check where the version is installed on the system.

### Using a virtualenv

Activate the environment with

```bash
source $DESTDIR/bin/activate
```

Once it's active you can install all the Python packages you want to and they'll be put in the folder $DESTDIR instead of in the computer's default Python installation.

You might have noticed your shell's prompt changed to include the full path to $DESTDIR, not all programs for making virtual environments will do this, but it's a useful visual indicator to remind you that you're using one.

### Finish using a virtualenv

Deactivate your environment with

```bash
source deactivate
```

## Next level

If you find yourself wanting to use different versions of python than we have installed you can also install [pyenv](https://github.com/pyenv/pyenv) for yourself. Pyenv will manage the installation of new versions for you and they're all stored wherever you tell pyenv to install itself so you don't need to be an admin or know how to compile anything.

Pyenv can also manage virtual environments for you if you install [pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv).
