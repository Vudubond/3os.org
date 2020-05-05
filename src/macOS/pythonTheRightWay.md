title: How to Install Python on MacOS the Right Way and Use Python Virtual Environment
description: How to Install Python on MacOS the Right Way and Use Python Virtual Environment

<link rel="stylesheet" href="/assets/CSS/roundedCorners.css">

# How to Install and use Python on MacOS the Right Way

## Intro

Using and developing with Python on MacOS sometimes may be frustrating...

The reason for that is that MacOS uses __Python 2__ for its core system with __pip__ as a package manager. When __Xcode Command Line Tools__ are installed __Python 3__ and __pip3__ package manager will be available at the cli. When using __Python2, Python3__ and their package managers this way, all the packages will be installed at the system level and my effect the native packages and their dependences , this can __break__ or lead to __unwanted bugs__ in OS.

The right way to use python at MacOS is to use __Virtual Environments__ for python. This way all the system related versions of python and their packages won't be affected and use by you.

## Preparation and Cleanup

If you already made the mistake of using python and installing packages at the system level, we need to do some cleanups before we begin the installation in order to return the state of the system level to the native state as possible

If __Xcode Command Line Tools__ is installed list the pip3 packages listed in the system level.

```python
pip3 list
```

The only packages that should be installed are (package version may vary):

| __Package__      | __Version__ |
|------------------|-------------|
| appdirs          | 1.4.3       |
| certifi          | 2020.4.5.1  |
| distlib          | 0.3.0       |
| filelock         | 3.0.12      |
| pip              | 20.1        |
| setuptools       | 46.1.3      |
| six              | 1.14.0      |
| virtualenv       | 20.0.18     |
| virtualenv-clone | 0.5.4       |

If any other packages is listed at you system, uninstall them

```python
pip3 uninstall <package name>
```

List all the brew packages you installed on your mac

```bash
brew list
```

Look for any Python packages (including pipenv) you may have installed and uninstall them, we won't be using them and we do not want any conflicts with them.

It's time to begin the installation and configuration
We will be using __pyenv__ and __pyenv-virtualenv__ to manage our python environments

## Installing and configuring pyenv, pyenv-virtualenv

In order to use pyenv, pyenv-virtualenv without conflicting with the native MacOS python we need to add some configuration to our ~/.zshrc config (for mac os catalina) or your bash config if you are still using bash.

__It's very imported to maintain the order of the configuration for the loading order__

* First of all we need to include your Executable Paths. In the example we added all the common paths, including the paths for pyenv, pyenv-virtualenv. If you have any other path that you use, you can add them at the same line or create a new line below this one.
* Second to Executable Paths we will add two if statements that will check if the pyenv,pyenv-virtualenv are installed, if they are it will load them. If they aren't and you are using the same zsh or bash config it will ignore loading them
* Third is a fix for __brew, brew doctor__. When using this method it may conflict with brew as it uses python as well. If you run run __brew doctor__ without the fix, it will show config warnings related to the python configuration files.

Configuration for ~/.zshrc or bash profile

```config
# Executable Paths
export PATH="/usr/local/bin:/usr/local/sbin:/Users/${USER}/.local/bin:/usr/bin:/usr/sbin:/bin:/sbin:$PATH"
export PATH="/usr/local/opt/curl/bin:$PATH"

# pyenv, pyenv-virtualenv
if which pyenv >/dev/null; then
  eval "$(pyenv init -)"
fi

if which pyenv-virtualenv-init >/dev/null; then
  eval "$(pyenv virtualenv-init -)"
fi

# pyenv brew fix
if which pyenv >/dev/null 2>&1; then
  alias brew='env PATH=${PATH//$(pyenv root)\/shims:/} brew'
fi
```

After you saved your configuration the best way to load it is to close your terminal session and open it again. This will load the session with your updated configuration.
There should be no errors at the new session.

This will install both pyenv and pyenv-virtualenv

```bash
brew install pyenv-virtualenv
```

Test if pyenv loaded currently

```python
pyenv -v
```

After the installation we would like to set a system level python version, you can chose the default from the list available from the pyenv

List available Python Version and find the version suited for your needs:

```python
pyenv install --list
```

Install Requeued Python Version (Exmaple version 3.8.2) as a default system

```python
pyenv install 3.8.2
```

Set it as global

```python
pyenv global 3.8.2
```

You can install multiply versions of python at the same time.

List all installed python versions and virtual environments and their python versions

```python
pyenv versions
```

Now let's test our system Python version we set before, it should be the version you choose as Global before

```bash
python -V
```

So far we cleaned your system and installed and configured pyenv, pyenv-virtualenv.

## How to use pyenv-virtualenv

Now let's understand how to use Python Virtual Environment with __pyenv-virtualenv__

Full documentation can be found at the original repo at git hub:
[pyenv-virtualenv github](https://github.com/pyenv/pyenv-virtualenv "pyenv-virtualenv github")

We will list here some basic examples for a quick start and basic understanding

To create a virtualenv for the Python version used with pyenv, run pyenv virtualenv, specifying the Python version you want and the name of the virtualenv directory. For example,

```python
pyenv virtualenv 3.8.2 my-project-name
```

This will create a virtualenv based on Python 3.8.2 under $(pyenv root)/versions in a folder called my-project-name

__Activating virtualenv automatically for project__

The best way we found to activate the virtualenv at your project is to link the projects directory to the virtualenv.

cd to the project's directory and link the virtualenv for example _my-project-name_ virtualenv

```python
pyenv local my-project-name
```

This will activate the linked virtualenv every time you cd to this directory automatically
From now you can use pip to install any packages you need for your project, the location of the installed packages will be at $(pyenv root)/versions/<virtualenv name>

__Activating virtualenv manually for project__

You can also activate and deactivate a pyenv virtualenv manually:

```python
pyenv activate <virtualenv name>
pyenv deactivate
```

This will alow you to use multiply versions of python or packages for the same project

List existing virtualenvs

```python
pyenv virtualenvs
```

Delete existing virtualenv

```python
pyenv uninstall my-virtual-env
```

or

```python
pyenv virtualenv-delete my-virtual-env
```

You and your MacOS should be ready for using python the __right way__ without conflicting any system or Xcode Command Line Tools (used by brew)