MultiScanner is intended to be used by secure operation centers (SOCs), malware analysis centers, and other organization involved with cyber threat information (CTI) sharing. This section enumerates the associated use cases.  

Scalable Malware Analysis
-------------------------
Every component of MultiScanner is designed with scale in mind. 

Note that MultiScanner scaling does not include scaling required for external analysis tools such as Cuckoo Sandbox. Also, auto-scaling (e.g., scaling required to auto-provision virtual machines) is not included. New nodes must be deployed manually and added to the Ansible playbook for proper configuration (see Section x).

Manual Malware Analysis
-----------------------
Manual malware analysis is supported via modules.

Analysis-Oriented Malware Repository
--------------------------------------
Long term storage of binaries and metadata.

Data Enrichment
---------------
This take malware analysis results and enriches it in support of CTI organizations. In addition to analysis of submitted samples, other CTI sources can be integrated with MultiScanner – TAXII feeds, threat intel providers (FireEye, Proofpoint, CrowdStrike, etc.), closed source intel.

Data Analytics
--------------
Analytics on malware samples either by interacting with the Elasticsearch backend or plugging into the web/REST UI. 
Two clustering analytics are available:
	* ssdeep
	* pehash
