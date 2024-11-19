# Informational Endpoints (Mandatory to be implemented for all the resources)

This specification defines GET endpoints to request information about resources.

* [Info endpoint](#-info-endpoint-)
* [Service Info endpoint](#-service-info-endpoint-)
* [Configuration endpoint](#-configuration-endpoint-)
* [Entry Types endpoint](#-entry-types-endpoint-)
* [Filtering terms endpoint](#-filtering-terms-endpoint-)
* [Map endpoint](#-map-endpoint-)

## Info endpoint

> **HTTP Request Method : GET**

[/info](https://github.com/ejp-rd-vp/vp-api-specs/blob/main/vp_api_v2.0.yml) returns the information about the Beacon.

### Example response for info

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

## Service-info endpoint

> **HTTP Request Method : GET**

[/service-info](https://github.com/ejp-rd-vp/vp-api-specs/blob/main/vp_api_v2.0.yml) returns the information about the basic metadata concerning its service, based on the [reference specification](https://github.com/ga4gh-discovery/ga4gh-service-info/).

### Example response for service-info

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
## Configuration

> **HTTP Request Method : GET**

[/configuration](https://github.com/ejp-rd-vp/vp-api-specs/blob/main/vp_api_v2.0.yml) returns the information about the Beacon.

### Example response for service-info

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

## Entry-types

> **HTTP Request Method : GET**

[/entry-types](https://github.com/ejp-rd-vp/vp-api-specs/blob/main/vp_api_v2.0.yml) returns the information about the Beacon.

### Example response for entry-types

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
## Filtering_terms

> **HTTP Request Method : GET**

The `/filtering_terms` endpoint is used by the Beacon to inform the clients on the list of the terms supported to filter 
the results.

### Example response for filtering_terms

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
| Dublin Core Vocabulary | dct | http://purl.org/dc/terms/ | dct:spatial |
| Resource Description Framework | rdf | http://www.w3.org/1999/02/22-rdf-syntax-ns# | rdf:type |
| EJP-RD Vocabulary | ejp-rd | https://w3id.org/ejp-rd/vocabulary# | ejp-rd:Biobank | 
| Semanticscience Integrated Ontology (SIO) | sio | http://semanticscience.org/ontology/ | sio |
| Human Phenotype Ontology | hp | http://purl.obolibrary.org/obo/HP_ | hp:0001251 |
| NCI Thesaurus (NCIT) | ncit | http://purl.obolibrary.org/obo/NCIT_ | ncit:C28421 |
| Ontology for Biomedical Investigations (OBI) | obi | http://purl.obolibrary.org/obo/OBI_ | obi:0000655 |
| Ontology for BIoBanking (OBIB) | obib | http://purl.obolibrary.org/obo/OBIB_ | obib:0000036 |
| Uber-anatomy ontology | uberon | http://purl.obolibrary.org/obo/UBERON_ | uberon:0007795 |
| Cell ontology | cl | http://purl.obolibrary.org/obo/CL_ | cl:2000001 | 

## Map

> **HTTP Request Method : GET**

[/map](https://github.com/ejp-rd-vp/vp-api-specs/blob/main/vp_api_v2.0.yml) returns the information related to the list of endpoints included in this Beacon instance.

### Example response for map

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

[ ^ Back to the top](#informational-endpoints-mandatory-to-be-implemented-for-all-the-resources)