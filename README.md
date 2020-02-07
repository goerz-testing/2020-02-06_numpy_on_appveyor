[![Build status](https://ci.appveyor.com/api/projects/status/12mycbqivjoltgdo?svg=true)](https://ci.appveyor.com/project/goerz/2020-02-06-numpy-on-appveyor)

This repository illustrates a problem with the numpy conda package when run inside a tox environment (using [tox-conda](https://github.com/tox-dev/tox-conda) on Windows):

Trying to import numpy, e.g. with

~~~
python.exe -c "import numpy; print(numpy.__version__)"
~~~

results in an error such as
~~~
Traceback (most recent call last):
  File "C:\projects\2020-02-06-numpy-on-appveyor\.tox\envdir\lib\site-packages\numpy\core\__init__.py", line 24, in <module>
    from . import multiarray
  File "C:\projects\2020-02-06-numpy-on-appveyor\.tox\envdir\lib\site-packages\numpy\core\multiarray.py", line 14, in <module>
    from . import overrides
  File "C:\projects\2020-02-06-numpy-on-appveyor\.tox\envdir\lib\site-packages\numpy\core\overrides.py", line 7, in <module>
    from numpy.core._multiarray_umath import (
ImportError: DLL load failed while importing _multiarray_umath: The specified module could not be found.

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "<string>", line 1, in <module>
  File "C:\projects\2020-02-06-numpy-on-appveyor\.tox\envdir\lib\site-packages\numpy\__init__.py", line 142, in <module>
    from . import core
  File "C:\projects\2020-02-06-numpy-on-appveyor\.tox\envdir\lib\site-packages\numpy\core\__init__.py", line 54, in <module>
    raise ImportError(msg)
ImportError:

IMPORTANT: PLEASE READ THIS FOR ADVICE ON HOW TO SOLVE THIS ISSUE!

Importing the numpy c-extensions failed.
- Try uninstalling and reinstalling numpy.
- If you have already done that, then:
  1. Check that you expected to use Python3.8 from "C:\projects\2020-02-06-numpy-on-appveyor\.tox\envdir\python.exe",
     and that you have no directories in your PATH or PYTHONPATH that can
     interfere with the Python and numpy version "1.18.1" you're trying to use.
  2. If (1) looks fine, you can open a new issue at
     https://github.com/numpy/numpy/issues.  Please include details on:
     - how you installed Python
     - how you installed numpy
     - your operating system
     - whether or not you have multiple versions of Python installed
     - if you built from source, your compiler versions and ideally a build log

- If you're working with a numpy git repository, try `git clean -xdf`
  (removes all files not under version control) and rebuild numpy.

Note: this error has many possible causes, so please don't comment on
an existing issue about this - open a new one instead.

Original error was: DLL load failed while importing _multiarray_umath: The specified module could not be found.

ERROR: InvocationError for command 'C:\projects\2020-02-06-numpy-on-appveyor\.tox\envdir\python.exe' -c 'import numpy; print(numpy.__version__)' (exited with code 1)
~~~

The error occurs for Python versions 3.7 and 3.8, but not 3.6. It also only occurs when installing the numpy package inside of tox (as opposed to the default conda environment)

The repository reproduces with error on [AppVeyor](https://ci.appveyor.com/project/goerz/2020-02-06-numpy-on-appveyor), but I've observed the same behavior "manually" on a standard Windows 10 installation.

* `master` branch: Python 3.8, numpy from conda-forge channel ❌ ([AppVeyor log](https://ci.appveyor.com/project/goerz/2020-02-06-numpy-on-appveyor/builds/30633830))
* `37` branch: [Python 3.7, numpy from conda-forge channel](https://github.com/goerz-testing/2020-02-06_numpy_on_appveyor/commit/25cb334e4efeb0beb7e7f015ae6be1a9eb79ca55) ❌ ([AppVeyor log](https://ci.appveyor.com/project/goerz/2020-02-06-numpy-on-appveyor/builds/30633901))
* `defaultchannel` branch: [Python 3.8, numpy from default channel](https://github.com/goerz-testing/2020-02-06_numpy_on_appveyor/commit/c6e99d1791843e61e53f9e6700b60d2d363a5c98) ❌ ([AppVeyor log](https://ci.appveyor.com/project/goerz/2020-02-06-numpy-on-appveyor/builds/30633927))  → Problem isn't just with the conda-forge numpy package
* `systemtox` branch: [Python 3.8, numpy from conda-forge channel, use system-tox instead of installing tox into conda](https://github.com/goerz-testing/2020-02-06_numpy_on_appveyor/commit/b30e21f25b594c126beb338f74fe026d82ee8f45) ❌ ([AppVeyor log](https://ci.appveyor.com/project/goerz/2020-02-06-numpy-on-appveyor/builds/30634194)) → Problem isn't with the tox installed via `conda install tox`)
* `36` branch: [Python 3.6, numpy from conda-forge channe](https://github.com/goerz-testing/2020-02-06_numpy_on_appveyor/commit/916e9b780672152a40fbdb33e26a7b37a1b49278) ✅ ([AppVeyor log](https://ci.appveyor.com/project/goerz/2020-02-06-numpy-on-appveyor/builds/30654430))
* `notox` branch: [Python 3.8, numpy from conda-forge channel, install into system-conda(no tox)](https://github.com/goerz-testing/2020-02-06_numpy_on_appveyor/commit/0e42fe12724321b1da4379a1d9244ff006ca2075) ✅ ([AppVeyor log](https://ci.appveyor.com/project/goerz/2020-02-06-numpy-on-appveyor/builds/30633961))
