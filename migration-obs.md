# python-otr notes

[TOC]

## Python 2
> Python version? 2.7?

### Installing

#### Debian requirements

* Requirements
    * `libmpfr-dev`
    * `libmpc-dev`
    * `libmpc-devexit`

To install debian requirements: 
`sudo apt install libmpfr-dev libmpc-dev libmpc-dev`

### Issues

#### python is not installing dependencies listed in install_requires of setuptools

Output when `python setup.py install` runs:

```shell=bash
running install
running build
running build_py
running install_lib
creating /home/bibo/miniconda3/envs/py2-otr/lib/python2.7/site-packages/otr
copying build/lib/otr/protocol.py -> /home/bibo/miniconda3/envs/py2-otr/lib/python2.7/site-packages/otr
copying build/lib/otr/cryptography.py -> /home/bibo/miniconda3/envs/py2-otr/lib/python2.7/site-packages/otr
copying build/lib/otr/__init__.py -> /home/bibo/miniconda3/envs/py2-otr/lib/python2.7/site-packages/otr
copying build/lib/otr/exceptions.py -> /home/bibo/miniconda3/envs/py2-otr/lib/python2.7/site-packages/otr
copying build/lib/otr/__info__.py -> /home/bibo/miniconda3/envs/py2-otr/lib/python2.7/site-packages/otr
copying build/lib/otr/util.py -> /home/bibo/miniconda3/envs/py2-otr/lib/python2.7/site-packages/otr
byte-compiling /home/bibo/miniconda3/envs/py2-otr/lib/python2.7/site-packages/otr/protocol.py to protocol.pyc
byte-compiling /home/bibo/miniconda3/envs/py2-otr/lib/python2.7/site-packages/otr/cryptography.py to cryptography.pyc
byte-compiling /home/bibo/miniconda3/envs/py2-otr/lib/python2.7/site-packages/otr/__init__.py to __init__.pyc
byte-compiling /home/bibo/miniconda3/envs/py2-otr/lib/python2.7/site-packages/otr/exceptions.py to exceptions.pyc
byte-compiling /home/bibo/miniconda3/envs/py2-otr/lib/python2.7/site-packages/otr/__info__.py to __info__.pyc
byte-compiling /home/bibo/miniconda3/envs/py2-otr/lib/python2.7/site-packages/otr/util.py to util.pyc
running install_egg_info
Writing /home/bibo/miniconda3/envs/py2-otr/lib/python2.7/site-packages/python_otr-1.2.1-py2.7.egg-info
``` 

`pip list` output after installation: 

```
Package    Version            
---------- -------------------
certifi    2019.11.28         
pip        19.3.1             
python-otr 1.2.1              
setuptools 44.0.0.post20200106
wheel      0.33.6  
```

##### Install dependencies from file

* requirements.txt content: 

```
python_application>=2.0.0
cryptography>=1.6
enum34
gmpy2
zope.interface
``` 

`pip install -r requirements.txt` 

##### Solve the issue

In `setup.py` file change line 4: 

`from distutils.core import setup, Distribution` 

with 

```shell=python
from distutils.core import Distribution
from setuptools import setup
``` 

## 2-to-3 migration

*in branch `python3`*

* **Automated Python 2 to 3 code translation** using `2to3` lib.

```shell=python
pip install 2to3
2to3 -w .
```

### Fix setup.py

* **(?) `from distutils.core import setup` or `from setuptools import setup`**
* **(?) Add** `requirements.txt` file.
    * **Fix** the order according to:
        * Requirements without Version Specifiers
        * Requirements with Version Specifiers
    > Reference: [installation order.](https://pip.pypa.io/en/latest/reference/pip_install/#installation-order)
* **Add** `requirements()` function: 
```shell=python
def requirements():
    install_requires = []
    with open('requirements.txt') as f:
        for line in f:
            install_requires.append(line.strip())
    return install_requires
```

* **Delete** lines from 21 to 27.

```python
requirements = [
    'python_application (>=2.0.0)',
    'cryptography (>=1.6)',
    'enum34',
    'gmpy2',
    'zope.interface'
]
```

* **Replace** lines 54 and 55: 
```shell=python
requires=requirements,
install_requires=[requirement.translate(None, ' ()') for requirement in requirements]
)
``` 
with: 
`install_requires=requirements()`

### Fix str vs bytes conflicts

* Python 3
    * `'b` + str
    *  `str.encode(''.join(str(x) for x in serie))`

### Test

* Use [snoop](https://pypi.org/project/snoop/) as debugging tool.

### Bugs

Localpoint attributes were set with `None` protocol and thus a PlainText state.

Remotepoint attributes were set with `None` protocol and thus a PlainText state.

Localpoint attributes were set with a wrong smp state.