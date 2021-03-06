.. PyNIDM documentation master file, created by
   sphinx-quickstart on Mon Aug 13 11:52:15 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

PyNIDM: Neuroimaging Data Model in Python
##########################################
A Python library to manipulate the [Neuroimaging Data Model](http://nidm.nidash.org).


.. toctree::
   :maxdepth: 2
   :caption: Contents:


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`


|Build Status|

.. contents::
.. section-numbering::


Dependencies
============

* Git-annex <https://git-annex.branchable.com/install/>
* Graphviz <http://graphviz.org> (native package):
* Fedora: `dnf install graphviz`
* OS-X: `brew install graphviz`

Creating a conda environment and installing the library (tested with OSX)
=========================================================================

macOS
-----  
.. code-block:: bash

	$ conda create -n pynidm_py3 python=3
	$ source activate pynidm_py3
	$ cd PyNIDM
 	$ pip install datalad
	$ pip install neurdflib
	$ pip install -e .

You can try to run a test: `pytest`

NIDM-Experiment Tools
=====================

Query
-----

.. code-block:: bash

	$ pynidm query [OPTIONS]

Options:
  -nl, --nidm_file_list TEXT  A comma separated list of NIDM files with full
                              path  [required]
  -q, --query_file PATH       Text file containing a SPARQL query to execute
                              [required]
  -o, --output_file TEXT      Optional output file (CSV) to store results of
                              query
  -u, --uri URI               URI for a :ref:`REST API style query<rest>`
                              file
  -j                          Output results in JSON format (default) or not
                              json format
  --help                      Show this message and exit.

Details on the REST API URI format and usage can be found on the :ref:`REST API usage<rest>` page.

BIDS MRI Conversion to NIDM
---------------------------
This program will convert a BIDS MRI dataset to a NIDM-Experiment RDF document.  It will parse phenotype information and simply store variables/values and link to the associated json data dictionary file.  To use this tool please set your INTERLEX_API_KEY environment variable to your unique API key.  To get an Interlex API key you visit [SciCrunch](http://scicrunch.org/nidm-terms), register for an account, then click on "MyAccount" and "API Keys" to add a new API key for your account.


.. code-block:: bash

    $ bidsmri2nidm -d [ROOT BIDS DIRECT] -bidsignore

Example 1:No variable->term mapping, simple BIDS dataset conversion which will add nidm.ttl file to BIDS dataset and .bidsignore file:

.. code-block:: bash

    $ bidsmri2nidm -d [root directory of BIDS dataset] -o [PATH/nidm.ttl]

Example 2:No variable->term mapping, simple BIDS dataset conversion but storing nidm file somewhere else:


.. code-block:: bash

    $ bidsmri2nidm -d [root directory of BIDS dataset] -json_map [Your JSON file] -bidsignore

Example 5 BIDS conversion with variable->term mappings, uses JSON mapping file first then uses Interlex, adds nidm.ttl file to root of BIDS dataset and adds to .bidsignore file:

	 json mapping file has entries for each variable with mappings to formal terms.  Example:

    	 {

    		 "site": {

			 "definition": "Number assigned to site",

			 "label": "site_id (UC Provider Care)",

			 "url": "http://uri.interlex.org/NDA/uris/datadictionary/elements/2031448"

			 },

			 "gender": {

			 "definition": "ndar:gender",

			 "label": "ndar:gender",

			 "url": "https://ndar.nih.gov/api/datadictionary/v2/dataelement/gender"

			 }

    	 }

optional arguments:
	-h, --help            show this help message and exit

	-d DIRECTORY          Path to BIDS dataset directory

	-jsonld, --jsonld     If flag set, output is json-ld not TURTLE

	-png, --png           If flag set, tool will output PNG file of NIDM graph

	-bidsignore, --bidsignore

	                      If flag set, tool will add NIDM-related files to .bidsignore file

	-o OUTPUTFILE         Outputs turtle file called nidm.ttl in BIDS directory by default

	map variables to terms arguments:

	-json_map JSON_MAP, --json_map JSON_MAP

	                      Optional user-suppled JSON file containing variable-term mappings.

	-ilxkey KEY, --ilxkey KEY

	                      Interlex/SciCrunch API key to use for query


CSV File to NIDM Conversion
---------------------------
This program will load in a CSV file and iterate over the header variable
names performing an elastic search of https://scicrunch.org/nidm-terms for NIDM-ReproNim
tagged terms that fuzzy match the variable names. The user will then
interactively pick a term to associate with the variable name. The resulting
annotated CSV data will then be written to a NIDM data file.  To use this tool please set your INTERLEX_API_KEY environment variable to your unique API key.  To get an Interlex API key you visit [SciCrunch](http://scicrunch.org/nidm-terms), register for an account, then click on "MyAccount" and "API Keys" to add a new API key for your account.


.. code-block:: bash

    $ csv2nidm [OPTIONS]

optional arguments:
  -h, --help            show this help message and exit

  -csv CSV_FILE         Path to CSV file to convert

  -json_map JSON_MAP    User-suppled JSON file containing variable-term mappings.

  -nidm NIDM_FILE       Optional NIDM file to add CSV->NIDM converted graph to

  -out OUTPUT_FILE      Filename to save NIDM file


.. |Build Status| image:: https://travis-ci.org/incf-nidash/PyNIDM.svg?branch=master
    :target: https://travis-ci.org/incf-nidash/PyNIDM
    :alt: Build status of the master branch




.. _rest:

PyNIDM: REST API and Command Line Usage
##########################################

Introduction
============

There are two main ways to interact with NIDM data using the PyNIDM REST API. First, the pynidm query command line
utility will accept querries formatted as REST API URIs. Second, the rest-server.py script can be used to run a
HTTP server to accept and process requests. This script can either be run directly or using a docker container
defined in the docker directory of the project.

Example usage:

.. code-block:: bash

   $ pynidm query -nl "cmu_a.ttl,cmu_b.ttl" -u /projects

   dc1bf9be-10a3-11ea-8779-003ee1ce9545
   ebe112da-10a3-11ea-af83-003ee1ce9545

   $

Installation
============

To use the REST API query syntax on the command line, follow the PyNIDM
`installation instructions <https://github.com/incf-nidash/PyNIDM/>`_.

The simplest way to deploy a HTTP REST API server would be with the provided docker container. You can find instructions
for that process in the `README.md <https://github.com/incf-nidash/PyNIDM/tree/master/docker>`_ file in the docker
directory of the Github repository.


URI formats
===========

You can find details on the REST API at the `SwaggerHub API Documentation <https://app.swaggerhub.com/apis-docs/albertcrowley/PyNIDM>`_.
The OpenAPI specification file is part of the Github repository in 'docs/REST_API_definition.openapi.yaml'

Here is a list of the current operations. See the SwaggerHub page for more details and return formats.

::

- /projects
- /projects/{project_id}
- /projects/{project_id}/subjects
- /projects/{project_id}/subjects?filter=[filter expression]
- /projects/{project_id}/subjects/{subject_id}
- /projects/{project_id}/subjects/{subject_id}/instruments/{instrument_id}
- /projects/{project_id}/subjects/{subject_id}/derivatives/{derivative_id}
- /subjects/{subject_id}
- /statistics/projects/{project_id}

You can append the following query parameters to many of the operations:

::

- filter
- field

Operations
-----------

**/projects**
 | Get a list of all project IDs available.
 | Supported query parameters: none

**/projects/{project_id}**
 | See some details for a project. This will include the list of subject IDs and data elements used in the project
 | Supported query parameters: fitler, fields

**/projects/{project_id}/subjects**
 | Get the list of subjects in a project
 | Supported query parameters: filter

**/projects/{project_id}/subjects/{subject_id}**
 | Get the details for a particular subject. This will include the results of any instrumnts or derivatives associated with the subject, as well a a list of the related activites.
 | Supported query parameters: none

**/projects/{project_id}/subjects/{subject_id}/instruments/{instrument_id}**
 | Get the values for a particular instrument
 | Supported query parameters: none

**/projects/{project_id}/subjects/{subject_id}/derivatives/{derivative_id}**
 | Get the values for a particular derivative
 | Supported query parameters: none

**/subjects/{subject_id}**
 | Get the details for a particular subject. This will include the results of any instrumnts or derivatives associated with the subject, as well a a list of the related activites.
 | Supported query parameters: none

**/statistics/projects/{project_id}**
 | See project statistics. You can also use this operation to get statsitcs on a particular instrument or derivative entry by use a *field* query option.
 | Supported query parameters: filter, field

**/statistics/projects/{project_id}/subjects/{subject_id}**
 | See some details for a project. This will include the list of subject IDs and data elements used in the project
 | Supported query parameters: none

Query Parameters
-----------------

**filter**
 | The filter query parameter is ues when you want to receive data only on subjects that match some criteria.  The format for the fitler value should be of the form:
 |    *identifier op value [ and identifier op value and ... ]*
 | Identifers should be formatted as "instrument.ID" or "derivatives.ID"  You can use any value for the instrument ID that is shown for an instrument or in the data_elements section of the project details. For the derivative ID, you can use the last component of a derivative field URI (ex. for the URI http://purl.org/nidash/fsl#fsl_000007, the ID would be "fsl_000007") or the exact label shown when viewing derivative data (ex. "Left-Caudate (mm^3)")
 | The *op* can be one of "eq", "gt", "lt"

 | **Example filters:**
 |    *?filter=instruments.AGE_AT_SCAN gt 30*
 |    *?filter=instrument.AGE_AT_SCAN eq 21 and derivative.fsl_000007 lt 3500*

**fields**
 | The fields query parameter is used to specify what fields should be detailed in a statistics operation. For each field specified the result will show minimum, maximum, average, median, and standard deviation for the values of that field across all subjects matching the operation and filter. Multiple fields can be specified by separating each field with a comma.
 | Fields should be formatted in the same way as identifiers are specified in the filter parameter.

 | **Example field query:**
 |    *http://localhost:5000/statistics/projects/abc123?field=instruments.AGE_AT_SCAN,derivatives.fsl_000020*


Return Formatting
==================

By default the HTTP REST API server will return JSON formatted objects or arrays.  When using the pynidm query
command line utility the default return format is text (when possible) or you can use the -j option to have the
output formatted as JSON.



Examples
--------

**Get the UUID for all the projects at this locaiton:**

.. code-block:: bash

   curl http://localhost:5000/projects

Example response:

.. code-block:: JSON

   [
       "dc1bf9be-10a3-11ea-8779-003ee1ce9545"
   ]

**Get the project summary details:**

.. code-block:: HTML

   curl http://localhost:5000/projects/dc1bf9be-10a3-11ea-8779-003ee1ce9545

Example response:

.. code-block:: JSON

   {
     "AcquisitionModality": [
       "MagneticResonanceImaging"
     ],
     "ImageContrastType": [
       "T1Weighted",
       "FlowWeighted"
     ],
     "ImageUsageType": [
       "Anatomical",
       "Functional"
     ],
     "Task": [
       "rest"
     ],
     "sio:Identifier": "1.0.1",
     "dctypes:title": "ABIDE CMU_a Site",
     "http://www.w3.org/1999/02/22-rdf-syntax-ns#type": "http://www.w3.org/ns/prov#Activity",
     "prov:Location": "file://datasets.datalad.org/abide/RawDataBIDS/CMU_a",
     "subjects": [
       "fdb6c8bc-67aa-11ea-ba45-003ee1ce9545",
       "b276ebb6-67aa-11ea-ba45-003ee1ce9545",
       "a38c4e42-67aa-11ea-ba45-003ee1ce9545",
       "a2ff751c-67aa-11ea-ba45-003ee1ce9545",
       "cfce5728-67aa-11ea-ba45-003ee1ce9545",
       "f165e7ae-67aa-11ea-ba45-003ee1ce9545",
       "cf4605ee-67aa-11ea-ba45-003ee1ce9545",
       "a1efa78c-67aa-11ea-ba45-003ee1ce9545",
       "d0de8ebc-67aa-11ea-ba45-003ee1ce9545",
       "a4a999ba-67aa-11ea-ba45-003ee1ce9545",
       "a0555098-67aa-11ea-ba45-003ee1ce9545",
       "b41d75f2-67aa-11ea-ba45-003ee1ce9545",
       "be3fbff0-67aa-11ea-ba45-003ee1ce9545",
       "eec5a0ca-67aa-11ea-ba45-003ee1ce9545"
     ],
     "data_elements": [
       "SCQ_TOTAL", "VIQ", "VINELAND_WRITTEN_V_SCALED", "WISC_IV_VCI", "ADOS_COMM", "FILE_ID", "WISC_IV_BLK_DSN_SCALED",
       "WISC_IV_SYM_SCALED", "ADI_R_SOCIAL_TOTAL_A", "WISC_IV_INFO_SCALED", "ADOS_GOTHAM_SEVERITY",
       "VINELAND_COMMUNICATION_STANDARD", "VINELAND_PERSONAL_V_SCALED", "SUB_ID", "ADOS_GOTHAM_TOTAL",
       "ADI_R_VERBAL_TOTAL_BV", "VINELAND_COPING_V_SCALED", "VINELAND_DOMESTIC_V_SCALED", "SRS_COGNITION",
       "FIQ_TEST_TYPE", "WISC_IV_PSI", "OFF_STIMULANTS_AT_SCAN", "VINELAND_PLAY_V_SCALED", "AGE_AT_MPRAGE",
       "VIQ_TEST_TYPE", "ADI_RRB_TOTAL_C", "WISC_IV_DIGIT_SPAN_SCALED", "FIQ", "DSM_IV_TR", "DX_GROUP",
       "VINELAND_INTERPERSONAL_V_SCALED", "VINELAND_SUM_SCORES", "ADOS_STEREO_BEHAV", "ADI_R_ONSET_TOTAL_D",
       "ADOS_GOTHAM_SOCAFFECT", "ADOS_GOTHAM_RRB", "CURRENT_MED_STATUS", "VINELAND_EXPRESSIVE_V_SCALED",
       "AGE_AT_SCAN", "WISC_IV_PRI", "SEX", "SRS_RAW_TOTAL", "ADOS_RSRCH_RELIABLE", "WISC_IV_SIM_SCALED",
       "WISC_IV_CODING_SCALED", "SRS_MANNERISMS", "AQ_TOTAL", "HANDEDNESS_SCORES", "HANDEDNESS_CATEGORY",
       "SRS_VERSION", "ADI_R_RSRCH_RELIABLE", "EYE_STATUS_AT_SCAN", "MEDICATION_NAME", "ADOS_SOCIAL",
       "ADOS_MODULE", "VINELAND_RECEPTIVE_V_SCALED", "VINELAND_DAILYLVNG_STANDARD", "VINELAND_ABC_STANDARD",
       "PIQ", "VINELAND_SOCIAL_STANDARD", "SITE_ID", "COMORBIDITY", "BMI", "VINELAND_COMMUNITY_V_SCALED",
       "ADOS_TOTAL", "VINELAND_INFORMANT", "WISC_IV_WMI", "WISC_IV_MATRIX_SCALED", "WISC_IV_LET_NUM_SCALED",
       "PIQ_TEST_TYPE", "SRS_COMMUNICATION", "WISC_IV_VOCAB_SCALED", "SRS_AWARENESS", "WISC_IV_PIC_CON_SCALED",
       "SRS_MOTIVATION"
     ]
   }

**Get Left-Pallidum volume (fsl_0000012) values for all subjects in a project**
.. code-block:: HTML

   pynidm query -nl ttl/cmu_a.ttl -u /projects/cc305b3e-67aa-11ea-ba45-003ee1ce9545?fields=fsl_000012

.. code-block:: HTML

   <pre>
   -----------------------------------------------  -----------------------------------------------------
   AcquisitionModality                              ["MagneticResonanceImaging"]
   ImageContrastType                                ["FlowWeighted", "T1Weighted"]
   ImageUsageType                                   ["Functional", "Anatomical"]
   Task                                             ["rest"]
   sio:Identifier                                   "1.0.1"
   dctypes:title                                    "ABIDE CMU_a Site"
   http://www.w3.org/1999/02/22-rdf-syntax-ns#type  "http://www.w3.org/ns/prov#Activity"
   prov:Location                                    "file://datasets.datalad.org/abide/RawDataBIDS/CMU_a"
   -----------------------------------------------  -----------------------------------------------------

   subjects
   ------------------------------------
   fdb6c8bc-67aa-11ea-ba45-003ee1ce9545
   b276ebb6-67aa-11ea-ba45-003ee1ce9545
   a38c4e42-67aa-11ea-ba45-003ee1ce9545
   a2ff751c-67aa-11ea-ba45-003ee1ce9545
   cfce5728-67aa-11ea-ba45-003ee1ce9545
   f165e7ae-67aa-11ea-ba45-003ee1ce9545
   cf4605ee-67aa-11ea-ba45-003ee1ce9545
   a1efa78c-67aa-11ea-ba45-003ee1ce9545
   d0de8ebc-67aa-11ea-ba45-003ee1ce9545
   a4a999ba-67aa-11ea-ba45-003ee1ce9545
   a0555098-67aa-11ea-ba45-003ee1ce9545
   b41d75f2-67aa-11ea-ba45-003ee1ce9545
   be3fbff0-67aa-11ea-ba45-003ee1ce9545
   eec5a0ca-67aa-11ea-ba45-003ee1ce9545

   data_elements
   -------------------------------
   SCQ_TOTAL
   VIQ
   ...
   WISC_IV_PIC_CON_SCALED
   SRS_MOTIVATION

   subject                               field       datumType    label                   value  units
   ------------------------------------  ----------  -----------  --------------------  -------  -------
   fdb6c8bc-67aa-11ea-ba45-003ee1ce9545  fsl_000012  ilx_0738276  Left-Pallidum (mm^3)     1630  mm^3
   b276ebb6-67aa-11ea-ba45-003ee1ce9545  fsl_000012  ilx_0738276  Left-Pallidum (mm^3)     2062  mm^3
   a38c4e42-67aa-11ea-ba45-003ee1ce9545  fsl_000012  ilx_0738276  Left-Pallidum (mm^3)     1699  mm^3
   a2ff751c-67aa-11ea-ba45-003ee1ce9545  fsl_000012  ilx_0738276  Left-Pallidum (mm^3)     1791  mm^3
   cfce5728-67aa-11ea-ba45-003ee1ce9545  fsl_000012  ilx_0738276  Left-Pallidum (mm^3)     2017  mm^3
   f165e7ae-67aa-11ea-ba45-003ee1ce9545  fsl_000012  ilx_0738276  Left-Pallidum (mm^3)     2405  mm^3
   cf4605ee-67aa-11ea-ba45-003ee1ce9545  fsl_000012  ilx_0738276  Left-Pallidum (mm^3)     2062  mm^3
   a1efa78c-67aa-11ea-ba45-003ee1ce9545  fsl_000012  ilx_0738276  Left-Pallidum (mm^3)     1961  mm^3
   d0de8ebc-67aa-11ea-ba45-003ee1ce9545  fsl_000012  ilx_0738276  Left-Pallidum (mm^3)     1568  mm^3
   a4a999ba-67aa-11ea-ba45-003ee1ce9545  fsl_000012  ilx_0738276  Left-Pallidum (mm^3)     1948  mm^3
   a0555098-67aa-11ea-ba45-003ee1ce9545  fsl_000012  ilx_0738276  Left-Pallidum (mm^3)     1764  mm^3
   b41d75f2-67aa-11ea-ba45-003ee1ce9545  fsl_000012  ilx_0738276  Left-Pallidum (mm^3)     2031  mm^3
   be3fbff0-67aa-11ea-ba45-003ee1ce9545  fsl_000012  ilx_0738276  Left-Pallidum (mm^3)     1935  mm^3
   eec5a0ca-67aa-11ea-ba45-003ee1ce9545  fsl_000012  ilx_0738276  Left-Pallidum (mm^3)     1806  mm^3
   </pre>

**Get the subjects in a project:**

.. code-block:: HTML

   pynidm query -nl "cmu_a.nidm.ttl" -u http://localhost:5000/projects/dc1bf9be-10a3-11ea-8779-003ee1ce9545/subjects

Example response:

.. code-block:: JSON

   deef8eb2-10a3-11ea-8779-003ee1ce9545
   df533e6c-10a3-11ea-8779-003ee1ce9545
   ddbfb454-10a3-11ea-8779-003ee1ce9545
   df21cada-10a3-11ea-8779-003ee1ce9545
   dcfa35b2-10a3-11ea-8779-003ee1ce9545
   de89ce4c-10a3-11ea-8779-003ee1ce9545
   dd2ce75a-10a3-11ea-8779-003ee1ce9545
   ddf21020-10a3-11ea-8779-003ee1ce9545
   debc0f74-10a3-11ea-8779-003ee1ce9545
   de245134-10a3-11ea-8779-003ee1ce9545
   dd5f2f30-10a3-11ea-8779-003ee1ce9545
   dd8d4faa-10a3-11ea-8779-003ee1ce9545
   df87cbaa-10a3-11ea-8779-003ee1ce9545
   de55285e-10a3-11ea-8779-003ee1ce9545


**Use the command line to get statistics on a project for the AGE_AT_SCAN and a FSL data element:**

.. code-block:: HTML

   pynidm query -nl ttl/cmu_a.nidm.ttl -u /statistics/projects/dc1bf9be-10a3-11ea-8779-003ee1ce9545?fields=instruments.AGE_AT_SCAN,derivatives.fsl_000001

Example response:


.. code-block:: bash

  -------------------------------------------------  ---------------------------------------------
  "http://www.w3.org/1999/02/22-rdf-syntax-ns#type"  http://www.w3.org/ns/prov#Activity
  "title"                                            ABIDE CMU_a Site
  "Identifier"                                       1.0.1
  "prov:Location"                                    /datasets.datalad.org/abide/RawDataBIDS/CMU_a
  "NIDM_0000171"                                     14
  "age_max"                                          33.0
  "age_min"                                          21.0

    gender
  --------
         1
         2

  handedness
  ------------
  R
  L
  Ambi

  subjects
  ------------------------------------
  de89ce4c-10a3-11ea-8779-003ee1ce9545
  deef8eb2-10a3-11ea-8779-003ee1ce9545
  dd8d4faa-10a3-11ea-8779-003ee1ce9545
  ddbfb454-10a3-11ea-8779-003ee1ce9545
  de245134-10a3-11ea-8779-003ee1ce9545
  debc0f74-10a3-11ea-8779-003ee1ce9545
  dd5f2f30-10a3-11ea-8779-003ee1ce9545
  ddf21020-10a3-11ea-8779-003ee1ce9545
  dcfa35b2-10a3-11ea-8779-003ee1ce9545
  df21cada-10a3-11ea-8779-003ee1ce9545
  df533e6c-10a3-11ea-8779-003ee1ce9545
  de55285e-10a3-11ea-8779-003ee1ce9545
  df87cbaa-10a3-11ea-8779-003ee1ce9545
  dd2ce75a-10a3-11ea-8779-003ee1ce9545

  -----------  ------------------  --------
  AGE_AT_SCAN  max                 33
  AGE_AT_SCAN  min                 21
  AGE_AT_SCAN  median              26
  AGE_AT_SCAN  mean                26.2857
  AGE_AT_SCAN  standard_deviation   4.14778
  -----------  ------------------  --------

  ----------  ------------------  -----------
  fsl_000001  max                 1.14899e+07
  fsl_000001  min                 5.5193e+06
  fsl_000001  median              7.66115e+06
  fsl_000001  mean                8.97177e+06
  fsl_000001  standard_deviation  2.22465e+06
  ----------  ------------------  -----------

**Get details on a subject. Use -j for a JSON formatted resonse:**

.. code-block:: HTML

   pynidm query -j -nl "cmu_a.nidm.ttl" -u http://localhost:5000/projects/dc1bf9be-10a3-11ea-8779-003ee1ce9545/subjects/df21cada-10a3-11ea-8779-003ee1ce9545

Example response:

.. code-block:: JSON

   {
  "uuid": "df21cada-10a3-11ea-8779-003ee1ce9545",
  "id": "0050665",
  "activity": [
    "e28dc764-10a3-11ea-a7d3-003ee1ce9545",
    "df28e95a-10a3-11ea-8779-003ee1ce9545",
    "df21c76a-10a3-11ea-8779-003ee1ce9545"
  ],
  "instruments": {
    "e28dd218-10a3-11ea-a7d3-003ee1ce9545": {
      "SRS_VERSION": "nan",
      "ADOS_MODULE": "nan",
      "WISC_IV_VCI": "nan",
      "WISC_IV_PSI": "nan",
      "ADOS_GOTHAM_SOCAFFECT": "nan",
      "VINELAND_PLAY_V_SCALED": "nan",
      "null": "http://www.w3.org/ns/prov#Entity",
      "VINELAND_EXPRESSIVE_V_SCALED": "nan",
      "SCQ_TOTAL": "nan",
      "SRS_MOTIVATION": "nan",
      "PIQ": "104.0",
      "FIQ": "109.0",
      "WISC_IV_PRI": "nan",
      "FILE_ID": "CMU_a_0050665",
      "VIQ": "111.0",
      "WISC_IV_VOCAB_SCALED": "nan",
      "VINELAND_DAILYLVNG_STANDARD": "nan",
      "WISC_IV_SIM_SCALED": "nan",
      "WISC_IV_DIGIT_SPAN_SCALED": "nan",
      "AGE_AT_SCAN": "33.0"
      }
   },
  "derivatives": {
      "b9fe0398-16cc-11ea-8729-003ee1ce9545": {
         "URI": "http://iri.nidash.org/b9fe0398-16cc-11ea-8729-003ee1ce9545",
         "values": {
           "http://purl.org/nidash/fsl#fsl_000005": {
             "datumType": "ilx_0102597",
             "label": "Left-Amygdala (voxels)",
             "value": "1573",
             "units": "voxel"
           },
           "http://purl.org/nidash/fsl#fsl_000004": {
             "datumType": "ilx_0738276",
             "label": "Left-Accumbens-area (mm^3)",
             "value": "466.0",
             "units": "mm^3"
           },
           "http://purl.org/nidash/fsl#fsl_000003": {
             "datumType": "ilx_0102597",
             "label": "Left-Accumbens-area (voxels)",
             "value": "466",
             "units": "voxel"
           }
         },
         "StatCollectionType": "FSLStatsCollection"
      }
   }
