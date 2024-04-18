# Biosamples endpoint

This section describes how to query a Beacon for biosamples using the `/biosamples` endpoint

> **HTTP Request Method : POST**

Similarly to the `/individuals` endpoint, `/biosamples` endpoint returns the **__maximum value of biosamples within a specified range__** from an 
RD resource (usually the resource is a Biobank). Queries are performed in the same way as for `/individuals` endpoint, adding filters in the body of the request.

Please **do not use HTTP GET method** to query the biosamples endpoint, as it is **not permitted** per this specification, and will result in a 403 error response.

## List of filters and permitted values for the biosamples endpoint

> **Note**: Elements within arrays in **value** fields are treated as **ORs**

| Concept | Ontological Term | Filter Type | ID | Operator | Permitted Values | 
| ----- | ----- | ----- | ----- | ----- | ----- |
| Sex | ncit:C28421 | Alhanumerical | ncit:C28421 | = | `ncit:C16576`, `ncit:C20197`, `ncit:C124294`, `ncit:C17998` | 
| Disease | ncit::C2991 | Ontology | A single value of an array of Orphanet terms using CURIE format (e.g., `ordo:Orphanet_589`, [`ordo:Orphanet_589`, `ordo:Orphanet_730`]) | NA | Any Orphanet codes in CURIE format
| Year of birth | ncit:C83164 | Numerical | ncit:C83164 | =, &gt;=, &gt;, &lt;=, &lt; | any integer | 
| Age at diagnosis | ncit:C156420 | Numerical | ncit:C156420 | =, &gt;=, &gt;, &lt;=, &lt; | any integer | 
| Biospecimen Type | ncit:C70713 | Ontology | One of the permitted values | NA | `obi:0000655 (blood specimen)`, `obi:0002512 (bone marrow)`,  `obib:0000036 (buffy coat)`, `cl:2000001 (peripheral blood mononuclear cell)`, `obi:0100016 (blood plasma specime)`, `obi:0100017 (blood serum)`, `uberon:0007795 (ascites fluid)`, `obi:0002502 (cerebrospinal fluid)`, `obi:0002507 (saliva)`, `obi:0002503 (feces)`, `obi:0000651 (urine)`, `obi:0002599 (swab)`, `obi:2000009 (bodily fluid specimen)`, `obi:1200000 (FFPE specimen)`, `obi:0000922 (frozen specimen)`, `obi:0001472 (specimen with known storage state)`, `obi:0001051 (DNA extract)`, `obi:0000880 (RNA extract)`, `obi:0001479 (specimen from organism)` |

[ ^ Back to the top](#top)

## Biosamples Filters Description

**Sex**: The biological sex of the person the biosample belongs to.

**Disease or Disorder**: All rare diseases that have been diagnosed **from the biosample**, to encompase, but not distinguish between all levels of diagnosis such as definitive, differential, provisional, etc.,

**Year of birth**: The year of birth of the person who the biosample belongs to

**Age at diagnosis**: Age at the diagnosis of a rare disease. For biosamples with more than one rare disease, this filter will look at all age of manifestations independently. -/+ will be added to all age queries when executed by the query engine at the resource.

**Biospecimen Type**: One or more biospecimen type. The list is a list of ontology terms taken from OBIB ontology corresponding to terms of [MIABIS](https://github.com/BBMRI-ERIC/miabis).

[ ^ Back to the top](#top)

## Example request and response for biosamples

**REQUEST**

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
        "id": "obi:0000655"
      }
    ],
    "requestedGranularity": "count"
  }
}
```

**RESPONSE**

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
          "id": "obi:0000655"
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
}
```

[Notes](./INDIVIDUALS.md#-notes-) about the `resultCount` and unsupported filters for the `/individuals` endpoint apply also for `biospecimens`

[ ^ Back to the top](#top)
