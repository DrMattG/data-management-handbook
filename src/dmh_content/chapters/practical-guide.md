Practical Guides
================

This chapter includes how-to’s and other practical guidance for data producers.

Create a Data Management Plan (DMP)
-----------------------------------

The funding agency of your project will usually provide requirements, guidelines or a template for the DMP. If this is not the case or for datasets that are not part of a project use the template provided by your institution or the template based on the [recommendations by Science Europe](https://www.forskningsradet.no/contentassets/e4cd6d2c23cf49d4989bb10c5eea087a/se_rdm_practical_guide_final.pdf).

### Using easyDMP

1.  Log in to [easyDMP](https://easydmp.sigma2.no/login/), use **Dataporten** if your institution supports that, otherwise pick one of the other login methods.

2.  Click on **+ Create a new plan** and pick a template

3.  By using the **Summary** button from page two and on, you can get an overview of all the questions.

### Publishing the plan

Currently you can use the *export* function in easyDMP to download an HTML or PDF version of the DMP and use it further. This might change if "[Hosted DMP](https://github.com/hmpf/easydmp/issues/226)" gets implemented.

Submitting data as NetCDF-CF
----------------------------

### Workflow

1.  Define your dataset (see [dataset](#glossary-dataset) and [???](#dataset))

2.  Create a NetCDF-CF file (see [Creating NetCDF-CF files](#data-as-netcdf-cf))

    -   Add discovery metadata as global attributes (see [global attibutes section](#acdd-elements))

    -   Add variables and [use metadata](#glossary-use-metadata) following the [CF conventions](https://cfconventions.org/)

3.  Store the NetCDF-CF file in a suitable location, and distribute it via thredds or another dap server (see, e.g., [How to add NetCDF-CF data to thredds](#add-to-thredds))

4.  Register your dataset in a searchable catalog (see [How to register your data in the catalog service](#register-dataset-in-catalog))

    -   Test that your dataset contains the necessary discovery metadata and create an MMD xml file (see [Generation of MMD xml file from NetCDF-CF](#local-mmd-xml-generation))

    -   Test the MMD xml file (see [Test the MMD xml file](#test-mmd-file))

    -   Push the MMD xml file to the discovery metadata catalog (see [Push the MMD xml file to the discovery metadata catalog](#push-mmd-file))

### Creating NetCDF-CF files

By documenting and formatting your data using [NetCDF](#netcdf) following the [CF conventions](https://cfconventions.org/) and the [Attribute Convention for Data Discovery (ACDD)](https://wiki.esipfed.org/Attribute_Convention_for_Data_Discovery_1-3), [MMD](#mmd) files can be automatically generated from the [NetCDF](#netcdf) files. The [CF conventions](#cf) is a controlled vocabulary providing a definitive description of what the data in each variable represents, and the spatial and temporal properties of the data. The [ACDD](#acdd) vocabulary describes attributes recommended for describing a [NetCDF](#netcdf) dataset to data discovery systems. See, e.g., [netCDF4-python docs](https://unidata.github.io/netcdf4-python/), or [xarray docs](http://xarray.pydata.org/en/stable/user-guide/io.html) for documentation about how to create netCDF files.

The ACDD recommendations should be followed in order to properly document your netCDF-CF files. The below tables summarize required and recommended ACDD and some additional attributes that are needed to properly populate a discovery metadata catalog which fulfills the requirements of international standards (e.g., GCMD/DIF, the INSPIRE and WMO profiles of ISO19115, etc.).

#### Notes

**Keywords** describe the content of your dataset following a given vocabulary. You may use any vocabularies to define your keywords, but a link to the keyword definitions should be provided in the \`\`keywords\_vocabulary\`\` attribute. This attribute provides information about the vocabulary defining the keywords used in the \`\`keywords\`\` attribute. Example:

:keywords\_vocabulary = "GCMDSK:GCMD Science Keywords:https://gcmd.earthdata.nasa.gov/kms/concepts/concept\_scheme/sciencekeywords, GEMET:INSPIRE Themes:http://inspire.ec.europa.eu/theme, NORTHEMES:GeoNorge Themes:https://register.geonorge.no/metadata-kodelister/nasjonal-temainndeling" ;

Note that the **GCMDSK**, **GEMET** and **NORTHEMES** vocabularies are required for indexing in [S-ENDA](https://adc.met.no/) and [Geonorge](https://www.geonorge.no/en/). You may find appropriate keywords at the following links:

-   [GCMDSK](https://gcmd.earthdata.nasa.gov/kms/concepts/concept_scheme/sciencekeywords)

-   [GEMET](http://inspire.ec.europa.eu/theme)

-   [NORTHEMES](https://register.geonorge.no/metadata-kodelister/nasjonal-temainndeling)

The keywords should be provided by the \`\`keywords\`\` attribute as a comma separated list with a short name defining the vocabulary used, followed by the actual keyword, i.e., \`\`short\_name:keyword\`\`. Example:

:keywords = "GCMDSK:Earth Science &gt; Atmosphere &gt; Atmospheric radiation, GEMET:Meteorological geographical features, GEMET:Atmospheric conditions, NORTHEMES:Weather and climate" ;

See <https://adc.met.no/node/96> for more information about how to define the ACDD keywords.

A data **license** provides information about any restrictions on the use of the dataset. To support a linked data approach, the \`\`license\`\` element should be supported by a \`\`license\_resource\`\` element, providing a link to the license definition. Example:

:license = "CC-BY-4.0" ;  
:license\_resource = "http://spdx.org/licenses/CC-BY-4.0" ;

#### List of Attributes

This section provides lists of ACDD elements that are required and recommended, as well as some extra elements that are needed to fully support our data management needs. The right columns of these tables provide the [MET Norway Metadata Specification (MMD)](https://htmlpreview.github.io/?https://github.com/metno/mmd/blob/master/doc/mmd-specification.html) fields that map to the ACDD (and our extension to ACDD) elements. Please refer to [MMD](https://htmlpreview.github.io/?https://github.com/metno/mmd/blob/master/doc/mmd-specification.html) for definitions of these elements, as well as controlled vocabularies that should be used. Note that the below tables are automatically generated - check <https://github.com/metno/py-mmd-tools/blob/master/py_mmd_tools/mmd_elements.yaml> if anything is unclear.

In order to check your netCDF-CF files, and to create MMD xml files, you can use the nc2mmd.py script in the [py-mmd-tools](https://github.com/metno/py-mmd-tools) Python package.

The following ACDD elements are required:

<table><colgroup><col style="width: 33%" /><col style="width: 33%" /><col style="width: 33%" /></colgroup><tbody><tr class="odd"><td><p><strong>ACDD Attribute</strong></p></td><td><p><strong>MMD equivalent</strong></p></td><td><p><strong>Comment</strong></p></td></tr><tr class="even"><td><p>id</p></td><td><p>metadata_identifier</p></td><td><p>Required, and should be UUID. No repetition allowed.</p></td></tr><tr class="odd"><td><p>naming_authority</p></td><td><p>metadata_identifier</p></td><td><p>Required. We recommend using reverse-DNS naming. No repetition allowed.</p></td></tr><tr class="even"><td><p>date_created</p></td><td><p>last_metadata_update&gt;update&gt;datetime</p></td><td><p>Format as ISO8601.</p></td></tr><tr class="odd"><td><p>title</p></td><td><p>title&gt;title</p></td><td><p>Use ACDD extension "title_no" for Norwegian translation.</p></td></tr><tr class="even"><td><p>summary</p></td><td><p>abstract&gt;abstract</p></td><td><p>Use ACDD extension "summary_no" for Norwegian translation.</p></td></tr><tr class="odd"><td><p>time_coverage_start</p></td><td><p>temporal_extent&gt;start_date</p></td><td><p>Comma separated list.</p></td></tr><tr class="even"><td><p>geospatial_lat_max</p></td><td><p>geographic_extent&gt;rectangle&gt;north</p></td><td><p>No repetition allowed.</p></td></tr><tr class="odd"><td><p>geospatial_lat_min</p></td><td><p>geographic_extent&gt;rectangle&gt;south</p></td><td><p>No repetition allowed.</p></td></tr><tr class="even"><td><p>geospatial_lon_max</p></td><td><p>geographic_extent&gt;rectangle&gt;east</p></td><td><p>No repetition allowed.</p></td></tr><tr class="odd"><td><p>geospatial_lon_min</p></td><td><p>geographic_extent&gt;rectangle&gt;west</p></td><td><p>No repetition allowed.</p></td></tr><tr class="even"><td><p>keywords</p></td><td><p>keywords&gt;keyword</p></td><td><p>Comma separated list.</p></td></tr><tr class="odd"><td><p>keywords_vocabulary</p></td><td><p>keywords&gt;vocabulary</p></td><td><p>Comma separated list.</p></td></tr></tbody></table>

The following ACDD elements are (highly) recommended:

<table><colgroup><col style="width: 25%" /><col style="width: 25%" /><col style="width: 25%" /><col style="width: 25%" /></colgroup><tbody><tr class="odd"><td><p><strong>ACDD Attribute</strong></p></td><td><p><strong>Default</strong></p></td><td><p><strong>MMD equivalent</strong></p></td><td><p><strong>Comment</strong></p></td></tr><tr class="even"><td><p>date_metadata_modified</p></td><td></td><td><p>last_metadata_update&gt;update&gt;datetime</p></td><td><p>Format as ISO8601. Comma separated list if more than once.</p></td></tr><tr class="odd"><td><p>time_coverage_end</p></td><td></td><td><p>temporal_extent&gt;end_date</p></td><td><p>Comma separated list.</p></td></tr><tr class="even"><td><p>geospatial_bounds</p></td><td></td><td><p>geographic_extent&gt;polygon</p></td><td><p>No repetition allowed.</p></td></tr><tr class="odd"><td><p>processing_level</p></td><td></td><td><p>operational_status</p></td><td><p>No repetition allowed. See the MMD docs for valid keywords.</p></td></tr><tr class="even"><td><p>license</p></td><td></td><td><p>use_constraint&gt;identifier</p></td><td><p>No repetition allowed.</p></td></tr><tr class="odd"><td><p>creator_role</p></td><td><p>Investigator</p></td><td><p>personnel&gt;role</p></td><td><p>Comma separated list.</p></td></tr><tr class="even"><td><p>contributor_role</p></td><td><p>Investigator</p></td><td><p>personnel&gt;role</p></td><td><p>Comma separated list.</p></td></tr><tr class="odd"><td><p>creator_name</p></td><td><p>Not available</p></td><td><p>personnel&gt;name</p></td><td><p>Comma separated list.</p></td></tr><tr class="even"><td><p>contributor_name</p></td><td><p>Not available</p></td><td><p>personnel&gt;name</p></td><td><p>Comma separated list.</p></td></tr><tr class="odd"><td><p>creator_email</p></td><td><p>Not available</p></td><td><p>personnel&gt;email</p></td><td><p>Comma separated list.</p></td></tr><tr class="even"><td><p>creator_institution</p></td><td><p>Not available</p></td><td><p>personnel&gt;organisation</p></td><td><p>Comma separated list.</p></td></tr><tr class="odd"><td><p>institution</p></td><td></td><td><p>data_center&gt;data_center_name&gt;long_name</p></td><td><p>Comma separated list.</p></td></tr><tr class="even"><td><p>publisher_url</p></td><td></td><td><p>data_center&gt;data_center_url</p></td><td><p>Comma separated list.</p></td></tr><tr class="odd"><td><p>project</p></td><td></td><td><p>project&gt;long_name</p></td><td><p>Semicolon separated list.</p></td></tr><tr class="even"><td><p>platform</p></td><td></td><td><p>platform&gt;long_name</p></td><td><p>Comma separated list.</p></td></tr><tr class="odd"><td><p>platform_vocabulary</p></td><td></td><td><p>platform&gt;resource</p></td><td><p>Comma separated list.</p></td></tr><tr class="even"><td><p>instrument</p></td><td></td><td><p>platform&gt;instrument&gt;long_name</p></td><td><p>Comma separated list.</p></td></tr><tr class="odd"><td><p>instrument_vocabulary</p></td><td></td><td><p>platform&gt;instrument&gt;resource</p></td><td><p>Comma separated list.</p></td></tr><tr class="even"><td><p>source</p></td><td></td><td><p>activity_type</p></td><td><p>Semicolon separated list.</p></td></tr><tr class="odd"><td><p>creator_name</p></td><td></td><td><p>dataset_citation&gt;author</p></td><td><p>Comma separated list.</p></td></tr><tr class="even"><td><p>date_created</p></td><td></td><td><p>dataset_citation&gt;publication_date</p></td><td><p>Comma separated list.</p></td></tr><tr class="odd"><td><p>title</p></td><td></td><td><p>dataset_citation&gt;title</p></td><td></td></tr><tr class="even"><td><p>publisher_name</p></td><td></td><td><p>dataset_citation&gt;publisher</p></td><td><p>Comma separated list.</p></td></tr><tr class="odd"><td><p>metadata_link</p></td><td></td><td><p>dataset_citation&gt;url</p></td><td><p>Comma separated list.</p></td></tr><tr class="even"><td><p>references</p></td><td></td><td><p>dataset_citation&gt;other</p></td><td><p>Comma separated list.</p></td></tr></tbody></table>

The following elements are ACDD extensions that are needed to improve (meta)data interoperability. Please refer to the documentation of [MMD](https://htmlpreview.github.io/?https://github.com/metno/mmd/blob/master/doc/mmd-specification.html) for more details:

<table><colgroup><col style="width: 25%" /><col style="width: 25%" /><col style="width: 25%" /><col style="width: 25%" /></colgroup><tbody><tr class="odd"><td><p><strong>Necessary non-ACDD Attribute</strong></p></td><td><p><strong>Default</strong></p></td><td><p><strong>MMD equivalent</strong></p></td><td><p><strong>Comment</strong></p></td></tr><tr class="even"><td><p>spatial_representation</p></td><td></td><td><p>spatial_representation</p></td><td><p>No repetition allowed.</p></td></tr><tr class="odd"><td><p>alternate_identifier</p></td><td></td><td><p>alternate_identifier&gt;alternate_identifier</p></td><td><p>Alternative identifier for the dataset (but not DOI). Comma separated list.</p></td></tr><tr class="even"><td><p>alternate_identifier_type</p></td><td></td><td><p>alternate_identifier&gt;type</p></td><td><p>Identification of the type of identifier used. Comma separated list.</p></td></tr><tr class="odd"><td><p>date_metadata_modified_type</p></td><td></td><td><p>last_metadata_update&gt;update&gt;type</p></td><td><p>E.g., major or minor modification. Comma separated list.</p></td></tr><tr class="even"><td><p>date_created_type</p></td><td><p>Created</p></td><td><p>last_metadata_update&gt;update&gt;type</p></td><td></td></tr><tr class="odd"><td><p>title_no</p></td><td></td><td><p>title&gt;title</p></td><td><p>Used for Norwegian version of the title.</p></td></tr><tr class="even"><td><p>title_lang</p></td><td><p>en</p></td><td><p>title&gt;lang</p></td><td><p>ISO language code.</p></td></tr><tr class="odd"><td><p>summary_no</p></td><td></td><td><p>abstract&gt;abstract</p></td><td><p>Used for Norwegian version of the abstract.</p></td></tr><tr class="even"><td><p>summary_lang</p></td><td><p>en</p></td><td><p>abstract&gt;lang</p></td><td><p>ISO language code.</p></td></tr><tr class="odd"><td><p>dataset_production_status</p></td><td><p>Complete</p></td><td><p>dataset_production_status</p></td><td><p>No repetition allowed.</p></td></tr><tr class="even"><td><p>access_constraint</p></td><td></td><td><p>access_constraint</p></td><td><p>No repetition allowed.</p></td></tr><tr class="odd"><td><p>license_resource</p></td><td></td><td><p>use_constraint&gt;resource</p></td><td><p>No repetition allowed.</p></td></tr><tr class="even"><td><p>contributor_email</p></td><td><p>Not available</p></td><td><p>personnel&gt;email</p></td><td><p>Comma separated list.</p></td></tr><tr class="odd"><td><p>contributor_institution</p></td><td></td><td><p>personnel&gt;organisation</p></td><td></td></tr><tr class="even"><td><p>contributor_organisation</p></td><td></td><td><p>personnel&gt;organisation</p></td><td></td></tr><tr class="odd"><td><p>institution_short_name</p></td><td></td><td><p>data_center&gt;data_center_name&gt;short_name</p></td><td><p>Comma separated list.</p></td></tr><tr class="even"><td><p>related_dataset_id</p></td><td></td><td><p>related_dataset&gt;related_dataset</p></td><td><p>Comma separated list.</p></td></tr><tr class="odd"><td><p>related_dataset_relation_type</p></td><td></td><td><p>related_dataset&gt;relation_type</p></td><td><p>Comma separated list.</p></td></tr><tr class="even"><td><p>iso_topic_category</p></td><td></td><td><p>iso_topic_category</p></td><td><p>Comma separated list.</p></td></tr><tr class="odd"><td><p>project_short_name</p></td><td></td><td><p>project&gt;short_name</p></td><td><p>Semicolon separated list.</p></td></tr><tr class="even"><td><p>quality_control</p></td><td></td><td><p>quality_control</p></td><td><p>No repetition allowed.</p></td></tr><tr class="odd"><td><p>doi</p></td><td></td><td><p>dataset_citation&gt;doi</p></td><td></td></tr></tbody></table>

### How to add NetCDF-CF data to thredds

This section should contain institution specific information about how to add netcdf-cf files to thredds.

### How to register your data in the catalog service

In order to make a dataset findable, a dataset must be registered in a searchable catalog with appropriate metadata. The (meta)data catalog is indexed and exposed through [CSW](https://en.wikipedia.org/wiki/Catalogue_Service_for_the_Web).

The following needs to be done:

1.  Generate an MMD xml file from your NetCDF-CF file (see [Generation of MMD xml file from NetCDF-CF](#local-mmd-xml-generation))

2.  Test your mmd xml metadata file (see [Test the MMD xml file](#test-mmd-file))

3.  Push the MMD xml file to the discovery metadata catalog (see [Push the MMD xml file to the discovery metadata catalog](#push-mmd-file))

#### Generation of MMD xml file from NetCDF-CF

Clone the [py-mmd-tools](https://github.com/metno/py-mmd-tools.git) repo and make a local installation with eg `pip install .`. This should bring in all needed dependencies (we recommend to use a virtual environment).

Then, generate your mmd xml file as follows:

cd script ./nc2mmd.py -i &lt;your netcdf file&gt; -o &lt;your xml output directory&gt;&lt;/programlisting&gt;

See `./nc2mmd.py --help` for documentation and extra options.

You will find Extensible Stylesheet Language Transformations (XSLT) documents in the [MMD](https://github.com/metno/mmd.git) repository. These can be used to translate the metadata documents from MMD to other vocabularies, such as ISO19115:

./bin/convert\_from\_mmd -i &lt;your mmd xml file&gt; -f iso -o &lt;your iso output file name&gt;&lt;/programlisting&gt;

Note that the discovery metadata catalog ingestion tool will take care of translations from MMD, so you don’t need to worry about that unless you have special interest in it.

#### Test the MMD xml file

Install the [dmci app](https://github.com/metno/discovery-metadata-catalog-ingestor), and run the usage example locally. This will return an error message if anything is wrong with your MMD file.

#### Push the MMD xml file to the discovery metadata catalog

For development and verification purposes:

curl --data-binary "@&lt;PATH\_TO\_MMD\_FILE&gt;" https://dmci-\*.s-enda.k8s.met.no/v1/insert&lt;/programlisting&gt;

where `*` should be either dev or staging.

For production (the official catalog):

curl --data-binary "@&lt;PATH\_TO\_MMD\_FILE&gt;" https://dmci.s-enda.k8s.met.no/v1/insert&lt;/programlisting&gt;

Searching data in the Catalog Service for the Web (CSW) interface
-----------------------------------------------------------------

### Using OpenSearch

#### Local test machines

The [`vagrant-s-enda`](https://github.com/metno/vagrant-s-enda) environment found at [vagrant-s-enda](https://github.com/metno/vagrant-s-enda) provides OpenSearch support through [PyCSW](https://github.com/geopython/pycsw). To test OpenSearch via the browser, start the `vagrant-s-enda` vm (`vagrant up`) and go to the following address:

-   `http://10.10.10.10/pycsw/csw.py?mode=opensearch&service=CSW&version=2.0.2&request=GetCapabilities`

#### Online catalog

For searching the online metadata catalog, the base url (`http://10.10.10.10/`) must be replaced by `https://csw.s-enda.k8s.met.no/`:

-   `http://csw.s-enda.k8s.met.no/?mode=opensearch&service=CSW&version=2.0.2&request=GetRecords&elementsetname=full&typenames=csw:Record&resulttype=results`

#### OpenSearch examples

To find all datasets in the catalog:

-   `https://csw.s-enda.k8s.met.no/?mode=opensearch&service=CSW&version=2.0.2&request=GetRecords&elementsetname=full&typenames=csw:Record&resulttype=results`

Or datasets within a given time span:

-   `http://csw.s-enda.k8s.met.no/?mode=opensearch&service=CSW&version=2.0.2&request=GetRecords&elementsetname=full&typenames=csw:Record&resulttype=results&time=2000-01-01/2020-09-01`

Or datasets within a geographical domain (defined as a box with parameters min\_longitude, min\_latitude, max\_longitude, max\_latitude):

-   `https://csw.s-enda.k8s.met.no/?mode=opensearch&service=CSW&version=2.0.2&request=GetRecords&elementsetname=full&typenames=csw:Record&resulttype=results&bbox=0,40,10,60`

Or, datasets from any of the Sentinel satellites:

-   `https://csw.s-enda.k8s.met.no/?mode=opensearch&service=CSW&version=2.0.2&request=GetRecords&elementsetname=full&typenames=csw:Record&resulttype=results&q=sentinel`

### Advanced geographical search

PyCSW opensearch only supports geographical searches querying for a box. For more advanced geographical searches, one must write specific XML files. For example:

-   To find all datasets containing a point:

<!-- -->

    <?xml version="1.0" encoding="ISO-8859-1" standalone="no"?>
    <csw:GetRecords
        xmlns:csw="http://www.opengis.net/cat/csw/2.0.2"
        xmlns:ogc="http://www.opengis.net/ogc"
        xmlns:gml="http://www.opengis.net/gml"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        service="CSW"
        version="2.0.2"
        resultType="results"
        maxRecords="10"
        outputFormat="application/xml"
        outputSchema="http://www.opengis.net/cat/csw/2.0.2"
        xsi:schemaLocation="http://www.opengis.net/cat/csw/2.0.2 http://schemas.opengis.net/csw/2.0.2/CSW-discovery.xsd" >
      <csw:Query typeNames="csw:Record">
        <csw:ElementSetName>full</csw:ElementSetName>
        <csw:Constraint version="1.1.0">
          <ogc:Filter>
            <ogc:Contains>
              <ogc:PropertyName>ows:BoundingBox</ogc:PropertyName>
              <gml:Point>
                <gml:pos srsDimension="2">59.0 4.0</gml:pos>
              </gml:Point>
            </ogc:Contains>
          </ogc:Filter>
        </csw:Constraint>
      </csw:Query>
    </csw:GetRecords>

-   To find all datasets intersecting a polygon:

<!-- -->

    <?xml version="1.0" encoding="ISO-8859-1" standalone="no"?>
    <csw:GetRecords
        xmlns:csw="http://www.opengis.net/cat/csw/2.0.2"
        xmlns:gml="http://www.opengis.net/gml"
        xmlns:ogc="http://www.opengis.net/ogc"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        service="CSW"
        version="2.0.2"
        resultType="results"
        maxRecords="10"
        outputFormat="application/xml"
        outputSchema="http://www.opengis.net/cat/csw/2.0.2"
        xsi:schemaLocation="http://www.opengis.net/cat/csw/2.0.2 http://schemas.opengis.net/csw/2.0.2/CSW-discovery.xsd" >
      <csw:Query typeNames="csw:Record">
        <csw:ElementSetName>full</csw:ElementSetName>
        <csw:Constraint version="1.1.0">
          <ogc:Filter>
            <ogc:Intersects>
              <ogc:PropertyName>ows:BoundingBox</ogc:PropertyName>
              <gml:Polygon>
                <gml:exterior>
                  <gml:LinearRing>
                    <gml:posList>
                      47.00 -5.00 55.00 -5.00 55.00 20.00 47.00 20.00 47.00 -5.00
                    </gml:posList>
                  </gml:LinearRing>
                </gml:exterior>
              </gml:Polygon>
            </ogc:Intersects>
          </ogc:Filter>
        </csw:Constraint>
      </csw:Query>
    </csw:GetRecords>

-   Then, you can query the CSW endpoint with, e.g., python:

<!-- -->

    import requests
    requests.post('https://csw.s-enda.k8s.met.no', data=open(my_xml_request).read()).text

### Web portals

**GeoNorge.no**

**TODO:** describe how to search in geonorge, possibly with screenshots

### QGIS

MET Norway’s S-ENDA CSW catalog service is available at `https://csw.s-enda.k8s.met.no`. This can be used from QGIS as follows:

1.  Select `Web > MetaSearch > MetaSearch` menu item

2.  Select `Services > New`

3.  Type, e.g., `csw.s-enda.k8s.met.no` for the name

4.  Type `https://csw.s-enda.k8s.met.no` for the URL

Under the `Search` tab, you can then add search parameters, click `Search`, and get a list of available datasets.