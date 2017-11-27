Using MultiScanner
==================

Default Modules
---------------
Whether accessing MultiScanner through its Web UI or its API, Table 1 shows the set of modules currently available in MultiScanner.

| AV Scans |   |
| -------- | - |
| AVG 2014 | Scans sample with AVG 2014.|
| ClamAVScan | Scans sample with ClamAV.|
| Sandbox Detonation | Detonates the sample in a sandbox for dynamic analysis |
| ------------------ | ------------------------------------------------------ |
| Cuckoo Sandbox | Submits a sample to Cuckoo Sandbox cluster for analysis.|
| FireEye API | Detoniates the sample in FireEye AX via FireEye's API. This "API" version replaces the "FireEye Scan" module.|

Web User Interface
------------------

API
---
Via its RESTful API, MultiScanner can be incorporated as a module in another project. Below is a simple example of how to import MultiScanner into a Python script.

``` python
import multiscanner
output = multiscanner.multiscan(FileList)
Results = multiscanner.parse_reports(output, python=True)
```

```Results``` is a dictionary object where each key is a filename of a scanned file.

```multiscanner.config_init(filepath)``` will create a default configuration file at
the location defined by filepath.