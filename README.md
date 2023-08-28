# QtPy: Abstraction layer for PyQt5/PySide2/PyQt6/PySide6

[![license](https://img.shields.io/pypi/l/qtpy.svg)](./LICENSE)
[![pypi version](https://img.shields.io/pypi/v/qtpy.svg)](https://pypi.org/project/QtPy/)
[![conda version](https://img.shields.io/conda/vn/conda-forge/qtpy.svg)](https://www.anaconda.com/download/)
[![download count](https://img.shields.io/conda/dn/conda-forge/qtpy.svg)](https://www.anaconda.com/download/)
[![OpenCollective Backers](https://opencollective.com/spyder/backers/badge.svg?color=blue)](#backers)
[![Join the chat at https://gitter.im/spyder-ide/public](https://badges.gitter.im/spyder-ide/spyder.svg)](https://gitter.im/spyder-ide/public)<br>
[![PyPI status](https://img.shields.io/pypi/status/qtpy.svg)](https://github.com/spyder-ide/qtpy)
[![Github build status](https://github.com/spyder-ide/qtpy/workflows/Tests/badge.svg)](https://github.com/spyder-ide/qtpy/actions)
[![Coverage Status](https://coveralls.io/repos/github/spyder-ide/qtpy/badge.svg?branch=master)](https://coveralls.io/github/spyder-ide/qtpy?branch=master)

*Copyright © 2009– The Spyder Development Team*


## Description

**QtPy** is a small abstraction layer that lets you
write applications using a single API call to either PyQt or PySide.

It provides support for PyQt5, PySide2, PyQt6 and PySide6 using the Qt5 layout
(where the QtGui module has been split into QtGui and QtWidgets).

Basically, you can write your code as if you were using PyQt or PySide directly,
but import Qt modules from `qtpy` instead of `PyQt5`, `PySide2`, `PyQt6` or `PySide6`.

Accordingly, when porting code between different Qt bindings (PyQt vs PySide) or Qt versions (Qt5 vs Qt6), QtPy makes this much more painless, and allows you to easily and incrementally transition between them. QtPy handles incompatibilities and differences between bindings or Qt versions for you while keeping your project running, so you can focus more on your own code and less on keeping track of supporting every Qt version and binding. Furthermore, when you do want to upgrade or support new bindings, it allows you to update your project module by module rather than all at once.  You can check out examples of this approach in projects using QtPy, like [git-cola](https://github.com/git-cola/git-cola/issues/232).

### Attribution and acknowledgments

This project is based on the [pyqode.qt](https://github.com/pyQode/pyqode.qt)
project and the [spyderlib.qt](https://github.com/spyder-ide/spyder/tree/2.3/spyderlib/qt)
module from the [Spyder](https://github.com/spyder-ide/spyder) project, and
also includes contributions adapted from
[qt-helpers](https://github.com/glue-viz/qt-helpers), developed as part of the
[glue](http://glueviz.org) project.

Unlike `pyqode.qt` this is not a namespace package, so it is not tied
to a particular project or namespace.


### License

This project is released under the [MIT license](LICENSE.txt).


### Requirements

You need PyQt5, PySide2, PyQt6 or PySide6 installed in your system to make use
of QtPy. If several of these packages are found, PyQt5 is used by
default unless you set the `QT_API` environment variable.

`QT_API` can take the following values:

* `pyqt5` (to use PyQt5).
* `pyside2` (to use PySide2).
* `pyqt6` (to use PyQt6).
* `pyside6` (to use PySide6).


### Module aliases and constants

* `QtCore.pyqtSignal`, `QtCore.pyqtSlot` and `QtCore.pyqtProperty` (available on PyQt5/6) are instead exposed as `QtCore.Signal`, `QtCore.Slot` and `QtCore.Property`, respectively, following the Qt5 module layout.

* The Qt version being used can be checked with `QtCore.__version__` (instead of `QtCore.QT_VERSION_STR`) as well as from `qtpy.QT_VERSION`.

* For PyQt6 enums, unscoped enum access was added by promoting the enums of the `QtCore`, `QtGui`, `QtTest` and `QtWidgets` modules.

* Compatibility is added between the `QtGui` and `QtOpenGL` modules for the `QOpenGL*` classes.

* To check the current binding version, you can use `qtpy.PYSIDE_VERSION` for PySide2/6 and `qtpy.PYQT_VERSION` for PyQt5/6. If the respective binding is not being used, the value of its attribute will be `None`.

* To check the current selected binding, you can use `qtpy.API_NAME`

* There are boolean values to check if Qt5/6, PyQt5/6 or PySide2/6 are being used: `qtpy.QT5`, `qtpy.QT6`, `qtpy.PYQT5`, `qtpy.PYQT6`, `qtpy.PYSIDE2` and `qtpy.PYSIDE6`. `True` if currently being used, `False` otherwise.

#### Compat module

In the `qtpy.compat` module, you can find wrappers for `QFileDialog` static methods and SIP/Shiboken functions, such as:

* `QFileDialog.getExistingDirectory` wrapped with `qtpy.compat.getexistingdirectory`

* `QFileDialog.getOpenFileName` wrapped with `qtpy.compat.getopenfilename`

* `QFileDialog.getOpenFileNames` wrapped with `qtpy.compat.getopenfilenames`

* `QFileDialog.getSaveFileName` wrapped with `qtpy.compat.getsavefilename`

* `sip.isdeleted` and `shiboken.isValid` wrapped with `qtpy.compat.isalive`


### Installation

```bash
pip install qtpy
```

or

```bash
conda install qtpy
```


### Type checker integration

Type checkers have no knowledge of installed packages, so these tools require
additional configuration.

A Command Line Interface (CLI) is offered to help with usage of QtPy (to get MyPy
and Pyright/Pylance args/configurations).

#### Mypy

The `mypy-args` command helps you to generate command line arguments for Mypy
that will enable it to process the QtPy source files with the same API
as QtPy itself would have selected.

If you run

```bash
qtpy mypy-args
```

QtPy will output a string of Mypy CLI args that will reflect the currently
selected Qt API.
For example, in an environment where PyQt5 is installed and selected
(or the default fallback, if no binding can be found in the environment),
this would output the following:

```text
--always-true=PYQT5 --always-false=PYSIDE2 --always-false=PYQT6 --always-false=PYSIDE6
```

Using Bash or a similar shell, this can be injected into
the Mypy command line invocation as follows:

```bash
mypy --package mypackage $(qtpy mypy-args)
```

#### Pyright/Pylance

In the case of Pyright, instead of runtime arguments, it is required to create a
config file for the project, called `pyrightconfig.json` or a `pyright` section
in `pyproject.toml`. See [here](https://github.com/microsoft/pyright/blob/main/docs/configuration.md)
for reference. In order to set this configuration, QtPy offers the `pyright-config`
command for guidance.

If you run

```bash
qtpy pyright-config
```

you will get the necessary configs to be included in your project files. If you don't
have them, it is recommended to create the latter. For example, in an environment where PyQt5
is installed and selected (or the default fallback, if no binding can be found in the
environment), this would output the following:

```text
pyrightconfig.json:
{"defineConstant": {"PYQT5": true, "PYSIDE2": false, "PYQT6": false, "PYSIDE6": false}}

pyproject.toml:
[tool.pyright.defineConstant]
PYQT5 = true
PYSIDE2 = false
PYQT6 = false
PYSIDE6 = false
```

**Note**: These configurations are necessary for the correct usage of the default VSCode's type
checking feature while using QtPy in your source code.


## Testing matrix

Currently, QtPy runs tests for different bindings on Linux, Windows and macOS, using
Python 3.7 and 3.11, and installing those bindings with `conda` and `pip`. For the
PyQt bindings, we also check the installation of extra packages via `pip`.

Following this, the current test matrix looks something like this:

|         | Python          | 3.7                                        |      | 3.11               |                            |
|---------|-----------------|--------------------------------------------|------|--------------------|----------------------------|
| OS      | Binding / manager | conda                                      | pip  | conda              | pip                        |
| Linux   | PyQt5           | 5.12                                       | 5.15 | 5.15               | 5.15 (with extras)         |
|         | PyQt6           | skip (unavailable)                         | 6.3  | skip (unavailable) | 6.5 (with extras)          |
|         | PySide2         | 5.13                                       | 5.12 | 5.15               | skip (no wheels available) |
|         | PySide6         | 6.4                                        | 6.3  | 6.5                | 6.5                        |
| Windows | PyQt5           | 5.9                                        | 5.15 | 5.15               | 5.15 (with extras)         |
|         | PyQt6           | skip (unavailable)                         | 6.2  | skip (unavailable) | 6.5 (with extras)          |
|         | PySide2         | 5.13                                       | 5.12 | 5.15               | skip (no wheels available) |
|         | PySide6         | skip (test hang with 6.4. 6.5 unavailable) | 6.2  | 6.5                | 6.5                        |
| MacOS   | PyQt5           | 5.12                                       | 5.15 | 5.15               | 5.15 (with extras)         |
|         | PyQt6           | skip (unavailable)                         | 6.3  | skip (unavailable) | 6.5 (with extras)          |
|         | PySide2         | 5.13                                       | 5.12 | 5.15               | skip (no wheels available) |
|         | PySide6         | 6.4                                        | 6.3  | 6.5                | 6.5                        |

**Note**: The mentioned extra packages for the PyQt bindings are the following:

* `PyQt3D` and `PyQt6-3D`
* `PyQtChart` and `PyQt6-Charts`
* `PyQtDataVisualization` and `PyQt6-DataVisualization`
* `PyQtNetworkAuth` and `PyQt6-NetworkAuth`
* `PyQtPurchasing`
* `PyQtWebEngine` and `PyQt6-WebEngine` 
* `QScintilla` and `PyQt6-QScintilla`

## Contributing

Everyone is welcome to contribute! See our [Contributing guide](CONTRIBUTING.md) for more details.


## Sponsors

QtPy is funded thanks to the generous support of


[![Quansight](https://user-images.githubusercontent.com/16781833/142477716-53152d43-99a0-470c-a70b-c04bbfa97dd4.png)](https://www.quansight.com/)[![Numfocus](https://i2.wp.com/numfocus.org/wp-content/uploads/2017/07/NumFocus_LRG.png?fit=320%2C148&ssl=1)](https://numfocus.org/)

and the donations we have received from our users around the world through [Open Collective](https://opencollective.com/spyder/):

[![Sponsors](https://opencollective.com/spyder/sponsors.svg)](https://opencollective.com/spyder#support)
