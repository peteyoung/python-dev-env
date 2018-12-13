# Building a Python Dev Env
### Goals
* Create a Python Dev enviroment on a given system with as few dependencies on any packaging system as possible.
* Understand the ecosystem and interactions of python project utilities so we can build to suit
   * `pyenv`
   * `pip`
      * `pip install --user ...`
   * `virtualenv`
   * `virtualenvwrapper`
   * `pyenv-virtualenv`
   * `pyenv-virtualenvwrapper`
   * `pipenv`
   * `pew`
   * `tox`
   * `anaconda`/`conda`
   * setuptools
   * wheel
   * poetry
   * `cookiecutter`
* Solve any chicken and egg questions: 
   * Use OS/system package manager, or discrete python utilities?
      * If you use OS/system package manager, you will only be as current as their repository.
   * `pip` or `pyenv ` first?
      * `pyenv` has no `python` dependencies, so if it doesn't exist on the system at all, you'll be fine.
      * `pip` is installed by default by `pyenv` during a Python build/installation. It is installed relative to that Python install, and is only visible to that Python install.
      * You can upgrade this `pip` with either `pip install -U pip` or the curl pipe method. Both will only upgrade `pip` in the currently active version of Python.
   * `pip` or `virtualenv` first?
      * `virtualenv` also installs `pip` in each new virtual environment. AAAAAGH!!! Did I mention it also installs setuptools too? Both are only relative to, and visible to the Python copied into that virtual environment.
         * While this is weird, it works out that `virtualenv`'s `pip` will override `pyenv`'s when the virtual environment is `activate`d.
      * Installing with `pip install virtualenv` is stated to install globally. What if this is done with a `pip` installed with a version of Python by `pyenv`?
         * It will install `virtualenv` for the version of Python that's currently active via `pyenv`. Other versions of Python managed by `pyenv` will error out if `virtualenv` is invoked and has not been installed for said other version of Python. However, `pyenv` will also report that `virtualenv` is installed for a different version of Python.
         
         ```
         # virtualenv
         pyenv: virtualenv: command not found
         The 'virtualenv' command exists in these Python versions:
         2.7.14
         ```
   
         * NOTE: Older versions of Python may get a `virtualenv` version of `pip` earlier than 9.0.1. You may run into [this TLS issue](https://stackoverflow.com/questions/49748063/pip-install-fails-for-every-package-could-not-find-a-version-that-satisfies/49748494#49748494). The fix is to upgrade `pip` and setuptools. The `pip` upgrade will need to be done with a curl pipe because `pip install -U pip` will have the same issue (it's a Catch 22). The curl pipe will only upgrade `pip` shimmed by the current `virtualenv`. This is because the script is piped into a Python which is currently copied and PATHed by `virtualenv`
         
         ```
         curl https://bootstrap.pypa.io/get-pip.py | python
         pip install --upgrade setuptools
         ```
            
   * Should `virtualenv` be installed to each `pyenv` installed Python, or should it be installed once to a user local directory with `pip install --user virtualenv`, or should it be installed in the global site-package?
      * `virtualenv` will work "ok" either way. But you should just go ahead and use `pyenv-virtualenv` instead.
   * Should `virtualenvwrapper` be installed to each `pyenv` installed Python, or should it be installed once to a user local directory with `pip install --user virtualenvwrapper `, or should it be installed in the global site-package?
      * None of the above. I highly recommend `pyenv-virtualenvwrapper` instead. I ran into some inconsistencies when using `pyenv` and `virtualenvwrapper` at the same time. `virtualenvwrapper.sh` can occasionally throw `pyenv` out of whack with what current Python version should be active.
   * If I install `pip` with `get-pip.py` in a `pyenv` folder, is this `pip` relative to the OS/System or the `pyenv` folder?
      * **TODO** test and answer
   * `pyenv` or `virtualenv` first?
      * `pyenv` first for sure. `virtualenv` will capture (by copying to its folder structure) the currently active Python for its virtual environment. Since `pip` is installed by `pyenv`, we can `pip install virtualenv`.	This will have to be done for each version of Python installed by `pyenv`
   * If `virtualenv` is used at the project level, where does it get installed/created, and how is the path updated to be able to call `virtualenv`? Is it shims?
      * `virtualenv` isn't necessarily used at the project level (a virtual environment for every project). You could use the same `virtualenv` environment for more than one project, but you'd be sharing dependencies across those projects. More often then not, a virtual environment will be paired with a single project. 
      * The folder for the virtual environment will be created in the current directory when `virtualenv FooVenv` is invoked. This doesn't necessarily have to be in the same folder as a project.
      * A virtual environment, once `activate`d, is active for the life of the shell session until `deactivate` is called, or the shell is terminated
   * When I commit code to `git` in a `virtualenv`, do I commit all the folders and files used by `virtualenv`?
      * This depends on whether or not you created your virtual environment in the same directory as your project. If so, since they're in a subfolder in your project folder, it's up to you. You could commit or .gitignore them. `virtualenvwrapper` avoids this problem by centralizing virtualenvs in a .folder in your home directory.
         * **TODO** is question relevant? Was asked w/o understanding interaction of `pyenv`/`virtualenv`
   * How to switch the Python version in a `virtualenv`?
      * Since `virtualenv` copies the currently active Python while creating the virtual environment, your best bet is to:
         1.  create a new virtual environment with the Python you want.
         1. `activate` it.
         1. Pull down your code from the repository.
         1. `pip install -r requirements.txt`
         1. Go on your merry way.
   * Should `virtualenvwrapper` be installed globally, as user local with `pip install --user`, per `pyenv` Python versions, or per `virtualenv`?
      * None of them feel appropriate given a `pyenv` installation since `pyenv` creates essentially a new Python environment for each Python version installed.
         * Globally might work since it's mostly written in shell script. But what about the hook loader written in Python? It makes sense given `virtualenvwrapper` is meant to make it easy to switch between centralized `virtualenv` virtual environments. Each of those could potentially have captured completely different Python versions.
         * A user local install shares the same concerns and observations as a global install.
         * Per `pyenv` environment might make sense due to the hook loader. But what happens to `workon` once you've already decided to `workon` something and a virtual environment is activated?
         * Per `virtualenv` environment doesn't make any sense. `virtualenvwrapper` is made to switch easily between virtual environments. Why trap an install inside one? All separate `virtualenvwrapper` installs would need to be configured to point to the same folder for virtual environments.
      * The recommendation is to install "into the same global site-packages area where `virtualenv` is installed." See [here](https://virtualenvwrapper.readthedocs.io/en/latest/install.html) and [here](https://docs.python.org/install/index.html#alternate-installation-the-user-scheme).
         * `virtualenv` itself needs a Python available to run. It also captures a copy of the available Python during creation of and into a virtual environment. This would seem to dictate that `virtualenv` should be installed per `pyenv` Python installation.
         * Taking the above and the site-packages area recommendation into consideration, it seems that `virtualenvwrapper` should be installed into each `pyenv` Python installation as well.
      * Strange things happen while experimenting with virtualenvwrapper in a `pyenv`. Things like PYENV_VERSION and/or `.python-version` being ignored completely while changing directories. Seems it is best to not use `virtualenvwrapper` together with `pyenv`
   * What needs to be installed before you install `pipenv`
      * Nothing really besides a Python or `pyenv`. 
      * `pip install --user pipenv` tries to install the following:
         * `pip` (exists already from pyenv)
         * certifi
         * `virtualenv`
         * setuptools (exists already from pyenv)
         * `virtualenv-clone`
         * `pipenv`
   * When/where do you install `pipsi`?
   * What should be installed with `pipsi`?
      * Anything that is a system level utility, not a python project level artifact:
         * [s3cmd](https://s3tools.org/s3cmd)
         * [mercurial and `hg`](https://www.mercurial-scm.org/doc/hg.1.html)
         * [Pygments and `pygmentize`](http://pygments.org/docs/cmdline/)
         * [Jedi](https://pypi.org/project/jedi/)
         * [Ranger](https://ranger.github.io/) (it's a little weird though, see [here](https://github.com/ranger/ranger/issues/621))
   * How does `pyenv` relate to `#!/usr/bin/env python`?

### Things Learned
* `pyenv` installs shimmed versions of stuff at its root level (no Python currently configured) and for each version of Python it installs
   * The main ones to know are `pip` and, of course, `python`
* `virtualenv` virtual environments are not tied to a project, but you can activate a virtual environment for one or more projects. They'll get the same Python version and libs if you use the same `activate`d environment.
   * At first I believed you needed to create the virtual environment in the same folder as a project, and that it was tied to that project. Some people follow this approach. You can choose to commit or .gitignore the `virtualenv` directories. 
* `virtualenv ` also installs shimmed versions of stuff
* when a `virtualenv` is `activate`d, its Python version will override any `pyenv` Python version settings for the life of a shell session or until `deactivate` is called
* `virtualenv`'s `deactivate` is a shell function created when `activate` is called



### Meta
* experiment using as clean a python docker image as possible
* Answer:
   * What X does
   * When to use X
   * How to use X
   * What happens to environment when X is installed
   * How X install affects installs of Y, Z, AA, BB, etc...


## Setup the Development Environment
### OS/System level
* bootstrap `pyenv`. It doesn't depend on `python`
   * ```
     curl https://raw.githubusercontent.com/pyenv/pyenv-installer/master/bin/pyenv-installer | bash
     ```

* put the following in .bashrc or .bash_local
    * ```
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

### Python level (done for a given install of Python)
* activate a Python (has to be installed already)
   * `pyenv local 3.7.0`
* check if `pip` is already installed (see [Do I need to install pip?](https://pip.pypa.io/en/stable/installing/#do-i-need-to-install-pip))
   * `pip -V`
* Install it if it's not installed
   * Does this install to OS or pyenv?
   * `curl -O https://bootstrap.pypa.io/get-pip.py`
   * `./get-pip.py`
* upgrade pip (only for your current Python activated by `pyenv`)
	* `pip install -U pip`
* install `virtualenv`
	* `pip install virtualenv`
	* Is this the right way to do it?
	   * Yes, because you will need `virtualenv` installed relative to the Python version you want copied into the virtual environment. Once you `activate` a virtual environment, that version of Python effectively becomes the only visible version in the current shell session. It will override `pyenv` and any `.python_version` settings. The shell will remain configured as such until `deactivate` is called, or the shell session is terminated.
	* Do I do this every time another Python version is installed?
	   * No, you do it everytime you want to cordon off a Python development project and environment based on a Python version.	* You are ready to create virtual environments that use that particular version of Python





## The "Not So Sure About These" Section
##### Everything in this section is from previous attempts and is dated. But it's still interesting information to some degree, and spurs the memory

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
   * no really, why make a pipsi install relative to a pyenv?
   * since `pipsi` is a system level utility, shouldn't `pipsi` install `pipsi`?


## Change python version with pyenv
* show python versions available for installation
   * `pyenv install -l`
* install a python
   * `pyenv install XYZ`
* Change to that python in your current directory
   * `pyenv local XYZ`


## Installation Docs
* [pyenv](https://github.com/pyenv/pyenv-installer#installation--update--uninstallation)
* [pip](https://virtualenv.pypa.io/en/stable/installation/)
* [virtualenv](https://virtualenv.pypa.io/en/stable/installation/)
* [virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/install.html#basic-installation)
* [pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv#installing-as-a-pyenv-plugin)
* [pyenv-virtualenvwrapper](https://github.com/pyenv/pyenv-virtualenvwrapper#installing-pyenv-virtualenvwrapper-as-a-pyenv-plugin)
* [pipenv](https://pipenv.readthedocs.io/en/latest/install/)

## Links
* [Python Virtual Environments: A Primer](https://realpython.com/python-virtual-environments-a-primer/)
* [What is the difference between venv, pyvenv, pyenv, virtualenv, virtualenvwrapper, pipenv, etc?](https://stackoverflow.com/questions/41573587/what-is-the-difference-between-venv-pyvenv-pyenv-virtualenv-virtualenvwrappe)
* [Installing Python Modules](https://docs.python.org/3/installing/index.html)
* [The definitive guide to setup my Python workspace](https://medium.com/@henriquebastos/the-definitive-guide-to-setup-my-python-workspace-628d68552e14)
* [Understanding Virtual Environments in Python](https://code.tutsplus.com/tutorials/understanding-virtual-environments-in-python--cms-28272)
* [`pip` user installs](https://pip.pypa.io/en/stable/user_guide/#user-installs)
* [Change Python Version in an Existing Virtualenv]()
* [wronk/python\_environment\_setup.md](https://gist.github.com/wronk/a902185f5f8ed018263d828e1027009b)
* [`poetry`](https://poetry.eustace.io/)

## Links (other than dev env related)
* [How do I find the location of my Python site-packages directory?](https://stackoverflow.com/questions/122327/how-do-i-find-the-location-of-my-python-site-packages-directory)
* [Processes In Containers Should Not Run As Root](https://medium.com/@mccode/processes-in-containers-should-not-run-as-root-2feae3f0df3b)

## Links (older)
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
