Installation
============

Installation information for the different components of MultiScanner is provided below. If you'd like to get an idea of how the system works without going through the full process of setting up the distributed architecture, refer to the `Standalone Docker Installation`_. 

The standalone system is less scalable, robust, and feature-rich, but it enables easy stand up the web UI, the REST API, and an ElasticSearch node, allowing users to quickly see how the system works. The standalone container is intended as an introduction to the system and its capabilities, but not designed for use in production.

System Requirements
-------------------

Python 3.6 is recommended. Compatibility with Python 2.7+ and 3.4+ is supported but not thoroughly maintained and tested. Please submit an issue or a pull request fixing any issues found with other versions of Python.

An installer script is included in the project (`install.sh <https://github.com/mitre/multiscanner/blob/feature-celery/install.sh>`_), which installs the prerequisites on most systems.

Currently, MultiScanner is deployed with Ansible. We are also currently working to support deploying the distributed architecture via Docker. 

Installing Ansible
------------------

If you're running on a RedHat or Debian based linux distribution, the `installer script <https://github.com/mitre/multiscanner/blob/feature-celery/install.sh>`_ should install required Python packages. Otherwise, refer to `requirements.txt <https://github.com/mitre/multiscanner/blob/feature-celery/requirements.txt>`_ for requirement details.

MultiScanner requires a configuration file to run. Generate the MultiScanner default
configuration by running ``python multiscanner.py init`` after cloning the repository.
This command can be used to rewrite the configuration file to its default state or,
if new modules have been written, to add their configuration to the configuration
file.

Installing Analytic Machines
----------------------------

Default modules have the option to be run locally or via SSH. The development team
runs MultiScanner on a Linux host and hosts the majority of analytical tools on
a separate Windows machine. The SSH server used in this environment is freeSSHd
from `<http://www.freesshd.com/>`_. 

A network share accessible to both the MultiScanner and the analytic machines is
required for the multi-machine setup. Once configured, the network share path must
be identified in the configuration file, config.ini. To do this, set the ``copyfilesto``
option under ``[main]`` to be the mount point on the system running MultiScanner.
Modules can have a ``replacement path`` option, which is the network share mount point
on the analytic machine.

Installing Elasticsearch
------------------------

Starting with ElasticSearch 2.X, field names may no longer contain '.' (dot) characters. Thus, the elasticsearch_storage module adds a pipeline called 'dedot' with a processor to replace dots in field names with underscores.

Add the following to your elasticsearch.yml config for the dedot processor to work::

    script.painless.regex.enabled: true


If planning to use the Multiscanner Web UI, also add the following::

    http.cors.enabled: true
    http.cors.allow-origin: <yourOrigin>

Module Configuration
--------------------

Modules are intended to be quickly written and incorporated into the framework.
A finished module must be placed in the modules folder before it can be used. The
configuration file does not need to be manually updated.

Modules are configured within the configuration file, `config.ini <https://github.com/mitre/multiscanner/blob/feature-celery/docker_utils/config.ini>`_. Parameters used by all modules are shown in the table below. Module-specific parameters follow for those modules that have them. See `Analysis Modules <use/use-analysis-mods.html>`_ for information about all existing modules.

Common Parameters
^^^^^^^^^^^^^^^^^

The parameters below may be used by all modules.

.. tabularcolumns:: |p{3cm}|p{12cm}|

====================  =============================
Parameter             Description
====================  =============================
**path**              Location of the executable.
**cmdline**           An array of command line options to be passed to the executable.
**host**              The hostname, port, and username of the machine that will be SSH’d into to run the analytic if the executable is not present on the local machine.
**key**               The SSH key to be used to SSH into the host.
**replacement path**  If the main config is set to copy the scanned files this will be what it replaces the path with. It should be where the network share is mounted. 
**ENABLED**           When set to false, the module will not run.
====================  =============================

Parameters of Core Modules
^^^^^^^^^^^^^^^^^^^^^^^^^^

**[main]**  

This module searches virustotal for a file hash and downloads the report, if available.

