#Python Dev Env
###Goals
* Create a Python Dev enviroment on a given system with as few dependencies on any packaging system as possible.
* Understand the ecosystem of python project utilities
   * `pyenv`
   * `pip`
   * `virtualenv`
   * `virtualenvwrapper`
   * `pyenv-virtualenv`
   * `pyenv-virtualenvwrapper`
   * `pipenv`
   * `pew`
   * `anaconda`/`conda`
   * setuptools
   * wheel
   * `cookiecutter`
* Solve any chicken and egg questions: 
   * Use OS/system package manager, or discreet python utilities?
      * If you use OS/system package manager, you will only be as current as their repository.
   * `pip` or `pyenv ` first?
      * `pyenv` has no `python` dependencies, so if it doesn't exist on the system at all, you'll be fine.
      * `pip` is installed by default by `pyenv`
      * You can upgrade this `pip` with either `pip install -U pip` or the curlpipe method. Both will only upgrade the currently active version of Python.
   * `pip` or `virtualenv` first?
      * virtualenv also installs `pip`. AAAAAGH!!! Did I mention it also installs setuptools too?
         * While this is weird, it works out that virtualenv's `pip` will override pyenv's when the virtualenv is activated.
      * Installing with `pip install virtualenv` is stated to install globally. What if this is done in a `pyenv`?
         * It will install `virtualenv` for that version of `python`. Other versions of `python` managed by `pyenv` will error out if `virtualenv` is invoked, but will also report that `virtualenv` is installed for a different version.
         
         ```
         # virtualenv
pyenv: virtualenv: command not found
The 'virtualenv' command exists in these Python versions:
  2.7.14
         ```
   
         * NOTE: Older versions of python may get a virtualenv version of `pip` earlier than 9.0.1. You may run into [this TLS issue](https://stackoverflow.com/questions/49748063/pip-install-fails-for-every-package-could-not-find-a-version-that-satisfies/49748494#49748494). The fix is to upgrade `pip` and setuptools. The `pip` upgrade will need to be done with a curlpipe because `pip install -U pip` will hit the same issue. The curlpipe will only upgrade `pip` for the current virtualenv. This is because the script is piped into `python` which is currently shimmed by pyenv and virtualenv
         
         ```
         curl https://bootstrap.pypa.io/get-pip.py | python
         pip install --upgrade setuptools
         ```
            
   * If I install `pip` with `get-pip.py` in a `pyenv` folder, is this `pip` relative to the OS/System or the `pyenv` folder?
   * `pyenv` or `virtualenv` first?
      * `virtualenv` should be installed per `pyenv` environment. Since `pip` is intstalled by `pyenv`, we should be able to `pip install virtualenv`.	
   * If `virtualenv` is used at the project level, where does it get installed, and how is the path updated to be able to call `virtualenv`? Is it shims?
   * When I commit code to `git` in a `virtualenv`, do I commit all the folders used by `virtualenv`?
   * How to switch `python` version in a `virtualenv`?
   * When/where do you install `pipsi`?
   * What should be installed with `pipsi`?
      * Anything that is a system level utility, not a python project level artifact, e.g. [`pygmentize`](http://pygments.org/docs/cmdline/)
      


###Meta
* experiment using python docker image

##Setup pip, virtualenv, pipenv, pyenv, pipsi
###OS/System level
* bootstrap `pyenv`. It doesn't depend on `python`
* 
```
curl https://raw.githubusercontent.com/pyenv/pyenv-installer/master/bin/pyenv-installer | bash
```

* put the following in .bashrc or .bash_local
* 
```
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
```
* `source` it in your current shell
   * `. ~/.bashrc`
* pick a `python` or two to install
   * `pyenv install -l`
* install a `python`
   * `pyenv install 3.7.0`

### Folder/Project level
* activate a `python` in the current folder (has to be installed already)
   * `pyenv local 3.7.0`
* check if `pip` is already installed (see [Do I need to install pip?](https://pip.pypa.io/en/stable/installing/#do-i-need-to-install-pip))
   * `pip -V`
* Install it if it's not installed
   * Does this install to OS or pyenv?
   * `curl -O https://bootstrap.pypa.io/get-pip.py`
   * `./get-pip.py`
* upgrade pip (only for your current `pyenv` context)
	* `pip install -U pip`
* install `virtualenv` each time you create a new `pyenv` directory
	* `pip install virtualenv`
	* Is this the right way to do it
	* Do I do this every time I create another `pyenv`?





##The "Not So Sure About These" Section

* install pipsi
	* `python get-pipsi.py`
	* `vim ~/.bashrc`
		* add `export PATH=/Users/peteyoung/.local/bin:$PATH`
	* `. ~/.bashrc`

* Fancy Installation of Pipenv (it's not even on the pipenv site anymore)
	* `pipsi install pew`
	* `pipsi install pipenv`
	 
#### Why do this
* Install/upgrade pip and pipsi with pyenv's pip
   * `$(pyenv root)/shims/pip install -U pip pipsi`


##Change python version with pyenv
* show python versions available for installation
   * 
`pyenv install -l`
* install a python
   * 
`pyenv install XYZ`
* Change to that python in your current directory
   * 
`pyenv local XYZ`


##Installation Docs
* [pyenv](https://github.com/pyenv/pyenv-installer#installation--update--uninstallation)
* [pip](https://virtualenv.pypa.io/en/stable/installation/)
* [virtualenv](https://virtualenv.pypa.io/en/stable/installation/)

##Links
* [Python Virtual Environments: A Primer](https://realpython.com/python-virtual-environments-a-primer/)
* [What is the difference between venv, pyvenv, pyenv, virtualenv, virtualenvwrapper, pipenv, etc?](https://stackoverflow.com/questions/41573587/what-is-the-difference-between-venv-pyvenv-pyenv-virtualenv-virtualenvwrappe)
* [Installing Python Modules](https://docs.python.org/3/installing/index.html)
* [The definitive guide to setup my Python workspace](https://medium.com/@henriquebastos/the-definitive-guide-to-setup-my-python-workspace-628d68552e14)

##Links (older)
* [pipsi - github](https://github.com/mitsuhiko/pipsi)
* [pyenv - github](https://github.com/pyenv/pyenv)
* [pyenv-installer - github](https://github.com/pyenv/pyenv-installer)
* [install pip](https://pip.pypa.io/en/stable/installing/)
* [Virtualenv](https://virtualenv.pypa.io/en/stable/)
* [install Virtualenv](https://virtualenv.pypa.io/en/stable/installation/)
* [Pipenv: Sacred Marriage of Pipfile, Pip, & Virtualenv](http://docs.pipenv.org/en/latest/)
* [Fancy Installation of Pipenv](http://docs.pipenv.org/en/latest/advanced.html#fancy-installation-of-pipenv)
* [Pipenv & Virtual Environments](http://docs.pipenv.org/en/latest/basics.html#make-sure-you-ve-got-python-pip)
* [Create an Excellent Python Dev Env](http://www.dougalmatthews.com/2016/Nov/12/create-an-excellent-python-dev-env/)
* [pygments cli](http://pygments.org/docs/cmdline/)