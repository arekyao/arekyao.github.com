---
layout: post
title: "Something About Python"
date: 2013-02-17 12:46
comments: true
categories: Tools
---

First, let me introduce something about develop environment, such as Brew, easy_install,RVM, ... etc. 



Mac Develop Environment
======


[Homebrew: The missing package manager for OS X](http://mxcl.github.com/homebrew/)
------------

```
$ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"
```

with brew, you can install anything in command. eg:

```
$brew install lighttpd
```


[Easy_install](http://pypi.python.org/pypi/setuptools/)
----------
> Download the appropriate egg for your version of Python (e.g. setuptools-0.6c9-py2.4.egg). Do NOT rename it.
> Run it as if it were a shell script, e.g. sh setuptools-0.6c9-py2.4.egg. 
> Setuptools will install itself using the matching version of Python (e.g. python2.4), and will place the easy_install executable in the default location for installing Python scripts (as determined by the standard distutils configuration files, or by the Python installation).
> 

with this, you can install anything abou python package in command. eg:

```
easy_install xxx
```

BTW: 
You can check package info in "/Library/Python/2.7/site-packages"

[RVM: Ruby Version Manager](https://rvm.io/)
---------
```
$ \curl -L https://get.rvm.io | bash -s stable --ruby
```




------------------------------








Common Python Library
=====




1, NLP:
------
nltk:  [http://nltk.org/](http://nltk.org/)

chinese word segment [https://github.com/fxsjy/jieba](https://github.com/fxsjy/jieba)


2, Machine Learning
----------
scikit_learn: [https://pypi.python.org/pypi/scikit-learn/](https://pypi.python.org/pypi/scikit-learn/)

However,ML's scikit-learn lib have to depend on lots of other package(scipy....)
Here is a good scipt for mac 10.8 user
[http://fonnesbeck.github.com/ScipySuperpack/](http://fonnesbeck.github.com/ScipySuperpack/)

[the scipt code](https://raw.github.com/fonnesbeck/ScipySuperpack/master/install_superpack.sh) :

```
#!/bin/sh
PYTHON='/usr/bin/python'
GIT_FILENAME='git-1.7.7.3-intel-universal-snow-leopard'
GIT_VOLUME='/Volumes/Git 1.7.7.3 Snow Leopard Intel Universal/'
GFORTRAN='gcc-42-5666.3-darwin11.pkg'
SUDO='sudo'

if [ -z "$VIRTUAL_ENV" ]; then
    # Standard Python env
    PYTHON=/usr/bin/python
    SUDO=${SUDO}
else
    # Virtualenv
    PYTHON=python
    SUDO="" #${SUDO} is not required in a virtualenv
fi

if  [ -d ".git" ]; then
    
    SUPERPACK_PATH='.'
    
else
    
    SUPERPACK_PATH='ScipySuperpack'
    
    hash git &> /dev/null
    if [ $? -eq 1 ]; then
        echo 'Downloading Git for OS X ...'
        curl -o ${GIT_FILENAME}.dmg http://git-osx-installer.googlecode.com/files/${GIT_FILENAME}.dmg
        echo 'Installing Git ...'
        hdiutil mount ${GIT_FILENAME}.dmg
        ${SUDO} installer -pkg "${GIT_VOLUME}${GIT_FILENAME}.pkg" -target '/'
        hdiutil unmount "${GIT_VOLUME}"
        echo 'Cleaning up'
        rm ${GIT_FILENAME}.dmg
        echo 'Cloning Scipy Superpack'
        /usr/local/git/bin/git clone --depth=1 git://github.com/fonnesbeck/ScipySuperpack.git
    else
        echo 'Cloning Scipy Superpack'
        git clone --depth=1 git://github.com/fonnesbeck/ScipySuperpack.git
    fi
fi

# hash gfortran &> /dev/null
# if [ $? -eq 1 ]; then
echo 'Downloading gFortran ...'
curl -o ${GFORTRAN} http://r.research.att.com/tools/${GFORTRAN}
echo 'Installing gFortran ...'
${SUDO} installer -pkg ${GFORTRAN} -target '/'
# fi

hash easy_install &> /dev/null
if [ $? -eq 1 ]; then
    echo 'Downloading ez_setup ...'
    curl -o ez_setup.py http://peak.telecommunity.com/dist/ez_setup.py
    echo 'Installing ez_setup ...'
    ${SUDO} "${PYTHON}" ez_setup.py
    rm ez_setup.py
fi

echo 'Installing Scipy Superpack ...'
${SUDO} "${PYTHON}" -m easy_install -N -Z ${SUPERPACK_PATH}/*.egg

echo 'Installing readline ...'
${SUDO} "${PYTHON}" -m easy_install -N -Z readline
echo 'Installing nose ...'
${SUDO} "${PYTHON}" -m easy_install -N -Z nose
echo 'Installing six'
${SUDO} "${PYTHON}" -m easy_install -N -Z six
echo 'Installing python-dateutil'
${SUDO} "${PYTHON}" -m easy_install -N -Z python-dateutil
echo 'Installing pytz'
${SUDO} "${PYTHON}" -m easy_install -N -Z pytz
echo 'Installing Tornado'
${SUDO} "${PYTHON}" -m easy_install -N -Z tornado
echo 'Installing pyzmq'
${SUDO} "${PYTHON}" -m easy_install -N -Z pyzmq
echo 'Installing pika'
${SUDO} "${PYTHON}" -m easy_install -N -Z pika
echo 'Installing jinja2'
${SUDO} "${PYTHON}" -m easy_install -N -Z jinja2
echo 'Installing patsy'
${SUDO} "${PYTHON}" -m easy_install -N -Z patsy
if  [ ! -d ".git" ]; then
    echo 'Cleaning up'  
    rm -rf ${SUPERPACK_PATH}
fi

echo 'Done'

```


3, [Scrapy](http://scrapy.org/download/)
-------

A Spider Structure based on Python

4, [IPython](http://ipython.org/install.html)
------
a good Python IDE

5, [Web Framework](http://wiki.python.org/moin/WebFrameworks)
-------

[Web.py](http://www.douban.com/group/web.py/)

[Django](http://www.douban.com/group/django/)

http://www.douban.com/group/django/


6, Python study
--------

[http://gnosis.cx/TPiP/]()

[http://docs.python-guide.org/en/latest/]()

[More...](http://www.python.org/about/apps/)
------

Reference
--------

[http://www.cnblogs.com/vamei/archive/2013/02/06/2892628.html]()
[http://www.python.org/about/apps/]()
[http://docs.python-guide.org/en/latest/]()