.. tabularcolumns:: |p{3cm}|p{12cm}|

====================  =============================
Parameter             Description
====================  =============================
**copyfilesto**       This is where the script will copy each file that is to be scanned. This can be removed or set to False to disable this feature.
**group-types**       This is the type of analytics to group into sections for the report. This can be removed or set to False to disable this feature.
**storage-config**    Path to the storage config file.
**api-config**        Path to the API config file.
**web-config**        Path to the Web UI config file.
====================  =============================

Parameters of Analysis Modules
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Analysis modules with additional parameters are given below in alphabetical order. See `Analysis Modules <use/use-analysis-mods.md>`_ for a list of all current analysis modules.

**[Cuckoo]**  

This module submits a file to a Cuckoo Sandbox cluster for analysis

.. tabularcolumns:: |p{3cm}|p{12cm}|

====================  =============================
Parameter             Description
====================  =============================
**API URL**           The URL to the API server.
**WEB URL**           The URL to the Web server.
**timeout**           The maximum time a sample will run.
**running timeout**   An additional timeout, if a task is in the running state this many seconds past **timeout**, the task is considered failed.
**delete tasks**      When set to True, tasks will be deleted from Cuckoo after detonation. This is to prevent filling up the Cuckoo machine's disk with reports.
**maec**              When set to True, a `MAEC <https://maecproject.github.io>`_ JSON-based report is added to Cuckoo JSON report. *NOTE*: Cuckoo needs MAEC reporting enabled to produce results.
====================  =============================

**[ExifToolsScan]**

This module scans the file with Exif tools and returns the results.

.. tabularcolumns:: |p{3cm}|p{12cm}|

====================  =============================
Parameter             Description
====================  =============================
**remove-entry**      A python list of ExifTool results that should not be included in the report. File system level attributes are not useful and stripped out.
====================  =============================

**[FireeyeAPI]**  

Detonates the sample in FireEye AX via FireEye's API. This "API" version replaces the "FireEye Scan" module.

.. tabularcolumns:: |p{3cm}|p{12cm}|

====================  =============================
Parameter             Description
====================  =============================
**API URL**           The URL to the API server.
**fireeye images**    A python list of the VMs in fireeye. These are used to generate where to copy the files.
**username**          Username on the FireEye AX. 
**password**          Password for the FireEye AX.
**info level**        Options are concise, normal, and extended.
**timeout**           The maximum time a sample will run.
**force**             If set to True, will rescan if the sample matches a previous scan.
**analysis type**     0 = sandbox, 1 = live.
**application id**    For AX Series appliances (7.7 and higher) and CM Series appliances that manage AX Series appliances (7.7 and higher), setting the application value to -1 allows the AX Series appliance to choose the application. For other appliances, setting the application value to 0 allows the AX Series appliance to choose the application.
====================  =============================

**[libmagic]**  

This module runs libmagic against the files.

.. tabularcolumns:: |p{3cm}|p{12cm}|

====================  =============================
Parameter             Description
====================  =============================
**magicfile**         The path to the compiled magic file you wish to use. If None it will use the default one.
====================  =============================

**[Metadefender]**  

This module runs Metadefender against the files.

.. tabularcolumns:: |p{3cm}|p{12cm}|

====================     =============================
Parameter                Description
====================     =============================
**timeout**              The maximum time a sample will run.|
**running timeout**      An additional timeout, if a task is in the running state this many seconds past **timeout**, the task is considered failed.
**fetch delay seconds** 
**poll interval** 
**user agent**
====================     =============================

**[NSRL]**  

This module looks up hashes in the NSRL database.

.. tabularcolumns:: |p{3cm}|p{12cm}|

====================  =============================
Parameter             Description
====================  =============================
**hash_list** 
**offsets**   
====================  =============================

**[PEFile]**  

This module extracts out feature information from EXE files. It uses `pefile <https://code.google.com/p/pefile/>`_ which is currently not available for python 3.

**[Tika]**  

