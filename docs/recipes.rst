===========================
Using the retriever-recipes
===========================

retriever-recipes
-----------------

The `Data Retriever`_ earlier used a simple CLI for developing new dataset scripts. This allowed users with no programming experience to quickly add most standard datasets to the Retriever by specifying the names and locations of the tables along with additional information about the configuration of the data. The script is saved as a JSON file, that follows the DataPackage_ standards.

.. _Data Retriever: http://data-retriever.org
.. _DataPackage: https://specs.frictionlessdata.io/data-package/

This functionality has been moved to the ``retriever-recipes`` repository to separate the scripts from the core ``retriever`` functionalities to help with organization, maintenance, and testing. The `retriever recipes`_ repository thus holds all the scripts which were earlier shipped with ``retriever`` and also all the script adding/editing functionalities.

.. _retriever recipes: https://github.com/weecology/retriever-recipes

Installation
------------

The ``retriever-recipes`` project can be installed from Github using the following steps:

::

  git clone https://www.github.com/weecology/retriever-recipes.git
  cd retriever-recipes
  python setup.py install

Script Creation
---------------

To create a new script, there are 2 methods :- 

1. Use retriever autocreate to automatically create a script template. Specify the type of data using -dt, the default data type is tabular. Download the files to a folder. In case of tabular data, the files should be CSV files. Autocreate can create a script template for each file using -f or use -d to create a single script template for all files in the directory.

::


  usage: retriever autocreate [-h] [-dt [{raster,vector,tabular}]] [-f] [-d]
                              [-o [O]] [--skip-lines SKIP_LINES]
                              path

  positional arguments:
    path                  path to the data file(s)

  optional arguments:
    -h, --help            show this help message and exit
    -dt [{raster,vector,tabular}]
                          datatype for files
    -f                    turn files into scripts
    -d                    turn a directory and subdirectories into scripts
    -o [O]                write scripts out to a designated directory
    --skip-lines SKIP_LINES
                          skip a set number of lines before processing data



2. Manual script creation using ``retriever-recipes new_json``, which starts the CLI tool for new script creation.

``Required``

#. **name:** A one word name for the dataset

``Strongly recommended``

#. **title:** Give the name of the dataset
#. **description:** A brief description of the dataset of ~25 words.
#. **citation:** Give a citation if available
#. **homepage:** A reference to the data or the home page
#. **keywords:** Helps in classifying the type of data (i.e using Taxon, Data Type, Spatial Scale, etc.)


``Mandatory for any table added; Add Table? (y/N)``

#. **table-name:** Name of the table, URL to the table
#. **table-url:** Name of the table, URL to the table

Basic Scripts
-------------

The most basic scripts structure requires only some general metadata about the
dataset, i.e., the shortname of the database and table, and the location of the
table.

**Example of a basic script, example.script**

``Creating script from the CLI``

::

  name (a short unique identifier; only lowercase letters and - allowed): example-mammal
  title: Mammal Life History Database - Ernest, et al., 2003
  description:
  citation: S. K. Morgan Ernest. 2003. Life history characteristics of placental non-volant mammals. Ecology 84:3402.
  homepage (for the entire dataset):
  keywords (separated by ';'): mammals ; compilation

  Add Table? (y/N): y
  table-name: species
  table-url: http://esapubs.org/archive/ecol/E084/093/Mammal_lifehistories_v2.txt
  missing values (separated by ';'):
  replace_columns (separated by ';'):
  delimiter:
  do_not_bulk_insert (bool = True/False):
  contains_pk (bool = True/False):
  escape_single_quotes (bool = True/False):
  escape_double_quotes (bool = True/False):
  fixed_width (bool = True/False):
  header_rows (int):
  Enter columns [format = name, type, (optional) size]:


  Add crosstab columns? (y,N): n

  Add Table? (y/N): n

``Created script``

::

  {
      "citation": "S. K. Morgan Ernest. 2003. Life history characteristics of placental non-volant mammals. Ecology 84:3402.",
      "description": "",
      "homepage": "",
      "keywords": [
          "Mammals",
          "Compilation"
      ],
      "name": "example-mammal",
      "resources": [
          {
              "dialect": {},
              "name": "species",
              "schema": {
                  "fields": []
              },
              "url": "http://esapubs.org/archive/ecol/E084/093/Mammal_lifehistories_v2.txt"
          }
      ],
      "retriever": "True",
      "retriever_minimum_version": "2.0.dev",
      "title": "Mammal Life History Database - Ernest, et al., 2003"
      "version": "1.0.0"
  }

