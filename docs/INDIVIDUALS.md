# Individuals endpoint

> **HTTP Request Method : POST**

[/individuals](https://github.com/ejp-rd-vp/vp-api-specs/blob/main/vp_api_v0.3.yml) endpoint returns the **__maximum value of individuals within a specified range__** from a RD resource. Filters are provided as a part of the body while using a HTTP POST request to query resources. 

Please **do not use HTTP GET method** to query the individuals endpoint, as it is **not permitted** per this specification, and will result in a 403 error response.

## List of filters and permitted values for the individuals endpoint

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
            obo:NCIT_C28421
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
        </td><td>obo:NCIT_C2991</td>
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
        <td>data:2295 </td>
        <td>=</td>
        <td>any HGNC gene symbol or array of HGNC symbols</td>
    </tr>
    <tr>
      <td><b>Age this year</b></td><td>obo:NCIT_C83164</td>
        <td>Numerical</td>
        <td>ncit:C83164 </td>
        <td>=, &gt;=, &gt;, &lt;=, &lt;</td>
        <td>any integer</td>
    </tr>
    <tr>
      <td><b>Symptom Onset</b></td><td>obo:NCIT_C124353</td>
        <td>Numerical</td>
        <td>ncit:C124353</td>
        <td>=, &gt;=, &gt;, &lt;=, &lt;</td>
        <td>any integer</td>
    </tr>
    <tr>
      <td><b>Age at diagnosis</b></td><td>obo:NCIT_C156420</td>
        <td>Numerical</td>
        <td>ncit:C156420</td>
        <td>=, &gt;=, &gt;, &lt;=, &lt;</td>
        <td>any integer</td>
    </tr>
</tbody>
</table>

[ ^ Back to the top](#top)

<hr>

## Individuals Filters Description

**Sex**: The biological sex of an individual patient.

**Disease or Disorder**: All rare diseases that have been diagnosed **within an individual**, to encompase, but not distinguish between all levels of diagnosis such as definitive, differential, provisional, etc.,

**Phenotype**: HPO terms of all phenotypes observed **within an individual**.

**Causative Genes**: All genes which have been deemed as causative of one or more of the diagnosed rare diseases **in an individual**, encompassing and not distinguishing between all certainty levels of casuality.

**Age this year**: Age at the end of the current year. -/+ will be added to all age queries when executed by the query engine at the resource. 

**Symptom Onset**: Age at the manifestation of a rare disease. For individuals with more than one rare disease, this filter will look at all age of manifestations independently. -/+ will be added to all age queries when executed by the query engine at the resource. 

**Age at diagnosis**: Age at the diagnosis of a rare disease. For individuals with more than one rare disease, this filter will look at all age of manifestations independently. -/+ will be added to all age queries when executed by the query engine at the resource.

[ ^ Back to the top](#top)

<hr>

## Example request and response

**REQUEST**

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

**RESPONSE**


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
               "maxRange": 80,
               "resultCountDescription": "This count refers to VCF files available in the dataset."
            },
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
The "resultCount" attribute in the above response is the **maximum value of whatever range that result** is within, rather than giving out the actual count of individuals. More information on responding using ranges can be found [here](./UNDERSTANDING_RESPONSE_WITH_RANGES.md) 

The filter **SHOULD** be one of the terms from the [filters and permitted values table](#list-of-filters-and-permitted-values-for-the-individuals-endpoint). Please note that not all resources will support all of the filters. In such cases the response **MUST** include a [warning message in the 'info' part](#example-warning-when-unsupported-filters-are-requested) indicating which requested filters are unsupported and these were not included in the query.

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
            "id": "data:2295",
            "operator": "=",
            "value": "100"
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