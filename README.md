pdt - Provider Data Tools
=========================

Version: 0.6.8

This repository contains a number of command-line utilities and related code libraries for
parsing, creating, and validating US-based health provider data.  These tools are:

* chop-nppes-public   - Parse the npi public data dissemination into flattened files.
* csv2pjson_public.py - Parse the npi public data dissemination into ProviderJSON files.
* csv2fhir_public.py  - Parse the npi public data dissemination to FHIR Practitioner and Organiation Resources.
* validate-pjson      - Parse a Provider JSON document and output errors and warnings as JSON.
* validate-pjson-dir  - Bulk validation of the output of csv2pjson-public.py.
* create-provider-indexes - Create default MongoDB indexes on Provides JSON data to support public search on common fields.
* loadnppes.py        - Download public, parse to JSON, and load to MongoDB in one step.


Please note the utilities `csv2json`, `json2mongo`, and `jsondir2mongo` have been
moved from `pdt` and placed in their own package called `jdt`. These tools are generic
and have utility outside health provider data.


Installation
------------

You can install the tool using `pip`. 

To install with pip just type:

    ~$ sudo pip install pdt

Note: If you use `sudo`, the scripts  will be installed at the system level and used by all users.
Add  `--upgrade` to the above install instructions to ensure you fetch the newest version. 


chop_nppes_public
-----------------


To make use of this script you need first fecth the "NPPES Data Dissemination" file.

To obtain the "NPPES Data Dissemination", go to  http://nppes.viva-it.com/NPI_Files.html.
Get the "Full Replacement Monthly" zip file.  Unzip the file with the unzip tool of your choice.



To run the utility simply call it on a command line and provide one command line argument, the csv file to parse:

    ~$ chop_nppes_public npidata_20050523-20140413.csv

The file name `npidata_20050523-20140413.csv` will vary depending on the date.

The script make take a few minutes to complete. When it completes you will have more files
in your current directory. Everything is still indexed by NPI. These files are described below.


* _basic.csv             - Contains basic demographic info.
* _addresses_flat.csv    - one address per line identifier as practice or mailing.
* _identifiers_flat.csv  - one identifier per line.
* _licenses_flat.csv 	 - one license per line.
* _taxonomy_flat.csv     - one taxonomy code per line and identified as primary or not.


csv2pjson_public.py
------------------

Convert the NPPES Public Data Dissemination  CSV file format to a directory of files in
ProviderJSON format.

Usage:

    csv2pjson_public.py [CSV_FILE] [OUTPUT_DIR]


Example:


    csv2pjson_public.py public_csvfile.csv output

Output:

  One file is created per line in the CSV file file inside  the directory`output`. Files are fanned out
  into a directory structure so as not to create millions of files in one directory.


csv2fhir_public.py
------------------

Convert the NPPES Public Data Dissemination  CSV file format to a directory of files in
ProviderJSON format.

Usage:

    csv2fhir_public.py [CSV_FILE] [OUTPUT_DIR]


Example:


    csv2fhir_public.py public_csvfile.csv output

Output:

  One file is created per line in the CSV file file inside  the directory`output`. Files are fanned out
  into a directory structure so as not to create millions of files in one directory.





validate_pjson
--------------

Validate the PJSON for compliance with a create/update request. It returns errors and warnings in
JSON to stdout.

Usage:


    validate_pjson [ProivderJSON] [update|create]


Example:

    validate_pjson  1003819723.json update

Example Output:

    {
    "errors": [
        "authorized_official_telephone_number must be in XXX-XXX-XXXX format.", 
        "EIN is required for a type-2 organization provider."
    ], 
    "warnings": [
        "Enumeration date is generated by CMS. The provided value will be ignored.", 
        "Last updated date is generated by CMS. The provided value will be ignored.", 
        "status is determined by CMS. The provided value will be ignored."
    ]
    }


loadnppes.py
------------

By streamlining several of the pdt utilities, the script loadnppes.py combines functionalty
for automatic setup. The script will download public data, parse to JProvider SON, and load
to MongoDB in one step. Note this script requires `unzip` and `wget` to be installed.

Usage:

    loadnppes.py [PROCESS_FULL Y/N] [DOWNLOAD_FROM_PUBLIC_FILE Y/N]"

Example:

    loadnppes.py y y 

Example Output:



    Downloading http://nppes.viva-it.com/NPPES_Data_Dissemination_March_2015.zip
    --2015-04-13 14:14:57--  http://nppes.viva-it.com/NPPES_Data_Dissemination_March_2015.zip
    Resolving nppes.viva-it.com (nppes.viva-it.com)... 68.142.118.4, 68.142.118.254
    Connecting to nppes.viva-it.com (nppes.viva-it.com)|68.142.118.4|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 514406694 (491M) [application/zip]
    Saving to: `NPPES_Data_Dissemination_March_2015.zip'
    
    0% [                                       ] 2,691,064   58.1K/s  eta 3h 38m 
    .
    .
    .
