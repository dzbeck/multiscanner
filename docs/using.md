Usage information on the web user interface and the REST API is below. We also provide information on currently available analysis modules and analytics.

#Web User Interface
need details...

#API
Via its RESTful API, MultiScanner can be incorporated as a module in another project. Below is a simple example of how to import MultiScanner into a Python script.

``` python
import multiscanner
output = multiscanner.multiscan(FileList)
Results = multiscanner.parse_reports(output, python=True)
```

```Results``` is a dictionary object where each key is a filename of a scanned file.

```multiscanner.config_init(filepath)``` will create a default configuration file at
the location defined by filepath.

#Current Analysis Modules

The set of analysis modules currently available in MultiScanner are listed below.

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
| FireEye API | Detonates the sample in FireEye AX via FireEye's API.|
| VxStream | Submits a file to a VxStream Sandbox cluster for analysis.|

| Metadata |   |
| -------- | - |
|ExifToolsScan | Scans sample with Exif tools and returns the results.|
|MD5 | Generates the MD5 hash of the sample.|
|PEFile | Extracts features from EXE files.|
|SHA1 | |
|SHA256 | Generates the SHA256 hash of the sample.|
|Tika | Extracts metadata from the sample using [Tika](https://tika.apache.org/).|
|TrID | Runs [TrID](http://mark0.net/soft-trid-e.html) against a file.|
|Flare FLOSS | FireEye Labs Obfuscated String Solver uses static analysis techniques to deobfuscate strings from malware binaries. [floss]|
|libmagic | Runs libmagic against the files to identify filetype.|
|Metadefender | Runs Metadefender against a file.|
|pdfinfo | Extracts feature information from PDF files using pdf-parser [http://blog.didierstevens.com/programs/pdf-tools/].|
|pehasher | "Computes pehash values using a variety of algorithms: totalhase, anymaster, anymaster_v1_0_1, endgame, crits, and pehashng."|
|ssdeep | Generates context triggered piecewise hashes (CTPH) for files. More information can be found on the [ssdeep website](http://ssdeep.sourceforge.net/).|

| Signatures |   |
| ---------- | - |
| YaraScan | Scans the sample with Yara and returns the results.|

#Current Analytics

###ssdeep Comparison

Fuzzy hashing is an effective method to identify similar files based on common byte strings despite changes in the byte order and structure of the files. [ssdeep](https://ssdeep-project.github.io/ssdeep/index.html) provides a fuzzy hash implementation and provides the capability to compare hashes. The [Virus Bulletin](https://www.virusbulletin.com/virusbulletin/2015/11/optimizing-ssdeep-use-scale/) originally described a method for comparing ssdeep hashes at scale. 

Comparing ssdeep hashes at scale is a challenge. Therefore, the ssdeep analytic computes ```ssdeep.compare``` for all samples where the result is non-zero and provides the capability to return all samples clustered based on the ssdeep hash. Furthermore,

- When possible, it can be effective to push work to the Elasticsearch cluster which support horizontal scaling. For the ssdeep comparison, Elasticsearch [NGram  Tokenizers](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-ngram-tokenizer.html)
are used to compute 7-grams of the chunk and double-chunk portions of the ssdeep hash as described here [[2]](http://www.intezer.com/intezer-community-tip-ssdeep-comparisons-with-elasticsearch/). This prevents the comparison of two ssdeep hashes where the result will be zero.

- Because we need to compute ```ssdeep.compare```, the ssdeep analytic cannot be done entirely in Elasticsearch. Python is used to query Elasicsearch, compute ```ssdeep.compare``` on the results, and update the documents in Elasticsearch.

- [celery beat](http://docs.celeryproject.org/en/latest/userguide/periodic-tasks.html) is used to schedule and kick off the ssdeep comparison task nightly at 2am local time, when the system is experiencing less load from users. This ensures the analytic will be run on all samples without adding an exorbinant load to the system.