Explanation for the keys:

- ``citation``: Citation for the dataset
- ``description``: Description for the dataset
- ``homepage``: Homepage or website where the data is hosted
- ``keywords``: Keywords/tags for the dataset (for searching and classification)
- ``name``: Shortname for the dataset. Unique, URL-identifiable
- ``resources``: List of tables within the dataset

  - ``dialect``: Metadata for retriever to process the table

    - ``missingValues``: (Optional) List of strings which represents missing values in tables
    - ``delimiter``: (Optional) a character which represent boundary between two separate value(ex. ',' in csv files)
    - ``header_rows``: (Optional) number of header rows in table.
  - ``name``: Name of the table
  - ``schema``: List of the columns in the table

    - ``fields``: (Optional-Recommended) List of columns and their types and (optional) size values
    - ``ct_column``: (Optional) Cross-tab column with column names from dataset

  - ``url``: URL of the table

- ``retriever``: Auto generated tag for script identification
- ``retriever_minimum_version``: Minimum version that supports this script
- ``title``: Title/Name of the dataset
- ``urls``: dictionary of table names and the respective urls
- ``version``: "1.0.0"

Multiple Tables
---------------

A good example of data with multiple tables is Ecological Archives E091-124-D1, `McGlinn et al. 2010`_. ``plant-comp-ok`` Vascular plant composition data.
Since there are several csv files, we create a table for each of the files.

Assuming we want to call our dataset McGlinn2010, below is an example of the script that will handle this data

.. _`McGlinn et al. 2010`: http://esapubs.org/archive/ecol/E091/124/

::

  ...
    "name": "McGlinn2010",
    "resources": [
        {
            "dialect": {},
            "name": "pres",
            "schema": {},
            "url": "http://esapubs.org/archive/ecol/E091/124/TGPP_pres.csv"
        },
        {
            "dialect": {},
            "name": "cover",
            "schema": {},
            "url": "http://esapubs.org/archive/ecol/E091/124/TGPP_cover.csv"
        },
        {
            "dialect": {},
            "name": "richness",
            "schema": {},
            "url": "http://esapubs.org/archive/ecol/E091/124/TGPP_rich.csv"
        },
        {
            "dialect": {},
            "name": "species",
            "schema": {},
            "url": "http://esapubs.org/archive/ecol/E091/124/TGPP_specodes.csv"
        },
        {
            "dialect": {},
            "name": "environment",
            "schema": {},
            "url": "http://esapubs.org/archive/ecol/E091/124/TGPP_env.csv"
        },
        {
            "dialect": {},
            "name": "climate",
            "schema": {},
            "url": "http://esapubs.org/archive/ecol/E091/124/TGPP_clim.csv"
        }
    ],
    "retriever": "True",
    "retriever_minimum_version": "2.0.dev",
    "title": "Vascular plant composition - McGlinn, et al., 2010",
    ...

Null Values
-----------

The Retriever can replace non-standard null values by providing a semi-colon separated list of those null values
after the table in which the null values occur.

::

  ...
  Table name: species
  Table URL: http://esapubs.org/archive/ecol/E084/093/Mammal_lifehistories_v2.txt
  nulls (separated by ';'): -999 ; 'NA'
  ...

For example, the `Adler et al. 2010`_. ``mapped-plant-quads-ks`` script uses -9999 to indicate null values.

.. _`Adler et al. 2010`: http://esapubs.org/archive/ecol/E088/161/

