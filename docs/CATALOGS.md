# Catalogs endpoint

> Method: POST

The [/catalogs](https://app.swaggerhub.com/apis/DVS6_1/virtual-platform_beacon_api/v2.0#/Query%20Endpoints/catalogs_request) endpoint is used to query a Catalog (as defined in [Resource Metadata Schema](https://github.com/ejp-rd-vp/resource-metadata-schema)) of resources. It returns a list of  **__metadata of RD resources__ (such as Dataset, Biobank, Patient Registry of Guidelined)** contained in the catalog.

There are two possible versions for this endpoint, each one specifying a set of filters and a schema for the responses.

 * **Version 1.0**: This is the legacy one which is derived by the Query Builder. It returns the resources as JSON with properties not having a specific semantic (i.e., they are not associated to an ontology term). Also the filters don't have specific semantic meaning.
 * **Version 4.0**: This version is semantically compatible with the [Resource Metadata Schema](https://github.com/ejp-rd-vp/resource-metadata-schema). The response contains json-ld items and it adds semantic to the properties (i.e., they are associated using @context to a term from an ontology or controlled vocabulary). Also the filters for this version are semantically compatible with the Resource Metadata Schema as well.

## Version 1.0

This version is the first one used to query for catalogs. 
The following sections describe the list of possible filters that a Beacon can support to query for catalogs and the schema of the responses

### List of filters and permitted values

> **Note**: Elements within arrays in **value** fields are treated as **ORs**

| Metadata Schema Concept | Associated Metadata Schema Term | Filter Type | ID | Operator | Permitted Values |
| ---- | ----- | ----- | ----- | ----- | ------ |
| Disease | `dcat:theme` | Custom | A single term or an array of Orphanet codes in e.g., `Orphanet_558` or `[Orphanet_558, Orphanet_773]` | Not Available | Any Orphanet code | 
| Phenotype | `sio:SIO_010056` | Custom | A single term or an array of HPO terms in the format `hp_<code>` e.g. `HP_0001251` or `[HP_0001251, HP_0012250]` | Not Available | Any HPO code in the format  |
| Resource types | `rdf:type` | Alphanumeric | A single term or an array of terms among the ones listed | Not Available | `PatientRegistryDataset`, `BiobankDataset`, `Dataset`, `Guideline` |  
| Country | `dct:spatial` | Alphanumeric | country | = | An ISO 3166-1 alpha-2 code (e.g., IT for Italy) |   

The definition for the filter is the following:

**Disease**: All rare diseases that are associated with the resource **within the catalog**. 

**Phenotype**: HPO terms of all phenotypes observed **within the catalog** of rare disease resources.

**Resource Types**: Types of resources **within the catalog**.

**Country**: The country of the resource. 

Notice that Disease and Phenotype are of type `Custom`: this is the Beacon type for 
filters that are not Ontology or Alphanumeric. Indeed, the id of the filters are 
Orpha codes and HPO terms, but they are not in a semantic format. This has been 
changed in version 4.0 as we will see.

Examples of body for each of the filters are

 * Disease: `{ "id": "Orphanet_589"}`, `{ "id": ["Orphanet_589", "Orphanet_730"]}`
 * Phenotype: `{ "id": "HP_0001251"}`, `{ "id": ["HP_0001251", "HP_0012250"]}`
 * Resource types: `{ "id": "resourceTypes", "operator": "=", "value": ["BiobankDataset", "PatientRegistryDataset"] }`
 * Country: `{ "id": "country", "operator": "=", "value": ["IT", "DE"] }`


## Version 4.0

A problem with the version 1.0 is that both the filters and the schema of the results
don't have a proper semantic support, meaning that they are not 
associated to a term from an ontology or a controlled vocabulary.
To overcome this problem a new version was designed.
This version provides the same filters but uses terms from ontologies. The schema of 
the response uses JSON-LD and is compliant with the model of the [Resource Metadata Schema](https://github.com/ejp-rd-vp/resource-metadata-schema), adding semantic support to the returned resouces

The following sections describe the filtering terms and the model of the items in the
response for this version

### List of filters and permitted values

> **Note**: Elements within arrays in **value** fields are treated as **ORs**

| Metadata Schema Concept | Associated Metadata Schema Term | Filter Type | ID | Operator | Permitted Values |
| ---- | ----- | ----- | ----- | ----- | ------ |
| Disease | `dcat:theme` | Ontology | A single term or an array of Orphanet terms in CURIE format prefixed with `ordo:` e.g. `ordo:Orphanet_558` or `[ordo:Orphanet_558, ordo:Orphanet_773]` | Not Available | Any Orphanet code in CURIE format | 
| Phenotype | `sio:SIO_010056` | Ontology | A single term or an array of HPO terms in CURIE format prefixed with `hp:` e.g. `hp:0001251` or `[hp:0001251, hp:0012250]` | Not Available | Any HPO code in CURIE format |
| Resource types | `rdf:type` | Ontology | A single term or an array of terms among the EJPRD supported resource's types | Not Available | `ejprd:PatientRegistry`, `ejprd:Biobank`, `ejprd:Guideline`, `dcat:Dataset` |  
| Country | `dct:spatial` | Alphanumeric | Country using ISO 3166-1 alpha-2 format (e.g, IT for Italy) | = | An ISO 3166-1 alpha-2 code |   

As can be noticed, the filters are the same as before but they are defined 
differently. Disease and Phenotype are now of type `Ontology` and they adopt the same 
codes but in CURIES syntax. Also the Resource Types has been changed to Ontology type 
since it uses terms from the controlled vocabularies defined by EJPRD. Finally, 
Country remains of type Alphanumeric but now the ID is dct:spatial, as defined by the 
resource metadata schema.

Examples of filter body for all of the filters are:

 * Disease: `{ "id": "ordo:Orphanet_589"}`, `{ "id": ["ordo:Orphanet_589", "ordo:Orphanet_730"]}`
 * Phenotype: `{ "id": "hp:0001251"}`, `{ "id": ["hp:0001251", "hp:0012250"]}`
 * Resource types: `{ "id": "ejprd:Biobank"}`, `{ "id": ["ejprd:PatientRegistry", "ejprd:Guideline"] }`
 * Country: `{ "id": "dct:sptial", "operator": "=", "value": ["IT", "DE"] }`

### Resources schema

The response contains a list of JSON-LD items with metadata of the 
resources in the catalog. The resources represent Biobanks, Patient 
Registries, Guidelines or Datasets as defined by the Resource Metadata 
schema. 

Each item must contain the property `@context` used to specify the 
JSON-LD context where the semantic of the properties is defined. 
The value of this property must be the [url](https://raw.githubusercontent.com/ejp-rd-vp/vp-api-specs/main/json-ld-contexts/ejprd-context.json) of 
the `json-ld-contexts/ejprd-context.json` file in this repository.

The schema id for this version is `ejprd-resource-v4.0.0`. The properties 
are described in the [ejprd-resources-v4.0.0](../schemas/4.0.0/ejprd-resources-v4.0.0.json) file.

## Request and response for Version 1.0

The request, as for the other endpoints, is performed via a POST REST request with a `meta` part and a `query` part. 

Here is an example of a request using filters of version 1

```json
{
  "meta": {
    "apiVersion": "2.0",
    "requestedSchemas": ["ejprd-resources-v1.0.0"]
  },
  "query": {
    "filters": [{
        "id": "Orphanet_100"
    }, {
        "id": "resourceTypes",
        "operator": "=",
        "value": ["BiobankDataset", "PatientRegistryDataset"]
    }, {
      "id": "country",
      "operator": "=",
      "value": ["IT", "DE"]
    }]
  }
}
```

Notice that in the meta section the client has specifically requested that the 
resources in the response are formatted using the v1.0.0 schema (`"requestedSchemas": 
["ejprd-resources-v1.0.0"]`). If not specified, the responses are returned using the 
default schema of the Beacon. The default schema and the supported schemas are listed 
using the `/entry_types` informational endpoint.

The `query` part contains the list of filters.

The following is an example response for the query below, returning one corresponding Biobank

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
          "id": "Orphanet_730"
        }, {
          "id": ["ejprd:Biobank", "ejprd:PatientRegistry"]
        }, {
          "id": "country",
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
        "schema": "ejprd-resources-v1.0.0",
        "name": "EJPRD schema for resources",
        "url": "https://raw.githubusercontent.com/ejp-rd-vp/vp-api-specs/main/schemas/1.0.0/ejprd-resources-v1.0.0.json",
        "version": "v1.0.0"
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
            "id": "biobank-1:collection:collection-1",
            "type": "BiobankDataset",
            "title": "Rare Disease Biobank",
            "description": "Rare disease biobank with data about muscular distrophy",
            "homepage": "http://biobank.raredisease.org",
            "publisher": {
              "id": "biobank-1",
              "name": "Organization hosting the collection of samples",
              "location": {
                "id": "IT",
                "country": "Italy"
              }
            }
          }
        ]
      }
    ]
  }
}
```

Some of the points to notice in the meta part are:

* `"returnedGranularity": "record"`: it means that the response contains the metadata of the resources
* `"returnedSchemas"`: the beacon service specifies the schema of the items in the response 
* `"responseSummary"`: summary about results. It tells whether some results are returned and the number of results.

The response part contains a `resultSet` with the resources formatted using Version 1.0

## Request and response for Version 4

What follows is an example of the same request as before, that uses version 4.0 filters and schema

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
            "id": ["ejprd:Biobank", "ejprd:PatientRegistry"]
        }, {
          "id": "dct:spatial",
          "operator": "=",
          "value": ["IT", "DE"]
        }]
    }
}
```

The following is an example of the response with the 4.0.0 schema

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
        "url": "https://raw.githubusercontent.com/ejp-rd-vp/vp-api-specs/main/schemas/4.0.0/ejprd-resources-v4.0.0.json",
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

Notice some differences with the version 1.0.0

* `"returnedSchemas"`: Now the schemas specified by the beacon is the 4.0.0 (`ejprd-resources-v4.0.0.json`)
* `response`: the items in the result are now formatted with the v4.0.0 schema


[Notes](#-notes-) about the unsupported filters for the `/individuals` endpoint apply also for `catalogs`