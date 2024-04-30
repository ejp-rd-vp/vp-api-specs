# REST API specification for querying RD resources (Virtual Platform Level 2)

> This API specification is defined in the context of the EJPRD project, complying with the latest [Beacon v2 Specification](https://github.com/ga4gh-beacon/beacon-v2).

In this work, we present API specification for querying RD patient registries, biobanks and similar resources at the safe record level (i.e, resources whose available assets are described by RD patient data). Resources that implement this specification would ideally collect data based on the set of common data elements for rare diseases registration, as recommended by the European commission Joint Research Centre. In this specification, where possible, we also make use of ontological terms recommended by the CDE semantic data model group.

<hr>

<h2 id="top"> Contents </h2>

* [Try out this API in Swagger](#-try-out-the-api-)
* [Specification](#-specification-)
* [Query Endpoints](#-query-endpoints-)
    * [Individuals endpoint](#-individuals-endpoint)
      * [List of filters](#-list-of-filters-and-permitted-values-for-the-individuals-endpoint-)
      * [Filters description](#-individuals-filters-description-)
      * [Example request & response](#-example-request-and-response-for-individuals-)
    * [Biosamples endpoint](#-biosamples-endpoint)
      * [List of filters](#-list-of-filters-and-permitted-values-for-the-biosamples-endpoint-)
      * [Filters description](#-biosamples-filters-description-)
      * [Example request & response](#-example-request-and-response-for-biosamples-)
    * [Catalogs endpoint](#-catalogs-endpoint-)
      * [List of filters](#-list-of-filters-and-permitted-values-for-catalogs-)
      * [Filters description](#-catalogs-filters-description-)
      * [Resource schema](#-catalogs-resources-schema-)
      * [Example request & response](#-example-request-and-response-for-catalogs-)
      * [Unsupported filters](#-catalogs-unsupported-filters-note-)
* [Authentication using Header(s)](#-authentication-using-header-)
* [Understanding the query](#-understanding-the-query)
    * [Syntax & Usage of Beacon Query with Filters](#-syntax-and-usage)
        * [Multi-Filter (AND) query](#beacon-queries-using-multiples-of-the-same-type-of-filter-and-logical-operator-between-filters)
        * [Same Filter (AND) query](#beacon-queries-using-multiple-values-as-in-phenotype-or-disease-filters-or-logical-operator-between-filter-values)
        * [Multi-Filter (OR) query using **Arrays**](#beacon-queries-using-multiples-of-the-same-type-of-filter-or-logical-operator-between-filters)
    * [Partial query matches with warning messages](#partial-query-matches-with-warning-messages)
* [Understanding the response with ranges (for /individuals and /biospecimens)](#-understanding-the-response-with-ranges-for-individuals-and-biospecimens)
* [Informational Endpoints](#-informational-endpoints-)
    * [Info endpoint](#-info-endpoint-)
    * [Service Info endpoint](#-service-info-endpoint-)
    * [Configuration endpoint](#-configuration-endpoint-)
    * [Entry Types endpoint](#-entry-types-endpoint-)
    * [Filtering terms endpoint](#-filtering-terms-endpoint-)
    * [Map endpoint](#-map-endpoint-)


<hr>
 
<h2 id="#-try-out-the-api-"> Try out the API </h2>

Latest version (v2.0) of this specification is available on Swagger here: https://app.swaggerhub.com/apis/DVS6_1/virtual-platform_beacon_api/v4.0

<hr>

<h2 id="-specification-"> Specification </h2>

The request and response conforms to the [Beacon Reference Framework](https://github.com/ga4gh-beacon/beacon-v2). This Specification defines two types of endpoints - **[The Query Endpoints](#-query-endpoints-)** and **[The Informational Endpoints](#-informational-endpoints-)**. 

[Informational Endpoints](#-informational-endpoints-) are simple GET requests without needing a request body, and respond with information relavant to this Beacon Specification. These are: /info, /configuration, /entry_types, /filtering_terms and /map. A special /service-info endpoint (also a GET request), responds with metadata relevant to this Beacon using the [GA4GH ServiceInfo format](https://github.com/ga4gh-discovery/ga4gh-service-info/). 

[Query Endpoints](#-query-endpoints-) require the requester to provide a JSON body and send request using the POST method. This document defines three query endpoints to query resources using filters - [/individuals](#-individuals-endpoint), [/biosamples](#-biosamples-endpoint) and [/catalogs](#-catalogs-endpoint-). 

<hr>

<h2 id="-query-endpoints-"> Query Endpoints ( Mandatory to implement atleast one of the query endpoints) </h2>

This specification defines POST endpoints to request information about resources. Each endpoint makes use of the [Filters](http://docs.genomebeacons.org/filters/) capability of the Beacon API.

<h3 id="-individuals-endpoint"> Individuals endpoint</h3>

> **HTTP Request Method : POST**

[/individuals](https://github.com/ejp-rd-vp/vp-api-specs/blob/main/vp_api_v0.3.yml) endpoint returns the **__maximum value of individuals within a specified range__** from a RD resource. Filters are provided as a part of the body while using a HTTP POST request to query resources. 

Please **do not use HTTP GET method** to query the individuals endpoint, as it is **not permitted** per this specification, and will result in a 403 error response.

<h4 id="-list-of-filters-and-permitted-values-for-the-individuals-endpoint-"> List of filters and permitted values for the individuals endpoint </h4>

> **Note**: Elements within arrays in **value** fields are treated as **ORs**

<table>
<thead>
        <th>CDE Concept</th>
        <th>CDE Term</th>
        <th>Beacon Filter Type</th>
        <th>ID</th>
        <th>Operator</th>
        <th>Permitted Values</th>
</thead>
<tbody>
    <tr>
        <td rowspan="5">
            <b>Sex</b>
        </td>
        <td rowspan="5">
            ncit:C28421
        </td>
        <td rowspan="5">
            Alphanumerical
        </td>
        <td rowspan="5">
            ncit:C28421
        </td>
        <td rowspan="5">
            =
        </td>
        <td>
            ncit:C16576
        </td>
    </tr>
    <tr>
        <td>
            ncit:C20197
        </td>
    </tr>
    <tr>
        <td>
            ncit:C124294
        </td>
    </tr>
    <tr>
        <td>
            ncit:C17998
        </td>
    </tr>
    <tr>
        <td>
            An array of any of the above
        </td>
    </tr>
    <tr>
        <td><b>Disease or Disorder</b>
        </td><td>ncit:C2991</td>
        <td>Ontology</td>
        <td>A single value or an array of orphanet terms. <b>e.g. ordo:Orphanet_558 or [ordo:Orphanet_558, ordo:Orphanet_773]</b></td>
        <td colspan="2">NA</td>
    </tr>
    <tr>
        <td><b>Phenotype</b></td>
        <td>sio:SIO_010056</td>
        <td>Ontology</td>
        <td>A single value or an array of HPO terms. <b>e.g. hp:0001251 or [hp:0001251, hp:0012250]</b></td>
        <td colspan="2">NA</td>
    </tr>
    <tr>
        <td><b>Causative Genes</b></td>
        <td>edam:data_2295</td>
        <td>Alphanumerical</td>
        <td>edam:data_2295 </td>
        <td>=</td>
        <td>any HGNC gene symbol or array of HGNC symbols</td>
    </tr>
    <tr>
      <td><b>Age this year</b></td><td>ncit:C83164</td>
        <td>Numerical</td>
        <td>ncit:C83164 </td>
        <td>=, &gt;=, &gt;, &lt;=, &lt;</td>
        <td>any integer</td>
    </tr>
    <tr>
      <td><b>Symptom Onset</b></td><td>ncit:C124353</td>
        <td>Numerical</td>
        <td>ncit:C124353</td>
        <td>=, &gt;=, &gt;, &lt;=, &lt;</td>
        <td>any integer</td>
    </tr>
    <tr>
      <td><b>Age at diagnosis</b></td><td>ncit:C156420</td>
        <td>Numerical</td>
        <td>ncit:C156420</td>
        <td>=, &gt;=, &gt;, &lt;=, &lt;</td>
        <td>any integer</td>
    </tr>
</tbody>
</table>

[ ^ Back to the top](#top)

<hr>

<h3 id="-individuals-filters-description-"> Individuals Filters Description </h3>

**Sex**: The biological sex of an individual patient.

**Disease or Disorder**: All rare diseases that have been diagnosed **within an individual**, to encompase, but not distinguish between all levels of diagnosis such as definitive, differential, provisional, etc.,

**Phenotype**: HPO terms of all phenotypes observed **within an individual**.

**Causative Genes**: All genes which have been deemed as causative of one or more of the diagnosed rare diseases **in an individual**, encompassing and not distinguishing between all certainty levels of casuality.

**Age this year**: Age at the end of the current year. -/+ will be added to all age queries when executed by the query engine at the resource. 

**Symptom Onset**: Age at the manifestation of a rare disease. For individuals with more than one rare disease, this filter will look at all age of manifestations independently. -/+ will be added to all age queries when executed by the query engine at the resource. 

**Age at diagnosis**: Age at the diagnosis of a rare disease. For individuals with more than one rare disease, this filter will look at all age of manifestations independently. -/+ will be added to all age queries when executed by the query engine at the resource.

[ ^ Back to the top](#top)

<hr>

<h3 id="-example-request-and-response-for-individuals-"> Example request and response for individuals </h3>

**EXAMPLE /individuals REQUEST**

```JSON
{
    "meta": {
        "apiVersion": "v2.0"
    },
    "query": {
        "filters": [
              {
                "id": "ordo:Orphanet_34587",
                "includeDescendantTerms": true
              },
              {
                "id": "edam:data_2295",
                "value": "ACTN4",
                "operator": "="
              },
              {
                "id": "ncit:C28421",
                "operator": "=",
                "value": "ncit:C16576"
              }
        ],
        "requestedGranularity": "boolean"
    }
}
```

**EXAMPLE /individuals RESPONSE**


```JSON
{
  "meta": {
      "apiVersion": "v2.0",
      "beaconId": "Responding unique Beacon ID in reverse domain name notation",
      "returnedGranularity": "count"
  },
  "response": {
     "resultSets": [
      {
         "id": "Vivify",
         "type": "dataset", 
         "exists": true,
         "resultCount": 80,
         "info": {
            "resultCountDescription": {
               "minRange": 71,
               "maxRange": 80  
            },
            "countType": "VCF file",
            "contactPoint": "admin",
            "contactEmail": "admin@cafevariome.org", 
            "contactURL": "rdnexusdev.molgeniscloud.org/cv2/"
         }      
      }
    ]
  },
  "responseSummary":{
    "exists": true,
    "numTotalResults": 80
  }
}
```
<p  id="-notes-">

The "resultCount" attribute in the above response is the **maximum value of whatever range that result** is within, rather than giving out the actual count of individuals. More information on responding using ranges can be found [here](#-understanding-the-response-with-ranges-for-individuals-and-biospecimens) 

The filter **SHOULD** be one of the terms from the [filters and permitted values table](#-list-of-filters-and-permitted-values-for-the-individuals-endpoint-). Please note that not all resources will support all of the filters. In such cases the response **MUST** include a [warning message in the 'info' part](#warning-response-example) indicating which requested filters are unsupported and these were not included in the query.

The count **MUST** be accompanied by an unrestricted free text term stating what type of entity the count refers to (e.g., biobanks, trial sites, RD cases, biosamples, cell lines, VCF files, etc) 

The "includeDescendantTerms" is used to query for entities associated with the submitted bio-ontology term(s). The default and assumed value of includeDescendantTerms is **false** . If the parameter is set to true, then the request implies that a hierarchical ontology search is requested.

</p>

**EXAMPLE warning when unsupported filters are requested**


```JSON
 {
    "meta": {
        "beaconId": "org.rd-connect.beacon",
        "apiVersion": "v2.0.0-draft.4",
        "returnedGranularity": "count",
        "receivedRequestSummary": {
            "apiVersion": "v2.0",
            "requestedSchemas": [
                {
                    "entityType": "individuals",
                    "schema": "beacon-individual-v2.0.0-draft.4"
                }
            ],
            "filters": [
                [
                    {
                        "id": [
                            "ordo:Orphanet_730",
                            "ordo:Orphanet_2730"
                        ]
                    },
                    {
                        "id": "ncit:C28421",
                        "operator": "=",
                        "value": "ncit:C20197"
                    },
                    {
                        "id": "edam:data_2295",
                        "operator": "=",
                        "value": "ACTN4"
                    },
                    {
                        "id": "ncit:C83164",
                        "operator": ">=",
                        "value": "0"
                    },
                    {
                        "id": "ncit:C124353",
                        "operator": ">=",
                        "value": "0"
                    },
                    {
                        "id": "ncit:C156420",
                        "operator": ">=",
                        "value": "0"
                    }
                ]
            ]
        },
        "returnedSchemas": [
            {
                "entityType": "individuals",
                "schema": "beacon-individual-v2.0.0-draft.4"
            }
        ]
    },
    "responseSummary": {
        "exists": false,
        "numTotalResults": 0
    },
    "info": {
        "warnings": {
            "unsupportedFilters": [
                "ncit:C83164",
                "ncit:C124353",
                "ncit:C156420"
            ]
        }
    },
    "response": {
        "resultSets": [
            {
                "id": "datasetBeacon",
                "type": "individuals",
                "exists": false,
                "resultCount": 0
            }
        ]
    }
}
```

<h3 id="-biosamples-endpoint"> Biosamples endpoint</h3>

> **HTTP Request Method : POST**

Similarly to the `/individuals` endpoint, `/biosamples` endpoint returns the **__maximum value of biosamples within a specified range__** from an RD resource (usually the resource is a Biobank). Queries are performed in the same way as for `/individuals` endpoint, adding filters in the body of the request.

Please **do not use HTTP GET method** to query the biosamples endpoint, as it is **not permitted** per this specification, and will result in a 403 error response.

<h4 id="-list-of-filters-and-permitted-values-for-the-biosamples-endpoint-"> List of filters and permitted values for the biosamples endpoint </h4>

> **Note**: Elements within arrays in **value** fields are treated as **ORs**

<table>
<thead>
        <th>Concept</th>
        <th>Ontological Term</th>
        <th>Beacon Filter Type</th>
        <th>ID</th>
        <th>Operator</th>
        <th>Permitted Values</th>
</thead>
<tbody>
    <tr>
        <td rowspan="5"><b>Sex</b></td>
        <td rowspan="5">ncit:C28421</td>
        <td rowspan="5">Alphanumerical</td>
        <td rowspan="5">ncit:C28421</td>
        <td rowspan="5">=</td>
        <td>ncit:C16576</td>
    </tr>
    <tr><td>ncit:C20197</td></tr>
    <tr><td>ncit:C124294</td></tr>
    <tr><td>ncit:C17998</td></tr>
    <tr><td>An array of any of the above</td></tr>
    <tr>
        <td><b>Disease or Disorder</b></td>
        <td>ncit:C2991</td>
        <td>Ontology</td>
        <td>A single value or an array of orphanet terms. <b>e.g. ordo:Orphanet_558 or [ordo:Orphanet_558, ordo:Orphanet_773]</b></td>
        <td colspan="2">NA</td>
    </tr>
    <tr>
        <td><b>Year of birth</b></td>
        <td>ncit:C83164</td>
        <td>Numerical</td>
        <td>ncit:C83164 </td>
        <td>=, &gt;=, &gt;, &lt;=, &lt;</td>
        <td>any integer</td>
    </tr>
    <tr>
        <td><b>Age at diagnosis</b></td>
        <td>ncit:C156420</td>
        <td>Numerical</td>
        <td>ncit:C156420</td>
        <td>=, &gt;=, &gt;, &lt;=, &lt;</td>
        <td>any integer</td>
    </tr>
    <tr>
        <td rowspan="20"><b>Biospecimen Type</b></td>
        <td rowspan="20">ncit:C70713</td>
        <td rowspan="20">Alphanumerical</td>
        <td rowspan="20">ncit:C70713</td>
        <td rowspan="20">=</td>
        <td>obi:0000655 (blood specimen)</td>
    </tr>
    <tr><td>obi:0002512 (bone marrow)</td></tr>
    <tr><td>obi:0000036 (buffy coat)</td></tr>
    <tr><td>cl_2000001 (peripheral blood mononuclear cell)</td></tr>
    <tr><td>obi:0100016 (blood plasma specime)</td></tr>
    <tr><td>obi:0100017 (blood serum)</td></tr>
    <tr><td>uberon_0007795 (ascites fluid)</td></tr>
    <tr><td>obi:0002502 (cerebrospinal fluid)</td></tr>
    <tr><td>obi:0002507 (saliva)</td></tr>
    <tr><td>obi:0002503 (feces)</td></tr>
    <tr><td>obi:0000651 (urine)</td></tr>
    <tr><td>obi:0002599 (swab)</td></tr>
    <tr><td>obi:2000009 (bodily fluid specimen)</td></tr>
    <tr><td>obi:1200000 (FFPE specimen)</td></tr>
    <tr><td>obi:0000922 (frozen specimen)</td></tr>
    <tr><td>obi:0001472 (specimen with known storage state)</td></tr>
    <tr><td>obi:0001051 (DNA extract)</td></tr>
    <tr><td>obi:0000880 (RNA extract)</td></tr>
    <tr><td>obi:0001479 (specimen from organism)</td></tr>
    <tr><td>An array of any of the above</td></tr>    
</tbody>
</table>

[ ^ Back to the top](#top)

<hr>

<h3 id="-biosamples-filters-description-"> Biosamples Filters Description </h3>

**Sex**: The biological sex of the person the biosample belongs to.

**Disease or Disorder**: All rare diseases that have been diagnosed **from the biosample**, to encompase, but not distinguish between all levels of diagnosis such as definitive, differential, provisional, etc.,

**Year of birth**: The year of birth of the person who the biosample belongs to

**Age at diagnosis**: Age at the diagnosis of a rare disease. For biosamples with more than one rare disease, this filter will look at all age of manifestations independently. -/+ will be added to all age queries when executed by the query engine at the resource.

**Biospecimen Type**: One or more biospecimen type. The list is a list of ontology terms taken from MIABIS

[ ^ Back to the top](#top)

<hr>

<h3 id="-example-request-and-response-for-biosamples-"> Example request and response for biosamples </h3>

**EXAMPLE /biosamples REQUEST**

```JSON
{
    "meta": {
        "apiVersion": "v2.0"
    },
    "query": {
        "filters": [
              {
                "id": "ordo:Orphanet_34587"
              },
              {
                "id": "ncit:C70713",
                "operator": "=",
                "value": "obi:0000655"
              }
        ],
        "requestedGranularity": "count"
    }
}
```

**EXAMPLE /biosamples RESPONSE**


```JSON
{
  "meta": {
      "beaconId": "biobank beacon",
      "apiVersion": "v2.0.0",
      "returnedGranularity": "count",
      "receivedRequestSummary": {
          "apiVersion": "2.0",
          "filters": [
              {
                  "id": "ordo:Orphanet_34587"
              },
              {
                  "id": "ncit:C70713",
                  "operator": "=",
                  "value": "obi:0000655"
              }
          ],
          "requestedGranularity": "count",
          "testMode": false
      },
      "returnedSchemas": [
          {
              "entityType": "biosample",
              "schema": "beacon-biosample-v2.0.0",
              "name": "Default schema for a biological sample",
              "url": "https://raw.githubusercontent.com/ga4gh-beacon/beacon-v2-Models/main/BEACON-V2-Model/biosamples/defaultSchema.json",
              "version": "v2.0.0"
          }
      ]
  },
  "responseSummary": {
      "exists": true,
      "numTotalResults": 100
  },
  "response": {
    "resultSets": [
      {
        "id": "Vivify",
        "type": "biosamples", 
        "exists": true,
        "resultCount": 100,
        "info": {
          "resultCountDescription": {
              "minRange": 91,
              "maxRange": 100  
          },
          "countType": "VCF file",
          "contactPoint": "admin",
          "contactEmail": "admin@cafevariome.org", 
          "contactURL": "rdnexusdev.molgeniscloud.org/cv2/"
        }      
      }
    ]
  }    
}
```
[Notes](#-notes-) about the `resultCount` and unsupported filters for the `/individuals` endpoint apply also for `biospecimens`

[ ^ Back to the top](#top)

<hr>

<h3 id="-catalogs-endpoint-">Catalogs endpoint </h3>

> Method: POST

The [/catalogs](https://app.swaggerhub.com/apis/DVS6_1/virtual-platform_beacon_api/v2.0#/Query%20Endpoints/catalogs_request) endpoint is used to query a Catalog (as defined in [Resource Metadata Schema](https://github.com/ejp-rd-vp/resource-metadata-schema)) of resources. It returns a list of  **__metadata of RD resources__ (such as Dataset, Biobank, Patient Registry of Guidelined)** contained in the catalog.

This response schema and the filters are semantically compatible with the [Resource Metadata Schema](https://github.com/ejp-rd-vp/resource-metadata-schema). The response contains json-ld items and it specifies the semantic of the properties (i.e., they are associated using @context to a term from an ontology or controlled vocabulary). Also the filters for this version are semantically compatible with the Resource Metadata Schema as well.

[ ^ Back to the top](#top)

<hr>

<h4 id="-list-of-filters-and-permitted-values-for-catalogs-">List of filters and permitted values</h4>

> **Note**: Elements within arrays in **value** fields are treated as **ORs**

| Metadata Schema Concept | Associated Metadata Schema Term | Filter Type | ID | Operator | Permitted Values |
| ---- | ----- | ----- | ----- | ----- | ------ |
| Disease | `dcat:theme` | Ontology | A single term or an array of Orphanet terms in CURIE format prefixed with `ordo:` e.g. `ordo:Orphanet_558` or `[ordo:Orphanet_558, ordo:Orphanet_773]` | Not Available | Any Orphanet code in CURIE format | 
| Phenotype | `sio:SIO_010056` | Ontology | A single term or an array of HPO terms in CURIE format prefixed with `hp:` e.g. `hp:0001251` or `[hp:0001251, hp:0012250]` | Not Available | Any HPO code in CURIE format |
| Resource types | `rdf:type` | Ontology | A single term or an array of terms among the EJPRD supported resource's types | Not Available | `ejprd:PatientRegistry`, `ejprd:Biobank`, `ejprd:Guideline`, `dcat:Dataset` |  
| Country | `dct:spatial` | Alphanumeric | Country using ISO 3166-1 alpha-2 format (e.g, IT for Italy) | = | An ISO 3166-1 alpha-2 code |   

[ ^ Back to the top](#top)

<hr>

<h4 id="-catalogs-filters-description-">Filters Description</h4>

The definition for the filter is the following:

**Disease**: Codes of rare diseases that are associated with the resource **within the catalog**.

**Phenotype**: HPO terms of all phenotypes observed **within the catalog** of rare disease resources.

**Resource Types**: Types of resources **within the catalog**. It is the set of ontology terms representing the type of resources defined by the resource metadata schema.

**Country**: The country of the resource. 

Examples of filter body for all of the filters are:

 * Disease: `{ "id": "ordo:Orphanet_589"}`, `{ "id": ["ordo:Orphanet_589", "ordo:Orphanet_730"]}`
 * Phenotype: `{ "id": "hp:0001251"}`, `{ "id": ["hp:0001251", "hp:0012250"]}`
 * Resource types: `{ "id": "ejprd:Biobank"}`, `{ "id": ["ejprd:PatientRegistry", "ejprd:Guideline"] }`
 * Country: `{ "id": "dct:spatial", "operator": "=", "value": ["IT", "DE"] }`

[ ^ Back to the top](#top)

<hr>

<h4 id="-catalogs-resources-schema-">Resources schema</h4>

The response contains a list of JSON-LD items with metadata of the 
resources in the catalog. The resources represent Biobanks, Patient 
Registries, Guidelines or Datasets as defined by the Resource Metadata 
schema. 

Each item must contain the property `@context` used to specify the 
JSON-LD context where the semantic of the properties is defined. 
The value of this property must be the [url](https://raw.githubusercontent.com/ejp-rd-vp/vp-api-specs/main/json-ld-contexts/ejprd-context.json) of 
the `json-ld-contexts/ejprd-context.json` file in this repository.

The schema id is `ejprd-resource-v4.0.0`. The properties 
are described in the [ejprd-resources-v4.0.0](../schemas/4.0.0/ejprd-resources-v4.0.0.json) file.

[ ^ Back to the top](#top)

<hr>

<h4 id="-example-request-and-response-for-catalogs-">Example request and response</h4>

**REQUEST**

```json
{
    "meta": {
        "apiVersion": "2.0",
        "requestedSchemas": ["ejprd-resources-v4.0.0"] 
    },
    "query": {
        "filters": [{
            "id": "ordo:Orphanet_100"
        }, {
            "id": ["ejprd:Biobank"]
        }, {
          "id": "dct:spatial",
          "operator": "=",
          "value": ["IT", "DE"]
        }]
    }
}
```

**RESPONSE**

```JSON
{
  "meta": {
    "beaconId": "ejprd.beacon.directory.bbmri-eric.eu",
    "apiVersion": "v2.0.0",
    "returnedGranularity": "record",
    "receivedRequestSummary": {
      "apiVersion": "2.0",
      "requestedSchemas": ["ejprd-resources-v4.0.0"],
      "filters": [{
          "id": "ordo:Orphanet_730"
        }, {
          "id": ["ejprd:Biobank"]
        }, {
          "id": "dct:spatial",
          "operator": "=",
          "value": ["IT", "DE"]
        }
      ],
      "requestParameters": {},
      "includeResultsetResponses": "HIT",
      "pagination": {
        "skip": 0,
        "limit": 50
      },
      "requestedGranularity": "record",
      "testMode": false
    },
    "returnedSchemas": [
      {
        "entityType": "resources",
        "schema": "ejprd-resources-v4.0.0",
        "name": "EJPRD schema for resources",
        "url": "https://raw.githubusercontent.com/ejp-rd-vp/vp-api-specs/main/schemas/4.0/ejprd-resources-v4.0.0.json",
        "version": "v4.0.0"
      }
    ]
  },
  "responseSummary": {
    "exists": true,
    "numTotalResults": 1
  },
  "beaconHandovers": [],
  "response": {
    "resultSets": [
      {
        "resultsCount": 1,
        "results": [
          {
            "@context": "https://raw.githubusercontent.com/ejp-rd-vp/vp-api-specs/main/json-ld-contexts/ejprd-context.json",
            "@id": "biobank-1:collection:collection-1",
            "@type": "ejprd:Biobank",
            "title": "Rare Disease Biobank",
            "logo": "http://raredisease.biobank.eu/logo.png",
            "description": "Rare disease biobank with data about muscular distrophy",
            "populationCoverage": "European",
            "theme": "ordo:Orphanet_730",
            "vpConnection": "ejprd:VPContentDiscovery",
            "landingPage": [
              "http://biobank.raredisease.org"
            ],
            "personalData": "true",
            "language": "IT",
            "publisher": {
              "@id": "biobank-1",
              "title": "Organization hosting the collection of samples",
              "description": "The biobank that hosts the collection",
              "spatial": {
                "title": "IT"
              }
            }
          }
        ]
      }
    ]
  }
}
```

[ ^ Back to the top](#top)

<hr>

<h3 id="-catalogs-unsupported-filters-note-">Unsupported filters</h3>

Similarly to what happens with the `/individuals` and `/biosamples` 
endpoints, filter **SHOULD** be one of the terms from the allowed ones. 
Not all resources will support all of the filters. In such cases 
the response **MUST** include a **warning message in the 'info' 
part** indicating which requested filters are unsupported and these 
were ignored.

An example of info section in the response is: 

```JSON
{
  "meta": {
    "beaconId": "ejprd.beacon.directory.bbmri-eric.eu",
    "apiVersion": "v2.0.0",
    "returnedGranularity": "record",
    "receivedRequestSummary": {
      "apiVersion": "2.0",
      "requestedSchemas": ["ejprd-resources-v4.0.0"],
      "filters": [{
          "id": "ordo:Orphanet_730"
        }, {
          "id": ["ejprd:Biobank", "ejprd:PatientRegistry"]
        }, {
          "id": "dct:spatial",
          "operator": "=",
          "value": ["IT", "DE"]
        }
      ],
      "requestParameters": {},
      "includeResultsetResponses": "HIT",
      "pagination": {
        "skip": 0,
        "limit": 50
      },
      "requestedGranularity": "record",
      "testMode": false
    },
    "returnedSchemas": [
      {
        "entityType": "resources",
        "schema": "ejprd-resources-v4.0.0",
        "name": "EJPRD schema for resources",
        "url": "https://raw.githubusercontent.com/ejp-rd-vp/vp-api-specs/main/schemas/4.0/ejprd-resources-v4.0.0.json",
        "version": "v4.0.0"
      }
    ]
  },
  "responseSummary": {
    "exists": true,
    "numTotalResults": 1
  },
  "info": {
    "warnings": {
      "unsupportedFilters": [
        "ejprd:PatientRegistry"  
      ]
    }
  },
  "beaconHandovers": [],
  "response": {
    "resultSets": [
      {
        "resultsCount": 1,
        "results": [
          {
            "@context": "https://raw.githubusercontent.com/ejp-rd-vp/vp-api-specs/main/json-ld-contexts/ejprd-context.json",
            "@id": "biobank-1:collection:collection-1",
            "@type": "ejprd:Biobank",
            "title": "Rare Disease Biobank",
            "logo": "http://raredisease.biobank.eu/logo.png",
            "description": "Rare disease biobank with data about muscular distrophy",
            "populationCoverage": "European",
            "theme": "ordo:Orphanet_730",
            "vpConnection": "ejprd:VPContentDiscovery",
            "landingPage": [
              "http://biobank.raredisease.org"
            ],
            "personalData": "true",
            "language": "IT",
            "publisher": {
              "@id": "biobank-1",
              "title": "Organization hosting the collection of samples",
              "description": "The biobank that hosts the collection",
              "spatial": {
                "title": "IT"
              }
            }
          }
        ]
      }
    ]
  }
}
```

[ ^ Back to the top](#top)

<hr>

<h2 id="-authentication-using-header-"> Authentication using Header </h2>

Since the specification allows for record level queries of individuals, additional information is required in the request header to verify the requester is authorised:

<table>
<thead>
<th>Header Attribute</th>
<th>Header Value</th>
<th>Purpose</th>
</thead>
<tbody>
<tr><td>auth-key</td><td>Token provided by resource</td><td>Indicates requester is authorised (required)</td></tr>
<tr><td>Authorization</td><td>Bearer Token</td><td>The access token issued by LS AAI</td></tr>
</tbody>
</table>

For now, the auth key is still used on some resources, but it will soon be deprecated, and switch to the Bearer token. The token is issued by LS AAI with OIDC protocol. The absence of the token indicate that it's a user who did not log in.

> **Note:** Presence of a bearer token is equivalent to auth-token:True

> **Note:** Implementers can provide distinct auth-keys to each requester or a single auth-key to all requesters.

To see this live in action in Swagger UI, see [Authentication in Swagger](#-authentication-using-header-for-swagger-).

[ ^ Back to the top](#top)

<hr>

<h2 id="-understanding-the-query-"> Understanding the query </h2>

<h3 id="-syntax-and-usage-"> Syntax and Usage: </h3>

> Method: POST

There are 3 types of Beacon queries that this specification currently supports: 
1. [Alphanumerical Query](http://docs.genomebeacons.org/filters/#exact-value-query)
2. [Numerical Query](http://docs.genomebeacons.org/filters/#numerical-value-query)
3. [Ontology Query](http://docs.genomebeacons.org/filters/#simple-curie-based-filters-query)

The following JSON body depicts the typical usage of filters to query resources. 
   * Alphanumeric filters **require** the use of id, operator(=) and value properties. 
   * Ontology filters only require the 'id' property to query resources.
   * Numeric filters also require id, operator and value properties. These filters differ from the alphanumeric filters as it is also possible to use comparison operators (>=,>,<=,<) along with '='.

These usage rules are illustrated using a general syntax as below:

```JSON
{
  "meta": {
    "apiVersion": "v2.0"
  },
  "query": {
    "filters": [
      {
        "id": "AlphanumericFilter:id",
        "operator": "=",
        "value":"AlphanumericFilter:value"
        
      },
      {
        "id": "AlphanumericFilter:id",
        "operator": "=",
        "value":"AlphanumericFilter:value"
      },
      { 
        "id": "OntologyFilter:value"
      }
    ],
     "requestedGranularity": "boolean"
  }
}
```

[ ^ Back to the top](#top)

<hr>

As shown above, different types of filters can be sent in a single query. These are further elucidated below. 

<h4 id="beacon-queries-using-multiples-of-the-same-type-of-filter-and-logical-operator-between-filters"> Beacon queries using multiple filters (AND logical operator between filters):</h4>

```JSON
{
"query": {
      "filters": [
        {
          "id": "ordo:Orphanet_34587" 
        },
        {
          "id": "edam:data_2295",
          "operator": "=",
          "value": "ACTN4"
        }
      ]
    }
}
```

This filter is asking for individuals that have been diagnosed with Danon disease (ordo:Orphanet_34587) **and** where LAMP2 gene has been identified as causative. These filters are handled independently, this means that individuals with Danon disease where LAMP2 has been identified as a causative gene, specifically for Danon disease, will match the query. It also means that individuals with Danon disease and where LAMP2 has been identified as a causative gene for a second rare disease, other than Danon disease, will also match this query.


[ ^ Back to the top](#top)

<hr>

<h4 id="beacon-queries-using-multiple-values-as-in-phenotype-or-disease-filters-and-logical-operator-between-filter-values">Beacon queries using multiples of the same type of filter (AND logical operator between filters):</h4>

To query for individuals with more than one instance of any of the filters you can send multiple of the same filter, such as in the below example:

> Ontology Filter Example:

```JSON
{
"query": 
    {
      "filters": [
        {
          "id": "ordo:Orphanet_34587"
        },
        {
          "id": "ordo:Orphanet_1653"
        }
      ]
   }
}
```
This query is looking for individuals with Danon disease ("ordo:Orphanet_34587") AND Dentin dysplasia ("ordo:Orphanet_1653").

> Alphanumeric Filter example: 

```JSON
{
"query": 
    {
      "filters": [
        {
          "id": "Available Materials",
          "operator": "=",
          "value": "RNA sequence"          
        },
        {
          "id": "Available Materials",
          "operator": "=",
          "value": "Whole Genome Sequence"
        }
      ]
   }
}
```
This query is looking if there are any individuals with RNA sequence information AND Whole Genome Sequence information available.

[ ^ Back to the top](#top)

<hr>

<h4 id="beacon-queries-using-multiples-of-the-same-type-of-filter-or-logical-operator-between-filters">Beacon queries using multiples of the same type of filter (OR logical operator between filters):</h4>

> Ontology Filter using Array Example:

```JSON
{
"query": 
    {
      "filters": [
        {
          "id": ["ordo:Orphanet_34587","ordo:Orphanet_1653"]
        }
      ]
   }
}
```
This query is looking for individuals either with Danon disease (ordo:Orphanet_34587) OR Dentin dysplasia (ordo:Orphanet_1653).

> **Note**: There are no OR operators available **between** filters with beacon queries.

All of the defined filters are optional, the user can provide as many or as few as wanted and the resource does not have to implement all filters.And if the user does not provide any filters all the list of entities will be returned.

If a user sends a query with a filter not supported by a resource, then the resource should complete the query but ignore the unsupported filter(s) and respond as usual, but with a warning noting that certain filters were ignored as they are unsupported.

The warning messages will be provided within the [`info`](#partial-query-matches-with-warning-messages)  section of the Beacon.

[ ^ Back to the top](#top)

<hr>

<h2 id="partial-query-matches-with-warning-messages"> Partial Request & Response with warning message Example </h2>

**EXAMPLE REQUEST**

```JSON
{
  "meta":{
      "apiVersion": "v2.0"
  },
  "query":{
    "filters": [
        {
          "id": "ordo:Orphanet_34587"
        },
        {
          "id": "edam:data_2295",
          "value": "ACTN4",
          "operator": "="
        },
        {
          "id": "ncit:C28421",
          "operator": "=",
          "value": "ncit:C16576"
        }
    ],
     "requestedGranularity": "boolean"
  }
}
```

This request is asking for females with Danon disease with LAMP2 being identified as a causative gene.

This request is sent to a resource which does not hold information about causative genes but does hold information about diagnoses and sex, an example response which could be returned is outlined below:

**EXAMPLE WARNING RESPONSE**

```JSON
{
  "meta": {
      "apiVersion": "v2.0",
      "beaconId": "Responding unique Beacon ID in reverse domain name notation",
      "returnedGranularity":"count"
  },
  "response": {
     "resultSets": [
      {
         "id": "Vivify",
         "type": "dataset",
         "exists": true,
         "resultCount": 20,
         "info": {
            "resultCountDescription": {
               "minRange": 11,
               "maxRange": 20
            },
            "countType": "VCF file",
            "contactPoint": "admin",
            "contactEmail": "admin@cafevariome.org",
            "contactURL": "rdnexusdev.molgeniscloud.org/cv2/"
         }   
      }
     ]
  },
  "responseSummary":{
    "exists": "true",
    "numTotalResults": 20
  },
  "info": { 
    "warnings":{
      "unsupportedFilters": [
        "edam:data_2295"
      ]
    }
  }
}
```

This response provides a warning message within the info section advising of unsupported filters which were ignored when the query was processed by the resources query engine. Please see the info part of the [IndividualResponse](https://app.swaggerhub.com/apis/DVS6_1/virtual-platform_beacon_api/v2.0#/IndividualResponse) schema on swagger. 


[ ^ Back to the top](#top)

<hr>

<h2 id="-understanding-the-response-with-ranges-for-individuals-and-biospecimens"> Understanding the response with ranges (for /individuals and /biospecimens)</h2>

In the examples of the response for the `/individuals` and `/biosamples` endpoints, information of the resultant dataset matching the query is provided within the **`resultSets`** attribute. 

To provide flexibility for implementers between using a range, the `info` section of each resultant dataset in `resultSets` need to conform to the following standardised structure:

```JSON
"info": {
   "resultCountDescription": {
      "minRange": N,
      "maxRange": N
   },
   "countType": "VCF file",
   "contactPoint": "Person/point of contact",
   "contactEmail": "Email for contact regarding this dataset/resource", 
   "contactURL": "URL of the implementer"
}
```

> **Note**: Here, N is an integer where the implementer can respond with **"minRange" & "maxRange"** (if employing a range) - the maximum value of whatever range that result is within, whereupon the "maxRange" value should match the "resultCount" value and the "numTotalResults" value.

**Example response employing a range:**

```JSON
{
  "meta": {
      "apiVersion": "v2.0",
      "beaconId": "Responding unique Beacon ID in reverse domain name notation",
      "returnedGranularity": "count"
  },
  "response": {
     "resultSets": [
      {
         "id": "Vivify",
         "type": "dataset", 
         "exists": true,
         "resultCount": 80,
         "info": {
            "resultCountDescription": {
               "minRange": 71,
               "maxRange": 80
            },
            "countType": "VCF file.",
            "contactPoint": "admin",
            "contactEmail": "admin@cafevariome.org", 
            "contactURL": "rdnexusdev.molgeniscloud.org/cv2/"
         }      
      }
    ]
  },
  "responseSummary":{
    "exists": true,
    "numTotalResults": 80
  }
}
```

In this example, the result could be a count of individuals between 71 to 80 (the resource only responds with ranges of size 10).


[ ^ Back to the top](#top)

<hr>



<h2 id="-informational-endpoints-"> Informational Endpoints (Mandatory to be implemented for all the resources )</h2>

This specification defines GET endpoints to request information about resources.

<h3 id="-info-endpoint-"> Info endpoint</h3>

> **HTTP Request Method : GET**

[/info](https://github.com/ejp-rd-vp/vp-api-specs/blob/main/vp_api_v2.0.yml) returns the information about the Beacon.

<h3 id="-example-request-and-response-for-info-"> Example response for info </h3>

```JSON
{
    "meta": {
        "beaconId": "BeaconAPI.cv2.rdnexusdev.molgeniscloud.org",
        "apiVersion": "v2.0",
        "returnedSchemas": {
            "entityType": "Info Endpoint",
            "schema": "https://raw.githubusercontent.com/ga4gh-beacon/beacon-v2/main/models/json/beacon-v2-default-model/endpoints.json"
        }
    },
    "response": {
        "id": "BeaconAPI.cv2.rdnexusdev.molgeniscloud.org",
        "name": "Cafe Variome Beacon",
        "apiVersion": "v2.0",
        "createDateTime": "2021-02-03 15:07 BST",
        "updateDateTime": "2022-10-05 17:18 BST",
        "description": "This Beacon is based on the Beacon specification by GA4GH. Implemented by The Brookeslab @ University of Leicester, this Beacon contains all informational endpoints along with individuals and biosamples discovery.",
        "environment": "dev",
        "organization": {
            "id": "ULEIC",
            "name": "University of Leicester",
            "address": "University Road, Leicester, LE1 7RH",
            "contactUrl": "mailto:admin@cafevariome.org?subject=Beacon Info",
            "logoUrl": "https://rdnexusdev.molgeniscloud.org/cv2/resources/images/logos/cafevariome-logo-full.png",
            "welcomeUrl": "https://le.ac.uk/health-data-research/",
            "description": "Cafe Variome is a flexible data discovery software. Cafe Variome + Beacon makes discovering genomic data easier."
        },
        "welcomeUrl": "https://www.cafevariome.org/",
        "alternativeUrl": "https://le.ac.uk/health-data-research/activities/"
    }
}
```
<h3 id="-service-info-endpoint-"> Service-info endpoint</h3>

> **HTTP Request Method : GET**

[/service-info](https://github.com/ejp-rd-vp/vp-api-specs/blob/main/vp_api_v2.0.yml) returns the information about the basic metadata concerning its service, based on the [reference specification](https://github.com/ga4gh-discovery/ga4gh-service-info/).

<h3 id="-example-request-and-response-for-service-info"> Example response for service-info </h3>

```JSON
{
    "id": "BeaconAPI.cv2.rdnexusdev.molgeniscloud.org",
    "name": "Cafe Variome Beacon",
    "type": {
        "artifact": "beacon",
        "group": "BeaconAPI.cv2.rdnexusdev.molgeniscloud.org",
        "version": "v2.0"
    },
    "organization": {
        "name": "University of Leicester",
        "url": "https://www.le.ac.uk"
    },
    "contactUrl": "mailto:admin@cafevariome.org?subject=Beacon Service Info",
    "createdAt": "2021-02-03 15:07 BST",
    "updatedAt": "2022-10-06 11:56 BST",
    "description": "This service provides information about Beacon deployed by Cafe Variome Software.",
    "documentationUrl": "https://cafe-variome.gitbook.io/cafe-variome-docs/features/beacon/beacon-api",
    "environment": "dev",
    "version": "v2.0"
}
```
<h3 id="-configuration-endpoint-">Configuration</h3>

> **HTTP Request Method : GET**

[/configuration](https://github.com/ejp-rd-vp/vp-api-specs/blob/main/vp_api_v2.0.yml) returns the information about the Beacon. Aimed to Beacon clients like web pages or Beacon networks.

<h3 id="-example-request-and-response-for-configuration"> Example response for service-info </h3>

```JSON
{
    "meta": {
        "beaconId": "BeaconAPI.cv2.rdnexusdev.molgeniscloud.org",
        "apiVersion": "v2.0",
        "returnedSchemas": [
            {
                "entityType": "individuals",
                "schema": "https://raw.githubusercontent.com/ga4gh-beacon/beacon-v2/main/models/json/beacon-v2-default-model/individuals/defaultSchema.json"
            },
            {
                "entityType": "biosamples",
                "schema": "https://raw.githubusercontent.com/ga4gh-beacon/beacon-v2/main/models/json/beacon-v2-default-model/biosamples/defaultSchema.json"
            }
        ]
    },
    "response": {
        "$schema": "https://raw.githubusercontent.com/ga4gh-beacon/beacon-v2/main/framework/json/configuration/beaconConfigurationSchema.json",
        "entryTypes": {
            "Individuals": {
                "id": "Individuals",
                "name": "Individuals",
                "ontologyTermForThisType": {
                    "id": "ncit:C25190"
                },
                "partOfSpecification": "v2.0",
                "defaultSchema": {
                    "id": "beacon-v2-individual",
                    "name": "Default Schema for Individuals",
                    "referenceToSchemaDefinition": "https://raw.githubusercontent.com/ga4gh-beacon/beacon-v2/main/models/json/beacon-v2-default-model/individuals/defaultSchema.json"
                }
            },
            "Biosamples": {
                "id": "Biosamples",
                "name": "Biosamples",
                "ontologyTermForThisType": {
                    "id": "ncit:C43412"
                },
                "partOfSpecification": "v2.0",
                "defaultSchema": {
                    "id": "beacon-v2-biosample",
                    "name": "Default Schema for Biosamples",
                    "referenceToSchemaDefinition": "https://raw.githubusercontent.com/ga4gh-beacon/beacon-v2/main/models/json/beacon-v2-default-model/biosamples/defaultSchema.json"
                }
            }
        },
        "maturityAttributes": {
            "productionStatus": "DEV"
        },
        "securityAttributes": {
            "defaultGranularity": "count"
        }
    }
}
```

<h3 id="-entry-types-endpoint-">Entry-types</h3>

> **HTTP Request Method : GET**

[/entry-types](https://github.com/ejp-rd-vp/vp-api-specs/blob/main/vp_api_v2.0.yml) returns the list of Entry types definitions.

<h3 id="example-request-and-response-for-entry-types"> Example response for entry-types </h3>

```JSON
{
    "meta": {
        "beaconId": "BeaconAPI.cv2.rdnexusdev.molgeniscloud.org",
        "apiVersion": "v2.0",
        "returnedSchemas": [
            {
                "entityType": "individuals",
                "schema": "https://raw.githubusercontent.com/ga4gh-beacon/beacon-v2/main/models/json/beacon-v2-default-model/individuals/defaultSchema.json"
            },
            {
                "entityType": "biosamples",
                "schema": "https://raw.githubusercontent.com/ga4gh-beacon/beacon-v2/main/models/json/beacon-v2-default-model/biosamples/defaultSchema.json"
            }
        ]
    },
    "response": {
        "entryTypes": {
            "Individuals": {
                "id": "Individuals",
                "name": "Individuals",
                "ontologyTermForThisType": {
                    "id": "ncit:C25190"
                },
                "partOfSpecification": "v2.0",
                "defaultSchema": {
                    "id": "beacon-v2-individual",
                    "name": "Default Schema for Individuals",
                    "referenceToSchemaDefinition": "https://raw.githubusercontent.com/ga4gh-beacon/beacon-v2-Models/main/BEACON-V2-draft4-Model/individuals/defaultSchema.json"
                }
            },
            "Biosamples": {
                "id": "Biosamples",
                "name": "Biosamples",
                "ontologyTermForThisType": {
                    "id": "ncit:C43412"
                },
                "partOfSpecification": "v2.0",
                "defaultSchema": {
                    "id": "beacon-v2-biosample",
                    "name": "Default Schema for Biosamples",
                    "referenceToSchemaDefinition": "https://raw.githubusercontent.com/ga4gh-beacon/beacon-v2/main/models/json/beacon-v2-default-model/biosamples/defaultSchema.json"
                }
            }
        }
    }
}
```
<h3 id="-filtering-terms-endpoint-">Filtering terms</h3>

> **HTTP Request Method : GET**

The `/filtering_terms` endpoint is used by the Beacon to inform the clients on the list of the terms supported to filter 
the results.

<h3 id="example-request-and-response-for-filtering_terms"> Example response for filtering_terms </h3>

Here is an example response for the filtering terms enpoints

```JSON
{
  "meta": {
    "beaconId": "BeaconAPI.cv2.rdnexusdev.molgeniscloud.org",
    "apiVersion": "v2.0",
    "returnedSchemas": []
  },
  "response": {
    "resources": [
      {
        "id": "ordo",
        "name": "Orphanet Ontology",
        "url": "https://www.orphadata.com/data/ontologies/ordo/last_version/ORDO_en_4.4.owl",
        "version": "4.4",
        "namespacePrefix": "ordo",
        "iriPrefix": "http://www.orpha.net/ORDO/"
      },
      {
        "id": "dcat",
        "name": "DCAT 2 Vocabulary",
        "url": "https://www.w3.org/ns/dcat2.ttl",
        "version": "2.0",
        "namespacePrefix": "dcat",
        "iriPrefix": "http://www.w3.org/ns/dcat#"
      },
      {
        "id": "ncit",
        "name": "NCIT",
        "url": "http://purl.obolibrary.org/obo/ncit.owl",
        "version": "2023-101-19",
        "namespacePrefix": "obo",
        "iriPrefix": "http://purl.obolibrary.org/obo/"
      }
    ],
    "filteringTerms": [
      {
        "id": "ordo:Orphanet_589",
        "label": "Disease or disorder",
        "type": "ontology",
        "scopes": [
          "individuals",
          "biosamples",
          "catalogs"
        ]
      },
      {
        "id": "ncit:C156420",
        "label": "Age at diagnosis",
        "type": "numeric",
        "scopes": [
          "individuals",
          "biosamples"
        ]
      },
      {
        "id": "dct:spatial",
        "label": "Country",
        "type": "alphanumeric",
        "scopes": [
          "catalogs"
        ]
      }
    ]
  }
}
```

This response notifies the clients that this beacon supports three filtering terms, as defined in the filteringTerms items:
 - ordo:Orphanet_589 filter by this code
 - ncit:C156420: filters by age at diagnosis
 - dct:spatial: filter by country

Notice that each term may specify also a `"scopes"` attribute: this is used to specify for which type of enpoints the filter is supported. For example the `ncit:C156420` term can be used only for `/individuals` and `/biosamples` endpoint.

The `"resource"` section of the endpoint is used the client how the CURIEs prefixes are interpreted by the beacon endpoint. 

For example the item:

```JSON
{
  "id": "ordo",
  "name": "Orphanet Ontology",
  "url": "https://www.orphadata.com/data/ontologies/ordo/last_version/ORDO_en_4.4.owl",
  "version": "4.4",
  "namespacePrefix": "ordo",
  "iriPrefix": "http://www.orpha.net/ORDO/"
}
```

means that the `ordo` prefix is used for the orphanet codes and terms starting with `ordo` (`"namespacePrefix"` attribute) are expanded to `"http://www.orpha.net/ORDO/"` ("iriPrefix").

The following table shows the recommended prefixes used by the Virtual Platform. 

| Ontology | Prefix | IRI | Example |
| -------- | ------ | --- | ------- |
| Orphanet | ordo   | http://www.orpha.net/ORDO/ | ordo:Orphanet_730 | 
| DCAT | dcat | http://www.w3.org/ns/dcat# | dcat:Dataset |
| DCMI Terms | dct | http://purl.org/dc/terms/ | dct:spatial |
| Resource Description Framework | rdf | http://www.w3.org/1999/02/22-rdf-syntax-ns# | rdf:type |
| EJP-RD Vocabulary | ejp-rd | https://w3id.org/ejp-rd/vocabulary# | ejp-rd:Biobank | 
| Semanticscience Integrated Ontology (SIO) | sio | http://semanticscience.org/ontology/ | sio |
| Human Phenotype Ontology | hp | http://purl.obolibrary.org/obo/HP_ | hp:0001251 |
| NCI Thesaurus (NCIT) | obo | http://purl.obolibrary.org/obo/ | obo:NCIT__C28421 |
| Ontology for Biomedical Investigations (OBI) | obi | http://purl.obolibrary.org/obo/ | obo:OBI_0000655 |
| Ontology for BIoBanking (OBIB) | obib | http://purl.obolibrary.org/obo/ | obo:OBIB_0000036 |
| Uber-anatomy ontology | obo | http://purl.obolibrary.org/obo/ | obo:UBERON_0007795 |
| Cell ontology | obo | http://purl.obolibrary.org/obo/ | obo:CL_2000001 | 

[ ^ Back to the top](#top)

<hr>

<h3 id="-map-endpoint-">Map</h3>

> **HTTP Request Method : GET**

[/map](https://github.com/ejp-rd-vp/vp-api-specs/blob/main/vp_api_v2.0.yml) returns the information related to the list of endpoints included in this Beacon instance.

<h3 id="example-request-and-response-for-map"> Example response for map </h3>

```JSON
{
    "meta": {
        "beaconId": "BeaconAPI.cv2.rdnexusdev.molgeniscloud.org",
        "apiVersion": "v2.0",
        "returnedSchemas": [
            {
                "entityType": "individuals",
                "schema": "https://raw.githubusercontent.com/ga4gh-beacon/beacon-v2/main/models/json/beacon-v2-default-model/individuals/defaultSchema.json"
            },
            {
                "entityType": "biosamples",
                "schema": "https://raw.githubusercontent.com/ga4gh-beacon/beacon-v2/main/models/json/beacon-v2-default-model/biosamples/defaultSchema.json"
            }
        ]
    },
    "response": {
        "$schema": "https://raw.githubusercontent.com/ga4gh-beacon/beacon-v2/main/framework/json/configuration/beaconMapSchema.json",
        "endpointSets": {
            "Individuals": {
                "entryType": "Individuals",
                "rootUrl": "https://rdnexusdev.molgeniscloud.org/cv2/BeaconAPI/Individuals",
                "filteringTermsUrl": "https://rdnexusdev.molgeniscloud.org/cv2/resources/beacon/filtering_terms.json"
            },
            "Biosamples": {
                "entryType": "Biosamples",
                "rootUrl": "https://rdnexusdev.molgeniscloud.org/cv2/BeaconAPI/Biosamples"
            }
        }
    }
}
```

[ ^ Back to the top](#top)

<hr>

<h2 id="swagger-auth"> Authentication using Header for Swagger </h2>

In Swagger, to query using both the /individuals endpoint & /catalogs endpoint (which are POST requests), you have to authorize the query using the **Authorize** button (extreme right, beside Servers dropdown in Swagger UI). 

![image](https://user-images.githubusercontent.com/24955128/203320000-a9cbc5a5-4c49-4a2b-8666-4e0cb17a5a62.png)

![image](https://user-images.githubusercontent.com/24955128/205334330-c90f760d-5d1e-4685-8640-030787233454.png)

Use one of the authentication token provided to perform record level queries.
![image](https://user-images.githubusercontent.com/24955128/205334443-fce92738-a515-4f0e-8f9f-b701561c283b.png)

> Developers Note: No matter the user agent being used to query (i.e., SwaggerUI, Postman, cURL, etc.,), the authentication header **auth-key is required**. 

[ ^ Back to the top](#top)

<hr>