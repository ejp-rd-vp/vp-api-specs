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
      * [List of filters](#-list-of-filters-and-permitted-values-for-the-catalogs-endpoint-)
      * [Filters description](#-catalogs-filters-description-)
      * [Example request & response](#-example-request-and-response-for-catalogs-)
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

<h2 id="-query-endpoints-"> Query Endpoints ( Mandatory to implement at least one of the query endpoints) </h2>

This specification defines POST endpoints to request information about resources. Each endpoint makes use of the [Filters](http://docs.genomebeacons.org/filters/) capability of the Beacon API.

There are three supported endpoints:

 * [Individuals endpoint](./docs/INDIVIDUALS.md)
 * [Biosamples endpoint](./docs/BIOSAMPLES.md)
 * [Catalogs endpoint](./docs/CATALOGS.md)

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
          "id": "data:2295",
          "operator": "=",
          "value": "LAMP2"
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
          "id": "data:2295",
          "value": "LAMP2",
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
            "resultCountDescription": "This count refers to VCF files available in the dataset.",
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
        "data:2295"
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
            "resultCountDescription": "This count refers to VCF files available in the dataset.",
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

[/configuration](https://github.com/ejp-rd-vp/vp-api-specs/blob/main/vp_api_v2.0.yml) returns the information about the Beacon.

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

[/entry-types](https://github.com/ejp-rd-vp/vp-api-specs/blob/main/vp_api_v2.0.yml) returns the information about the Beacon.

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
<h3 id="-filtering-terms-endpoint-">Filtering_terms</h3>

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
        "namespacePrefix": "ncit",
        "iriPrefix": "http://purl.obolibrary.org/obo/NCIT_"

      }

    ],
    "filteringTerms": [
      {
        "id": "ordo:*",
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
 - ordo:* whatever term from the Orphanet (ORDO) ontology is accepted (e.g., ordo:Orphanet_730)
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

The following table shows the preferred codes prefixes used by the Virtual Platform. Notice that implementers may use different prefixes but adoption of the following one is highly reccommended.

| Ontology | Prefix | IRI | Example |
| -------- | ------ | --- | ------- |
| Orphanet | ordo   | http://www.orpha.net/ORDO/ | ordo:Orphanet_730 | 
| DCAT | dcat | http://www.w3.org/ns/dcat# | dcat:Dataset |
| DCMI Terms | dct | http://purl.org/dc/terms/ | dct:spatial |
| Dublin Core Vocabulary | dct | http://purl.org/dc/terms/ | dct:spatial |
| Resource Description Framework | rdf | http://www.w3.org/1999/02/22-rdf-syntax-ns# | rdf:type |
| EJP-RD Vocabulary | ejp-rd | https://w3id.org/ejp-rd/vocabulary# | ejp-rd:Biobank | 
| Semanticscience Integrated Ontology (SIO) | sio | http://semanticscience.org/ontology/ | sio |
| NCI Thesaurus | ncit | http://purl.obolibrary.org/obo/NCIT_ | ncit:C28421 |
| Human Phenotype Ontology | hp | http://purl.obolibrary.org/obo/HP_ | hp:0001251 |

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
