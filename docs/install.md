Installation
============

### MultiScanner ###
If you're running on a RedHat or Debian based linux distribution you should try and run
[install.sh](<install.sh>). Otherwise the required python packages are defined in
[requirements.txt](<requirements.txt>).

MultiScanner must have a configuration file to run. Generate the MultiScanner default
configuration by running `python multiscanner.py init` after cloning the repository.
This command can be used to rewrite the configuration file to its default state or,
if new modules have been written, to add their configuration to the configuration
file.

### Analytic Machine ###
Default modules have the option to be run locally or via SSH. The development team
runs MultiScanner on a Linux host and hosts the majority of analytical tools on
a separate Windows machine. The SSH server used in this environment is freeSSHd
from <http://www.freesshd.com/>. 

A network share accessible to both the MultiScanner and the Analytic Machines is
required for the multi-machine setup. Once configured, the network share path must
be identified in the configuration file, config.ini. To do this, set the `copyfilesto`
option under `[main]` to be the mount point on the system running MultiScanner.
Modules can have a `replacement path` option, which is the network share mount point
on the analytic machine.