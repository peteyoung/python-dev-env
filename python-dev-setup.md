#Python Dev Env

##Links
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

##Setup pip, virtualenv, pipenv, pyenv, pipsi

* make a work dir
	* `mkdir python-support && cd $_`
* install pip
	* `curl -O https://bootstrap.pypa.io/get-pip.py`
	* `sudo python get-pip.py`
* upgrade pip
	* `pip install -U pip`
* install virtualenv
	* `sudo pip install virtualenv`
* install pyenv
	* `brew install pyenv`
* install pipsi
	* `python get-pipsi.py`
	* `vim ~/.bashrc`
		* add `export PATH=/Users/peteyoung/.local/bin:$PATH`
	* `. ~/.bashrc`
* Fancy Installation of Pipenv
	* `pipsi install pew`
	* `pipsi install pipenv`
	 

##Setup pip, virtualenv, pipenv, pyenv, pipsi (second try)

* One time only
    * install pyenv (use homebrew on Mac) 
    * put the following in .bashrc or .bash_local
    * 
```
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
```

    * Install python versions with pyenv
	    * `pyenv install 2.7.14`
    * Install/upgrade pip and pipsi with pyenv's pip
	    * `$(pyenv root)/shims/pip install -U pip pipsi`


##Change python version with pyenv