This module extracts metadata from the file using `Tika <https://tika.apache.org/>`_. For configuration of the module see the `tika-python <https://github.com/chrismattmann/tika-python/blob/master/README.md>`_ documentation.

.. tabularcolumns:: |p{3cm}|p{12cm}|

====================  =============================
Parameter             Description
====================  =============================
**remove-entry**      A python list of Tika results that should not be included in the report.
====================  =============================

**[TrID]**  

This module runs `TrID <http://mark0.net/soft-trid-e.html>`_ against a file. The definition file must be in the same folder as the executable malware sample.

**[vtsearch]**  

This module searches `virustotal <https://www.virustotal.com/>`_ for the files hash and download the report if available.

.. tabularcolumns:: |p{3cm}|p{12cm}|

====================  =============================
Parameter             Description
====================  =============================
**apikey**            Public/private api key. Can optionally make it a list and the requests will be distributed across them. This is useful when two groups with private api keys want to share the load and reports.
====================  =============================

**[VxStream]**  
This module submits a file to a VxStream Sandbox cluster for analysis

.. tabularcolumns:: |p{3cm}|p{12cm}|

====================  =============================
Parameter             Description
====================  =============================
**BASE URL** 
**API URL**           The URL to the API server (include the /api/ in this URL).
**API Key**           The user's API key to the API server.
**API Secret**        The user's secret to the API server.
**Environment ID** 
**Verify** 
**timeout**           The maximum time a sample will run
**running timeout**   An additional timeout, if a task is in the running state this many seconds past **timeout**, the task is considered failed.
====================  =============================

**[YaraScan]**  

This module scans the files with yara and returns the results; yara-python must be installed.

.. tabularcolumns:: |p{3cm}|p{12cm}|

====================  =============================
Parameter             Description
====================  =============================
**ruledir**           The directory to look for rule files in.
**fileextensions**    A python array of all valid rule file extensions. Files not ending in one of these will be ignored.
**ignore-tags**       A python array of yara rule tags that will not be included in the report.
====================  =============================

Standalone Docker Installation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To introduce new users to the power of the MultiScanner framework, web UI, and REST API, we have built a standalone docker application that is simple to run in new environments. Simply clone the top level directory and run::

    $ docker-compose up

This will build the 3 necessary containers (one for the web application, one for the REST API, and one for the ElasticSearch backend).

Running this command will generate a lot of output and take some time. The system is not ready until you see the following output in your terminal::

    api_1      |  * Running on http://0.0.0.0:8080/ (Press CTRL+C to quit)

.. note:: We assume you are already running latest version of docker and have the latest version of docker-compose installed on your machine. Guides on how to do that are here: https://docs.docker.com/engine/installation/ and here: https://docs.docker.com/compose/install/

.. note:: Because this docker container runs two web applications and an ElasticSearch node, there is a fairly high requirement for computing power (RAM). We recommend running this on a machine with at least 4GB of RAM.

.. note::  THIS CONTAINER IS NOT DESIGNED FOR PRODUCTION USE. This is simply a primer for using MultiScanner's web interface. Users should not run this in production or at scale. The MultiScanner framework is highly scalable and distributed, but that requires a full install. Currently, we support installing the distributed system via Ansible. More information about that process can be found here: `<https://github.com/mitre/multiscanner-ansible>`_.

.. note:: This container will only be reachable and functionable on localhost.

.. note:: Additionally, if you are installing this system behind a proxy, you must edit the docker-compose.yml file in four places. First, uncomment `lines 18-20 <https://github.com/mitre/multiscanner/blob/feature-celery/docker-compose.yml#L18>`_ and `lines 35-37 <https://github.com/mitre/multiscanner/blob/feature-celery/docker-compose.yml#L35>`_. Next, uncomment `lines 25-28 <https://github.com/mitre/multiscanner/blob/feature-celery/docker-compose.yml#L25>`_ and set the correct proxy variables there. Finally, do the same thing in `lines 42-45 <https://github.com/mitre/multiscanner/blob/feature-celery/docker-compose.yml#L42>`_. The docker-compose.yml file has comments to make clear where to make these changes.