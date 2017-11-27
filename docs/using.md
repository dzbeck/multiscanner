Using MultiScanner
==================
Information on default tool modules, the user interface, and the API is below.

##Default Tool Modules
----------------------
Whether accessing MultiScanner through its Web UI or its API, Table 1 shows the set of modules currently available in MultiScanner.

| AV Scans |   |
| -------- | - |
| AVG 2014 | Scans sample with AVG 2014|
| ClamAVScan | Scans sample with ClamAV|
| McAfeeScan |	Scans sample with McAfee AntiVirus Command Line|
| Microsoft Security Essentials	| |
| Metadefender | |
| vtsearch | Searches VirusTotal for sample’s hash and downloads the report if available|

| Sandbox Detonation |   |
| ------------------ | - |
| Cuckoo Sandbox | Submits a sample to Cuckoo Sandbox cluster for analysis.|
| FireEye API | Detoniates the sample in FireEye AX via FireEye's API. This "API" version replaces the "FireEye Scan" module.|
| VxStream | Submits a file to a VxStream Sandbox cluster for analysis.|

| Metadata |   |
| -------- | - |
|ExifToolsScan | Scans sample with Exif tools and returns the results.|
|MD5 | Generates the MD5 hash of the sample.|
|PEFile | Extracts features from EXE files. It uses pefile which is currently not available for Python 3. [https://code.google.com/p/pefile/]|
|SHA1 | |
|SHA256 | Generates the SHA256 hash of the sample.|
|Tika | Extracts metadata from the sample using Tika.|
|TrID | Runs TrID against the files. The definition file should be in the same folder as the executable.   [http://mark0.net/soft-trid-e.html]|
|Flare FLOSS | FireEye Labs Obfuscated String Solver uses static analysis techniques to deobfuscate strings from malware binaries. [floss]|
|libmagic | Runs libmagic against the files to identify filetype.|
|pdfinfo | Extracts feature information from PDF files using pdf-parser [http://blog.didierstevens.com/programs/pdf-tools/].|
|pehasher | "Computes pehash values using a variety of algorithms: totalhase, anymaster, anymaster_v1_0_1, endgame, crits, and pehashng."|
|ssdeep | Generates context triggered piecewise hashes (CTPH) for files. More information can be found on the ssdeep website [1xx].|

| Signatures |   |
| ---------- | - |
| YaraScan | Scans the sample with Yara and returns the results.|

##Web User Interface
--------------------

##API
-----
Via its RESTful API, MultiScanner can be incorporated as a module in another project. Below is a simple example of how to import MultiScanner into a Python script.

``` python
import multiscanner
output = multiscanner.multiscan(FileList)
Results = multiscanner.parse_reports(output, python=True)
```

```Results``` is a dictionary object where each key is a filename of a scanned file.

```multiscanner.config_init(filepath)``` will create a default configuration file at
the location defined by filepath.