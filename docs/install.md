Installation
============
System Requirements
-------------------
Python 3.6 is recommended. Compatibility with 2.7+ and 3.4+ is supported but not as thoroughly maintained and tested. Please submit an issue or a pull request fixing any issues found with other versions of Python.

An installer script is included in the project [install.sh](https://github.com/mitre/multiscanner/blob/master/install.sh), which
installs the prerequisites on most systems.

## Installing Ansible ##
If you're running on a RedHat or Debian based linux distribution, try and run
[install.sh](<install.sh>). Otherwise the required python packages are defined in
[requirements.txt](<requirements.txt>).

MultiScanner must have a configuration file to run. Generate the MultiScanner default
configuration by running `python multiscanner.py init` after cloning the repository.
This command can be used to rewrite the configuration file to its default state or,
if new modules have been written, to add their configuration to the configuration
file.

## Installing Analytic Machines ##
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

Installing Elasticsearch
------------------------
Starting with ElasticSearch 2.X, field names may no longer contain '.' (dot) characters. Thus, the elasticsearch_storage module adds a pipeline called 'dedot' with a processor to replace dots in field names with underscores.

Add the following to your elasticsearch.yml config for the dedot processor to work:
`script.painless.regex.enabled: true`
If planning to use the Multiscanner web UI, also add the following:
`http.cors.enabled: true`
`http.cors.allow-origin: "<yourOrigin>"`

Module Configuration
--------------------
Modules are intended to be quickly written and incorporated into the framework.
A finished module must be placed in the modules folder before it can be used. The
configuration file does not need to be manually updated. See [docs/module\_writing.md](<docs/module_writing.md>)
for more information.

Modules are configured within the configuration file, config.ini. General parameters are shown in Table X. Module-specific parameters follow for those modules that have them. See Section 2.3xx for information about all default modules.

###General Module Parameters###

| Parameter | Description |
| --------- | ----------- |
| `path` | Location of the executable. |
| `cmdline` | An array of command line options to be passed to the executable. |
| `host` | The hostname, port, and username of the machine that will be SSH’d into to run the analytic if the executable is not present on the local machine.|
| `key` | The SSH key to be used to SSH into the host. |
| `replacement path` | If the main config is set to copy the scanned files this will be what it replaces the path with. It should be where the network share is mounted. |
| `ENABLED` | When set to false, the module will not run. |

### [main] ###
This module searches virustotal for the file hash and downloads the report, if available.

- **copyfilesto** - This is where the script will copy each file that is to be scanned. This can be removed or set to False to disable this feature
- **group-types** - This is the type of analytics to group into sections for the report. This can be removed or set to False to disable this feature

### [AVGScan] ###
This module scans a file with AVG 2014 anti-virus.

### [ClamAVScan] ###
This module scans a file with ClamAV.

### [Cuckoo] ###
This module submits a file to a Cuckoo Sandbox cluster for analysis

- **API URL** - This is the URL to the API server
- **timeout** - This is max time a sample with run for
- **running timeout** - This is an additional timeout, if a task is in the running state this many seconds past **timeout** we will consider the task failed.
- **delete tasks** - When set to True, tasks will be deleted from cuckoo after detonation. This is to prevent filling up the Cuckoo machine's disk with reports.
- **maec** - When set to True, [MAEC](https://maecproject.github.io) JSON report is added to Cuckoo JSON report. *NOTE*: Cuckoo needs MAEC reporting enabled to produce results.

### [VxStream] ###
This module submits a file to a VxStream Sandbox cluster for analysis

- **API URL** - This is the URL to the API server (include the /api/ in this URL)
- **API Key** - This is the user's API key to the API server
- **API Secret** - This is the user's secret to the API server
- **timeout** - This is max time a sample with run for
- **running timeout** - This is an additional timeout, if a task is in the running state this many seconds past **timeout** we will consider the task failed.

### [ExifToolsScan] ###
This module scans the file with Exif tools and returns the results.

- **remove-entry** - A python list of ExifTool results that should not be included in the report. File system level attributes are not useful and stripped out 

### [FireeyeScan] ###
This module uses a FireEye AX to scan the files. It uses the Malware Repository feature to automatically scan files. This may not be the best way but it does work. It will copy the files to be scanned to the mounted share folders.
*NOTE*: This module is suuuuuper slow

- **base path** - The mount point where the fireeye images folders are
- **src folder** - The folder name where input files are put
- **fireeye images** - A python list of the VMs in fireeye. These are used to generate where to copy the files.
- **enabled** - True or False
- **good path** - The folder name where good files are put
- **cheatsheet** - Not implemented yet

### [MD5] ###
This module generates the MD5 hash of the files.

### [McAfeeScan] ###
This module scans the files with McAfee AntiVirus Command Line.

### [PEFile] ###
This module extracts out feature information from EXE files. It uses [pefile](https://code.google.com/p/pefile/) which is currently not available for python 3.

### [SHA256] ###
This module generates the SHA256 hash of the files.

### [Tika] ###
This module extracts metadata from the file using [Tika](https://tika.apache.org/). For configuration of the module see the [tika-python](https://github.com/chrismattmann/tika-python/blob/master/README.md) documentation.

- **remove-entry** - A python list of Tika results that should not be included in the report.

### [TrID] ###
This module runs [TrID](http://mark0.net/soft-trid-e.html) against the files. The definition file should be in the same folder as the executable

### [YaraScan] ###
This module scans the files with yara and returns the results. You will need yara-python installed for this module.

- **ruledir** - The directory to look for rule files in
- **fileextensions** - A python array of all valid rule file extensions. Files not ending in one of these will be ignored.
- **ignore-tags** - A python array of yara rule tags that will not be included in the report.

### [libmagic] ###
This module runs libmagic against the files.

- **magicfile** - The path to the compiled magic file you wish to use. If None it will use the default one.

### [pdfinfo] ###
This module extracts out feature information from PDF files. It uses [pdf-parser](http://blog.didierstevens.com/programs/pdf-tools/)

### [ssdeeper] ###
This module generates context triggered piecewise hashes (CTPH) for the files. More information can be found on the [ssdeep website](http://ssdeep.sourceforge.net/).

### [vtsearch] ###
This module searches [virustotal](https://www.virustotal.com/) for the files hash and download the report if available.
- **apikey** - This is your public/private api key. You can optionally make it a list and the requests will be distributed across them. This is useful when two groups with private api keys want to share the load and reports