::

  ...
        {
            "dialect": {},
            "name": "quadrat_info",
            "schema": {},
            "url": "http://esapubs.org/archive/ecol/E088/161/quadrat_info.csv"
        },
        {
            "dialect": {
                "missingValues": [
                    "NA"
                ]
            },
  ...


Headers
-------

If the first row of a table is the headers then naming the columns will, be default, be handled automatically.
If you want to rename an existing header row for some reason, e.g.,
it includes reserved keywords for a database management system,
you can do so by adding a list of semi-colon separated column names,
with the new columns provided after a comma for each such column.

::

  ...
  Add Table? (y/N): y
  Table name: species
  Table URL: http://esapubs.org/archive/ecol/E091/124/TGPP_specodes.csv
  replace_columns (separated by ';', with comma-separated values): jan, january ; feb, february ; mar, march
  ...


The ``mapped-plant-quads-ks`` script for the `Adler et al. 2007`_. dataset from Ecological Archives
includes this functionality:


.. _`Adler et al. 2007`: http://esapubs.org/archive/ecol/E088/161/

::

  ...
   "name": "mapped-plant-quads-ks",
    "resources": [
        {
            "dialect": {},
            "name": "main",
            "schema": {},
            "url": "http://esapubs.org/archive/ecol/E088/161/allrecords.csv"
        },
        {
            "dialect": {},
            "name": "quadrat_info",
            "schema": {},
            "url": "http://esapubs.org/archive/ecol/E088/161/quadrat_info.csv"
        },
        {
            "dialect": {
                "missingValues": [
                    "NA"
                ]
            },
            "name": "quadrat_inventory",
            "schema": {},
            "url": "http://esapubs.org/archive/ecol/E088/161/quadrat_inventory.csv"
        },
        {
            "dialect": {},
            "name": "species",
            "schema": {},
            "url": "http://esapubs.org/archive/ecol/E088/161/species_list.csv"
        },
        {
            "dialect": {
                "missingValues": [
                    "NA"
                ],
                "replace_columns": [
                    [
                        "jan",
                        "january"
                    ],
                    [
                        "feb",
                        "february"
                    ],
                    [
                        "mar",
                        "march"
                    ],
                    [
                        "apr",
                        "april"
                    ],
                    [
                        "jun",
                        "june"
                    ],
                    [
                        "jul",
                        "july"
                    ],
                    [
                        "aug",
                        "august"
                    ],
                    [
                        "sep",
                        "september"
                    ],
                    [
                        "oct",
                        "october"
                    ],
                    [
                        "nov",
                        "november"
                    ],
                    [
                        "dec",
                        "december"
                    ]
                ]
            },
            "name": "monthly_temp",
            "schema": {},
            "url": "http://esapubs.org/archive/ecol/E088/161/monthly_temp.csv"
        },
    ...


Data Format
-----------

Data packages for different data formats can been added to Retriever now.
To add data format add keys in the script for Data sources except in the case of csv.

Data formats which can be added are :-

1. JSON Data :- For JSON raw data, add the key word ``json_data`` to the
resource. To add data formats for a given data package(`nuclear-power-plants`),
add keys to the resource part as described below.

::

  ...
   "name": "nuclear-power-plants",
    "resources": [
        {
            "dialect": {
                "delimiter": ","
            },
            "name": "nuclear_power_plants",
            "path": "nuclear_power_plants.csv",
            "json_data": "nuclear_power_plants.json",
            "schema": {
                "fields": [
                    {
                        "name": "id",
                        "type": "int"
                    },
                    {
                        "name": "name",
                        "size": "40",
                        "type": "char"
                    },
    ...

2. XML Data :- For XML raw data, add the key words ``xml_data`` and ``empty_rows``
to the resource. To add data formats for a given data package(`county-emergency-management-offices`), add keys to
the resource part as described below.

::

  ...
  "name": "county-emergency-management-offices",
    "resources": [
        {
            "dialect": {
                "delimiter": ","
            },
            "name": "county_emergency_management_offices",
            "path": "County_Emergency_Management_Offices.csv",
            "xml_data": "emergency_offices.xml",
            "empty_rows": 1,
            "schema": {
                "fields": [
                    {
                        "name": "county",
                        "size": "11",
                        "type": "char"
                    },
                    {
                        "name": "emergency_manager",
                        "size": "20",
                        "type": "char"
    ...

3. SQlite Data :- For SQlite raw data, add the key word ``sqlite_data`` to the
resource. To add data formats for a given data package(`portal-project-test`),
add keys to the resource part as described below.

::

  ...
   "name": "portal-project-test",
    "resources": [
        {
            "dialect": {
                "delimiter": ","
            },
            "name": "species",
            "path": "species.csv",
            "sqlite_data": ["species","portal_project.sqlite"],
            "schema": {
                "fields": [
                    {
                        "name": "species_id",
                        "size": "2",
                        "type": "char"
                    },
                    {
                        "name": "genus",
                        "size": "16",
                        "type": "char"
                    },
    ...


4. GeoJSON Data :- For GeoJSON raw data, add the key word ``geojson_data`` to the
resource. To add data formats for a given data package(`lake-county-illinois-cancer-rates`),
add keys to the resource part as described below.

::

  ...
   "name": "lake-county-illinois-cancer-rates",
    "resources": [
        {
            "dialect": {
                "delimiter": ","
            },
            "name": "lakecounty_health",
            "path": "LakeCounty_Health.csv",
            "format": "tabular",
            "geojson_data": "mytest.geojson",
            "schema": {
                "fields": [
                    {
                        "name": "fid",
                        "type": "int"
                    },
                    {
                        "name": "zip",
                        "type": "int"
                    },

    ...

5. HDF5 Data :- For HDF5 raw data, add the key word ``hdf5_data`` to the
resource. To add data formats for a given data package(`sample-hdf`),
add keys to the resource part as described below.

::

  ...
   "name": "sample-hdf",
  "title": "Test data raster bio1",
  "description": "Test data sampled from bioclim bio1",
  "citation": "N/A",
  "keywords": [
    "tests"
  ],
  "encoding": "latin-1",
  "url": "https://github.com/ashishpriyadarshiCIC/My_data/raw/main/Test_table_image_data.h5",
  "ref": "N/A",
  "version": "1.0.0",
  "retriever_minimum_version": "2.1.dev",
  "driver": "GTiff",
  "colums": 284,
  "rows": 249,
  "band_count": 1,
  "datum": "N/A - Coordinate Reference System",
  "projection": "GEOGCS[\"WGS 84\",DATUM[\"WGS_1984\",SPHEROID[\"WGS 84\",6378137,298.257223563,AUTHORITY[\"EPSG\",\"7030\"]],AUTHORITY[\"EPSG\",\"6326\"]],PRIMEM[\"Greenwich\",0],UNIT[\"degree\",0.0174532925199433],AUTHORITY[\"EPSG\",\"4326\"]]",
  "file_size": "N/A",
  "group_count": "N/A",
  "dataset_count": "N/A",
  "transform": {
    "xOrigin": -121.6250000000029,
    "pixelWidth": 0.041666666666667,
    "rotation_2": 0.0,
    "yOrigin": 42.79166666666632,
    "rotation_4": 0.0,
    "pixelHeight": -0.041666666666667
  },
  "resources": [
    {
      "dialect": {
        "delimiter": ","
      },
      "name": "table_data",
      "path": "table_data.csv",
      "hdf5_data": [
        "Test_table_image_data.h5",
        "csv",
        "G1/table_data"
      ],
      "schema": {
        "fields": [
          {
            "name": "region",
            "size": "33",
            "type": "char"
          },
          {
            "name": "country",
            "size": "32",
            "type": "char"
          },
          {
            "name": "item_type",
            "size": "15",
            "type": "char"
          },
          {
            "name": "sales_channel",
            "size": "7",
            "type": "char"
          },
          {
            "name": "order_id",
            "type": "int"
          },
          {
            "name": "total_profit",
            "type": "double"
          }
        ]
      },
      "url": "https://github.com/ashishpriyadarshiCIC/My_data/raw/main/Test_table_image_data.h5"
    },
    {
      "name": "test_image",
      "format": "raster",
      "hdf5_data": [
        "Test_table_image_data.h5",
        "image",
        "G1/G2/im"
      ],
      "path": "test_raster_bio1.tif",
      "extensions": [
        "tif"
      ],
      "no_data_value": -9999.0,
      "min": null,
      "max": null,
      "scale": 1.0,
      "color_table": null,
      "statistics": {
        "minimum": 0.0,
        "maximum": 0.0,
        "mean": 0.0,
        "stddev": -1.0
      },
    ...


Full control over column names and data types
---------------------------------------------

By default the Retriever automatically detects both column names and data types, but you can also exercise complete
control over the structure of the resulting database by adding column names and types.

It is recommended to describe the schema of the table while creating the JSON file. This enables processing of the data faster since column detection increases the processing time.

These values are stored in the ``fields`` array of the ``schema`` dict of the JSON script.

The ``fields`` value enables full control of the columns, which includes, renaming columns, skipping unwanted columns, mentioning primary key and combining columns.

The basic format for ``fields`` is as shown below:

::

  ...
  Enter columns [format = name, type, (optional) size]:

  count, int
  name, char, 40
  year, int
  ...


where ``name`` represents name of the column and ``type`` represents the type of data present in the column. The following can be used to describe the data type:

::

  pk-auto: Auto generated primary key starting from 1
  pk-[char,int,double]: primary key with data type
  char: strings
  int: integers
  double:floats/decimals
  ct-[int,double,char]:Cross tab data
  skip: used to skip the column in database


``pk-auto`` is used to create an additional column of type int which acts as a primary key with values starting from 1. While ``pk-[char,int,double]`` is used to make a primary key from existing columns of the table having data type of char/int/double.

The Smith et al. Masses of Mammals ``mammal-masses`` dataset script includes this type of functionality.

::

  ...
     "name": "mammal-masses",
    "resources": [
        {
            "dialect": {
                "missingValues": [
                    -999
                ],
                "header_rows": 0
            },
            "name": "MammalMasses",
            "schema": {
                "fields": [
                    {
                        "name": "record_id",
                        "type": "pk-auto"
                    },
                    {
                        "name": "continent",
                        "size": "20",
                        "type": "char"
                    },
                    {
                        "name": "status",
                        "size": "20",
                        "type": "char"
                    },
                    {
                        "name": "sporder",
                        "size": "20",
                        "type": "char"
                    },
                    {
                        "name": "family",
                        "size": "20",
                        "type": "char"
                    },
                    {
                        "name": "genus",
                        "size": "20",
                        "type": "char"
                    },
                    {
                        "name": "species",
                        "size": "20",
                        "type": "char"
                    },
                    {
                        "name": "log_mass_g",
                        "type": "double"
                    },
                    {
                        "name": "comb_mass_g",
                        "type": "double"
                    },
                    {
                        "name": "reference",
                        "type": "char"
                    }
                ]
            },
            "url": "http://www.esapubs.org/Archive/ecol/E084/094/MOMv3.3.txt"
        }
    ],
    "retriever": "True",
    "retriever_minimum_version": "2.0.dev",
    "title": "Masses of Mammals (Smith et al. 2003)",
  ...

Restructuring cross-tab data
----------------------------

It is common in ecology to see data where the rows indicate one level of grouping (e.g., by site),
the columns indicate another level of grouping (e.g., by species), and the values in each cell indicate
the value for the group indicated by the row and column (e.g., the abundance of species x at site y).
This is referred as cross-tab data and cannot be easily handled by database management systems,
which are based on a one record per line structure. The Retriever can restructure this type of
data into the appropriate form.
In scripts this involves telling the retriever the name of the column to store the data in
and the names of the columns to be restructured.

::

  ...
  Add crosstab columns? (y,N): y
  Crosstab column name: <name of column to store cross-tab data>
  Enter names of crosstab column values (Press return after each name):

  ct column 1
  ct column 2
  ct column 3
  ...

The `Moral et al 2010 script`_. ``mt-st-helens-veg`` takes advantage of this functionality.

.. _`Moral et al 2010 script`: http://esapubs.org/archive/ecol/E091/152/

::

  ...
  "name": "mt-st-helens-veg",
    "resources": [
        {
            "dialect": {
                "delimiter": ","
            },
            "name": "species_plot_year",
            "schema": {
                "ct_column": "species",
                "ct_names": [
                    "Abilas",
                    "Abipro",
                    "Achmil",
                    "Achocc",
                    "Agoaur",
                    "Agrexa",
                    "Agrpal",
                    "Agrsca",
                    "Alnvir",
                    "Anamar",
                    "Antmic",
                    "Antros",
                    "Aqifor",
                    "Arcnev",
                    "Arnlat",
                    "Astled",
                    "Athdis",
                    "Blespi",
                    "Brocar",
                    "Brosit",
                    "Carmer",
                    "Carmic",
                    "Carpac",
                    "Carpay",
                    "Carpha",
                    "Carros",
                    "Carspe",
                    "Casmin",
                    "Chaang",
                    "Cirarv",
                    "Cisumb",
                    "Crycas",
                    "Danint",
                    "Descae",
                    "Elyely",
                    "Epiana",
                    "Eriova",
                    "Eripyr",
                    "Fesocc",
                    "Fravir",
                    "Gencal",
                    "Hiealb",
                    "Hiegra",
                    "Hyprad",
                    "Junmer",
                    "Junpar",
                    "Juncom",
                    "Leppun",
                    "Lommar",
                    "Luepec",
                    "Luihyp",
                    "Luplat",
                    "Luplep",
                    "Luzpar",
                    "Maiste",
                    "Pencar",
                    "Pencon",
                    "Penser",
                    "Phahas",
                    "Phlalp",
                    "Phldif",
                    "Phyemp",
                    "Pincon",
                    "Poasec",
                    "Poldav",
                    "Polmin",
                    "Pollon",
                    "Poljun",
                    "Popbal",
                    "Potarg",
                    "Psemen",
                    "Raccan",
                    "Rumace",
                    "Salsit",
                    "Saxfer",
                    "Senspp",
                    "Sibpro",
                    "Sorsit",
                    "Spiden",
                    "Trispi",
                    "Tsumer",
                    "Vacmem",
                    "Vervir",
                    "Vioadu",
                    "Xerten"
                ],
                "fields": [
                    {
                        "name": "record_id",
                        "type": "pk-auto"
                    },
                    {
                        "name": "plot_id_year",
                        "size": "20",
                        "type": "char"
                    },
                    {
                        "name": "plot_name",
                        "size": "4",
                        "type": "char"
                    },
                    {
                        "name": "plot_number",
                        "type": "int"
                    },
                    {
                        "name": "year",
                        "type": "int"
                    },
                    {
                        "name": "count",
                        "type": "ct-double"
                    }
                ]
            },
            "url": "http://esapubs.org/archive/ecol/E091/152/MSH_SPECIES_PLOT_YEAR.csv"
        },
  ...

Script Editing
--------------
**Note:** Any time a script gets updated, the minor version number must be incremented from within the script.

The JSON scripts created using the retriever-recipes CLI can also be edited using the CLI.

To edit a script, use the ``retriever-recipes edit_json`` command, followed by the script's shortname;

For example, editing the ``mammal-life-hist`` (Mammal Life History Database - Ernest, et al., 2003)
dataset, the editing tool will ask a series a questions for each of the keys and values of the script,
and act according to the input.


The tool describes the values you want to edit.
In the script below the first keyword is citation, ``citation ( <class 'str'> )``
and it is of class string or expects a string.

::

  dev@retriever:~$ retriever-recipes edit_json mammal-life-hist

    ->citation ( <class 'str'> ) :

    S. K. Morgan Ernest. 2003. Life history characteristics of placental non-volant mammals. Ecology 84:3402

    Select one of the following for the key 'citation'

    1. Modify value
    2. Remove from script
    3. Continue (no changes)


    Your choice: 3

      ->homepage ( <class 'str'> ) :

      http://esapubs.org/archive/ecol/E084/093/


    Select one of the following for the key 'homepage':

    1. Modify value
    2. Remove from script
    3. Continue (no changes)


    Your choice: 3

      ->description ( <class 'str'> ) :

      The purpose of this data set was to compile general life history characteristics for a variety of mammalian
      species to perform comparative life history analyses among different taxa and different body size groups.


    Select one of the following for the key 'description':

    1. Modify value
    2. Remove from script
    3. Continue (no changes)


    Your choice: 3

      ->retriever_minimum_version ( <class 'str'> ) :

      2.0.dev


    Select one of the following for the key 'retriever_minimum_version':

    1. Modify value
    2. Remove from script
    3. Continue (no changes)


    Your choice: 3

      ->version ( <class 'str'> ) :

      1.1.0


    Select one of the following for the key 'version':

    1. Modify value
    2. Remove from script
    3. Continue (no changes)


    Your choice: 3

      ->resources ( <class 'list'> ) :

      {'dialect': {}, 'schema': {}, 'name': 'species', 'url': 'http://esapubs.org/archive/ecol/E084/093/Mammal_lifehistories_v2.txt'}


    1 .  {'dialect': {}, 'schema': {}, 'name': 'species', 'url': 'http://esapubs.org/archive/ecol/E084/093/Mammal_lifehistories_v2.txt'}

    Edit this dict in 'resources'? (y/N): n
    Select one of the following for the key 'resources':

    1. Add an item
    2. Delete an item
    3. Remove from script
    4. Continue (no changes)
    ...
