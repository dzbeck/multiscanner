MultiScanner
==============
MultiScanner is a file analysis framework that assists the user in evaluating a set
of files by automatically running a suite of tools for the user and aggregating the output.
Tools can be custom built python scripts, web APIs, software running on another machine, etc.
Tools are incorporated by creating modules that run in the MultiScanner framework.

Modules are designed to be quickly written and easily incorporated into the framework.
Currently written and maintained modules are related to malware analytics, but the framework is not limited to that
scope. For a list of modules you can look in [modules](modules), descriptions and config
options can be found in [docs/modules.md](docs/modules.md)

Requirements
------------
Python 3.6 is recommended. Compatibility with 2.7+ and
3.4+ is supported but not as thoroughly maintained and tested. Please submit an issue
or a pull request fixing any issues found with other versions of Python.


An installer script is included in the project [install.sh](<install.sh>), which
installs the prerequisites on most systems.

![architecture](img/arch1.png)

Python API
----------
MultiScanner can be incorporated as a module in another projects. Below is a simple
example of how to import MultiScanner into a Python script.

``` python
import multiscanner
output = multiscanner.multiscan(FileList)
Results = multiscanner.parse_reports(output, python=True)
```

Results is a dictionary object where each key is a filename of a scanned file.

`multiscanner.config_init(filepath)` will create a default configuration file at
the location defined by filepath.