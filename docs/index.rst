.. MultiScanner documentation master file, created by
   sphinx-quickstart on Fri Dec 22 13:35:06 2017.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

MultiScanner
============

MultiScanner is a distributed file analysis framework that assists the user in evaluating a set
of files by automatically running a suite of tools for the user and aggregating the output.
Tools can be custom python scripts, web APIs, software running on another machine, etc.
Tools are incorporated by creating modules that run in the MultiScanner framework.

Modules are designed to be quickly written and easily incorporated into the framework.
Existing modules are related to malware analysis, but the framework is not limited in
scope. For descriptions of existing modules, see [Analysis Modules](use/use-analysis-mods.md). Module configuration options are given in [Installation](install#module-configuration).

MultiScanner supports a distributed workflow for sample storage, analysis, and report viewing. This functionality includes a web interface, a REST API, a distributed file system (GlusterFS), distributed report storage / searching (ElasticSearch), and distributed task management (Celery / RabbitMQ). See the [workflow diagram](arch.md#complete-workflow) for details.


.. toctree::
   :maxdepth: 3
   :glob:
   
   *


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
