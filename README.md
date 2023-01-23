# REST API specification for querying RD resources (record level queries)

> This API specification is defined in the context of the EJPRD project, complying with the latest [Beacon v2 Specification](https://github.com/ga4gh-beacon/beacon-v2).

In this work, we present API specification for querying RD patient registries, biobanks and similar resources at the safe record level (i.e, resources whose available assets are described by RD patient data). Resources that implement this specification would ideally collect data based on the set of common data elements for rare diseases registration, as recommended by the European commission Joint Research Centre. In this specification, where possible, we also make use of ontological terms recommended by the CDE semantic data model group.

<hr>

<h2 id="top"> Contents </h2>

* [Try out this API in Swagger](#try-in-swagger)
* [Specification](#specification)
* [Query Endpoints](#query-endpoints)
    * [Individuals endpoint](#individuals)
      * [List of filters](#individuals-filters)
      * [Example request & response](#individuals-example)
    * [Catalogs endpoint](#catalogs)
      * [List of filters](#catalogs-filters)
      * [Example request & response](#catalogs-example)
* [Authentication using Header(s)](#auth-header)
* [Understanding the filters](#understand-filters)
* [Further Examples](#examples)
    * [Syntax of Beacon Query with Filters](#syntax-usage)
    * [Understanding the query](#understand-query)
        * [Multi-Filter (AND) query](#multi-and)
        * [Same Filter (AND) query](#same-and)
        * [Multi-Filter (OR) query using Arrays](#multi-or-arrays)
    * [Partial query matches with warning messages](#warning-response-example)
* [Informational Endpoints](#info-endpoints)
* [Deprecated Filters](#deprecated-filters)

<hr>
 
<h2 id="try-in-swagger"> Try out the API </h2>

Check out version 0.1 [here](https://app.swaggerhub.com/apis/VM172_1/vp_individuals/v0.1) and version 0.2 [here](https://app.swaggerhub.com/apis/VM172_1/vp_individuals/v0.2).

<hr>

<h2 id="specification"> Specification </h2>

The request and response conforms to the [Beacon Reference Framework](https://github.com/ga4gh-beacon/beacon-v2). This Specification defines two types of endpoints - **[The Query Endpoints](#query-endpoints)** and **[The Informational Endpoints](#info-endpoints)**. 

[Informational Endpoints](#info-endpoints) are simple GET requests without needing a request body, and respond with information relavant to this Beacon Specification. These are: /info, /configuration, /entry_types, /filtering_terms and /map. A special /service-info endpoint (also a GET request), responds with metadata relevant to this Beacon using the [GA4GH ServiceInfo format](https://github.com/ga4gh-discovery/ga4gh-service-info/). 

[Query Endpoints](#query-endpoints) require the requester to provide a JSON body and send request using the POST method.

<hr>

<h2 id="query-endpoints"> Query Endpoints </h2>

This specification defines POST endpoints to request information about resources. Each endpoint makes use of the [Filters](http://docs.genomebeacons.org/filters/) capability of the Beacon API.

<h3 id="individuals"> Individuals endpoint</h3>

> Method : POST

[/individuals](https://github.com/ejp-rd-vp/vp-api-specs/blob/main/individuals_api_v0.2.yml) endpoint returns the **__count of individuals__** from a RD resource. Filters are provided as a part of the body while using a POST request to query resources.

<h4 id="individuals-filters"> List of filters and permitted values for the individuals endpoint </h4>

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
            NCIT_C28421
        </td>
        <td rowspan="5">
            =
        </td>
        <td>
            NCIT_C16576
        </td>
    </tr>
    <tr>
        <td>
            NCIT_C20197
        </td>
    </tr>
    <tr>
        <td>
            NCIT_C124294
        </td>
    </tr>
    <tr>
        <td>
            NCIT_C17998
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
        <td>A single value or an array of orphanet terms. <b>e.g. Orphanet_558 or [Orphanet_558, Orphanet_773]</b></td>
        <td colspan="2">NA</td>
    </tr>
    <tr>
        <td><b>Phenotype</b></td>
        <td>sio:SIO_010056</td>
        <td>Ontology</td>
        <td>A single value or an array of HPO terms. <b>e.g. HP_0001251 or [HP_0001251, HP_0012250]</b></td>
        <td colspan="2">NA</td>
    </tr>
    <tr>
        <td><b>Causative Genes</b></td>
        <td>edam:data_2295</td>
        <td>Alphanumerical</td>
        <td>data_2295 </td>
        <td>=</td>
        <td>any HGNC gene symbol or array of HGNC symbols</td>
    </tr>
    <tr>
        <td rowspan="10"><b>Available Materials</b> </td>
        <td rowspan="10">NA</td>
        <td rowspan="10">Alphanumerical</td>
        <td rowspan="10">Available Materials</td>
        <td rowspan="10">=</td>
    </tr>
    <tr>
        <td>Whole Genome Sequence</td>
    </tr>
    <tr>
        <td>Exome panel sequence</td>
    </tr>
    <tr>
        <td>RNA sequence</td>
    </tr>
    <tr>
        <td>Methylomics</td>
    </tr>
    <tr>
        <td>Epigenomics</td>
    </tr>
    <tr>
        <td>Pedigree data</td>
    </tr>
    <tr>
        <td>Clinical data</td>
    </tr>
    <tr>
        <td>Biosamples</td>
    </tr>
    <tr>
        <td>An array of any of the above</td>
    </tr>
    <tr>
</tbody>
</table>



An example request & response to query for individuals is shown below:

<h4 id="individuals-example">EXAMPLE /individuals REQUEST <h4>

```JSON
{
  "meta":{
  "apiVersion": "v2.0",
    "beaconId": "Unique Beacon ID in reverse domain name notation",
    "returnedSchemas": {
      "entityType": "string",
      "schema": "string"
    }
  },
  "query":{
    "filters": [
        {
          "id": "Orphanet_34587"
        },
        {
          "id": "data_2295",
          "value": "LAMP2",
          "operator": "="
        },
        {
          "id": "NCIT_C28421",
          "operator": "=",
          "value": "NCIT_C16576"
        }
    ]
  }
}
```

**EXAMPLE /individuals RESPONSE**


```JSON
{
  "meta": {
  "apiVersion": "v2.0",
    "beaconId": "Responding unique Beacon ID in reverse domain name notation",
    "returnedSchemas": {
      "entityType": "string",
      "schema": "string"
    }
  },
  "responseSummary":{
    "exists": "true",
    "numTotalResults": 10
  }
}
```
The filter **SHOULD** be one of the terms from the [filters and permitted values table](#individuals). Please note that not all resources will support all of the filters. In such cases the response should include a [warning message in the 'info' part](#warning-response-example) indicating which requested filters are unsupported and these were not included in the query.

[ ^ Back to the top](#top)

<hr>

<h3 id="catalogs"> Catalogs endpoint </h3>

> Method: POST

[/catalogs](https://github.com/ejp-rd-vp/vp-api-specs/blob/main/catalogs_api_v0.2.yml) endpoint returns the **__metadata of RD resource__**. Filters are provided as a part of the body while using a POST request to query resources.

<h4 id="catalogs-filters"> List of filters and permitted values for the catalogs endpoint </h4>

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
        <td><b>Disease or Disorder</b></td>
        <td>obo:NCIT_C2991</td>
        <td>Ontology</td>
        <td>A single value or an array of orphanet terms. <b>e.g. Orphanet_558 or [Orphanet_558, Orphanet_773]</b></td>
        <td colspan="2">NA</td>
    </tr>
    <tr>
        <td><b>Phenotype</b></td>
        <td>sio:SIO_010056</td>
        <td>Ontology</td>
        <td>A single value or an array of HPO terms. <b>e.g. HP_0001251 or [HP_0001251, HP_0012250]</b></td>
        <td colspan="2">NA</td>
    </tr>
    <tr>
        <td rowspan="10"><b>Available Materials</b> </td>
        <td rowspan="10">NA</td>
        <td rowspan="10">Alphanumerical</td>
        <td rowspan="10">Available Materials</td>
        <td rowspan="10">=</td>
    </tr>
    <tr>
        <td>Whole Genome Sequence</td>
    </tr>
    <tr>
        <td>Exome panel sequence</td>
    </tr>
    <tr>
        <td>RNA sequence</td>
    </tr>
    <tr>
        <td>Methylomics</td>
    </tr>
    <tr>
        <td>Epigenomics</td>
    </tr>
    <tr>
        <td>Pedigree data</td>
    </tr>
    <tr>
        <td>Clinical data</td>
    </tr>
    <tr>
        <td>Biosamples</td>
    </tr>
    <tr>
        <td>An array of any of the above</td>
    </tr>
    <tr>
        <td><b>ID </b></td> 
        <td>NA</td>
        <td>Alphanumerical</td>
        <td>id</td>
        <td>=</td>
        <td>any String</td>
    </tr>
    <tr>
        <td><b>Name </b></td>
        <td>NA</td>
        <td>Alphanumerical</td>
        <td>name</td>
        <td>=</td>
        <td>any String</td>
    </tr>
    <tr>
        <td><b>Description </b> </td>
        <td>NA</td>
        <td>Alphanumerical</td>
        <td>description</td>
        <td>=</td>
        <td>any String</td>
    </tr>
    <tr>
        <td><b>Organisation </b> </td>
        <td>NA</td>
        <td>Alphanumerical</td>
        <td>organisation</td>
        <td>=</td>
        <td>any String</td>
    </tr>
    <tr>
        <td rowspan="4"><b>Resource Types</b></td>
        <td rowspan="4">NA</td>
        <td rowspan="4">Alphanumerical</td>
        <td rowspan="4">resourceTypes</td>
        <td rowspan="4">=</td>
        <td>PatientRegistryDataset</td>
    </tr>
    <tr>
        <td>BiobankDataset</td>
    </tr>
    <tr>
        <td>KnowledgeBase</td>
    </tr>
    <tr>
        <td>An array of any of the above</td>
    </tr>
</tbody>
</table>


An example request & response to query for resources via the /catalogs endpoint is shown below:

<h4 id="catalogs-example"> EXAMPLE /catalogs REQUEST </h4>

```JSON
{ 
 "meta":{},
 "query": {
      "filters": [
        {
          "id": "description",
          "value": "%genome comparison%",
        },
        {
          "id": "resourceTypes",
          "value": " BiobankDataset"

        }
      ]
    }
}
```

**EXAMPLE /catalogs RESPONSE**
```JSON
{
  "meta": {},

  "responseSummary": 
  {
    "exists": true,
    "numTotalResults": 1
  },
  "response": {
    "resultSets": [
      {
        "resultsCount": 1,
        "results": [
          {
          "createDateTime": "2017-04-30T00:00:00+00:00",
          "description": "The Genome in a Bottle Consortium, hosted by the National Institute of Standards and Technology (NIST) is creating reference materials and data for human genome sequencing, as well as methods for genome comparison and benchmarking. ",
          "externalUrl": "https://www.nature.com/articles/sdata201625, https://jimb.stanford.edu/giab-resources",
          "id": "EGAD00001008097",
          "name": "The Genome in a Bottle Consortium (GIAB)",
          "updateDateTime": "2017-04-30T00:00:00+00:00",
          "resourceTypes": ["BiobankDataset"],
          "organisation": ["UOL"]

          }
        ],
        "resultsHandover": null
      }
    ]
  },
  "beaconHandovers": []
  }
```

[ ^ Back to the top](#top)

<hr>

<h2 id="auth-header"> Authentication using Header </h2>

Since the specification allows for record level queries of individuals, additional information is required in the request header to verify the requester is authorised:

<table>
<thead>
<th>Header Attribute</th>
<th>Header Value</th>
<th>Purpose</th>
</thead>
<tbody>
<tr><td>auth-key</td><td>Token provided by resource</td><td>Indicates requester is authorised (required)</td></tr>
<tr><td>auth-token</td><td>Bearer token, True, False</td><td>Indicates requesting user's logged in status (optional)</td></tr>
<tr><td>authentication-url</td><td>Bearer token authentication provider</td><td>Enables validation of bearer token (optional)</td></tr>
</tbody>
</table>

> **Note:** Presence of a bearer token is equivalent to auth-token:True

> **Note:** Implementers can provide distinct auth-keys to each requester or a single auth-key to all requesters.

To check this, see [Authentication in Swagger](#swagger-auth)

[ ^ Back to the top](#top)

<hr>

<h2 id="understand-filters"> Understanding the filters </h2>

All filters are to be handled independently, see below for clarification.

<table>
<thead>
  <tr>
    <th>Filter</th>
    <th>Filter description</th>
    <th>Filter interpretation</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>Sex</td>
    <td>The biological sex of the patient</td>
    <td></td>
  </tr>
  <tr>
    <td>Disease or Disorder</td>
    <td>All rare diseases that have been diagnosed within an individual, to encompase, but not distinguish between, all levels of diagnosis such as definitive, differential, provisional etc.</td>
    <td>To be handled independently of other filters</td>
  </tr>
  <tr>
    <td>Phenotype</td>
    <td>HPO terms of all phenotypes observed within an individual</td>
    <td>To be handled independently of other filters</td>
  </tr>
  <tr>
    <td>Causative Genes</td>
    <td>All genes which have been deemed as causative of one or more of the diagnosed rare diseased, encompassing and not distinguishing between all certainty levels of causality</td>
    <td>To be handled independently of other filters</td>
  </tr>
  <tr>
    <td>Age this year</td>
    <td>Age at the end of the current year</td>
    <td>-/+ 1 will be added to all age queries when executed by the query engine at the resource</td>
  </tr>
  <tr>
    <td>Symptom Onset</td>
    <td>Age at the manifestation of a rare disease</td>
    <td>For individuals with more than one rare disease this filter will look at all age of manifestations independently. <br /> -/+ 1 will be added to all age queries when executed by the query engine at the resource</td>
  </tr>
  <tr>
    <td>Age at diagnosis</td>
    <td>Age at the diagnosis of a rare disease</td>
    <td>For individuals with more than one rare disease this filter will look at all age of diagnosis independently. <br /> -/+ 1 will be added to all age queries when executed by the query engine at the resource</td>
  </tr>
  <tr>
    <td>Available Materials</td>
    <td>A list of what information is available about an individual</td>
    <td></td>
  </tr>
</tbody>
</table>

[ ^ Back to the top](#top)

<hr>

<h2 id="examples"> Examples </h2>

<h3 id="syntax-usage"> Syntax & Usage: </h3>

> Method: POST

```JSON
{
  "meta": {
    "apiVersion": "v2.0",
    "beaconId": "Unique Beacon ID in reverse domain name notation",
    "returnedSchemas": {
      "entityType": "string",
      "schema": "string"
    }
  },
  "query": {
    "filters": [
      {
        "id": "filter1_id",
        "operator": "=",
        "value":"filter1_value"
        
      },
      {
        "id": "filter2_id",
        "operator": "=",
        "value":"filter1_value"
      },
      { 
        "id": "filter3_value"
      }
    ]
  }
}
```

[ ^ Back to the top](#top)

<hr>

<h3 id="understand-query"> Understanding the query </h3>

There are 3 types of Beacon query that this specification currently supports: 
1. [Ontology Query](http://docs.genomebeacons.org/filters/#simple-curie-based-filters-query)
2. [Alphanumerical Query](http://docs.genomebeacons.org/filters/#exact-value-query)
3. [Numerical Query](http://docs.genomebeacons.org/filters/#numerical-value-query)

<h4 id="multi-and"> Beacon queries require the use of the AND logical operator between filters:</h4>

```JSON
{
"query": {
      "filters": [
        {
          "id": "Orphanet_34587" 
        },
        {
          "id": "data_2295",
          "operator": "=",
          "value": "LAMP2"
        }
      ]
    }
}
```

This filter is asking for individuals that have been diagnosed with Danon disease (Orphanet_34587) **and** where LAMP2 gene has been identified as causative. These filters are handled independently, this means that individuals with Danon disease where LAMP2 has been identified as a causative gene, specifically for Danon disease, will match the query. It also means that individuals with Danon disease and where LAMP2 has been identified as a causative gene for a second rare disease, other than Danon disease, will also match this query.


[ ^ Back to the top](#top)

<hr>

<h4 id="same-and">Beacon queries using multiples of the same type of filter:</h4>

To query for individuals with more than one instance of any of the filters you can send multiple of the same filter, such as in the below example:

> Ontology Filter Example:

```JSON
{
"query": 
    {
      "filters": [
        {
          "id": "Orphanet_34587"
        },
        {
          "id": "Orphanet_1653"
        }
      ]
   }
}
```
This query is looking for individuals with Danon disease ("Orphanet_34587") AND Dentin dysplasia ("Orphanet_1653").

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

<h4 id="multi-or-arrays">Beacon queries using multiples values as an OR in phenotype or disease filters</h4>

> Ontology Filter using Array Example:

```JSON
{
"query": 
    {
      "filters": [
        {
          "id": ["Orphanet_34587","Orphanet_1653"]
        }
      ]
   }
}
```
This query is looking for individuals either with Danon disease (Orphanet_34587) OR Dentin dysplasia (Orphanet_1653).

There are no OR operators available **between** filters with beacon queries.

All of the defined filters are optional, the user can provide as many or as few as wanted and the resource does not have to implement all filters.

If a user sends a query with a filter not supported by a resource, then the resource should complete the query but ignore the unsupported filter(s) and respond as usual, but with a warning noting that certain filters were ignored as they are unsupported.

The warning messages will be provided within the ['info' section](#warning-response-example) of the Beacon.

[ ^ Back to the top](#top)

<hr>

<h2 id="warning-response-example"> Partial Request & Response with warning message Example </h2>

**EXAMPLE REQUEST**

```JSON
{
  "meta":{
  "apiVersion": "v2.0",
    "beaconId": "Unique Beacon ID in reverse domain name notation",
    "returnedSchemas": {
      "entityType": "string",
      "schema": "string"
    }
  },
  "query":{
    "filters": [
        {
          "id": "Orphanet_34587"
        },
        {
          "id": "data_2295",
          "value": "LAMP2",
          "operator": "="
        },
        {
          "id": "NCIT_C28421",
          "operator": "=",
          "value": "NCIT_C16576"
        }
    ]
  }
}
```

This request is asking for females with Danon disease with LAMP2 being identified as a causative gene.

This request is sent to a resource which does not hold information about causative genes but does hold information about diagnoses and sex, an example response which could be returned is outlined below:

**EXAMPLE RESPONSE**

```JSON
{
  "meta": {
  "apiVersion": "v2.0",
    "beaconId": "Responding unique Beacon ID in reverse domain name notation",
    "returnedSchemas": {
      "entityType": "string",
      "schema": "string"
    }
  },
  "responseSummary":{
    "exists": "true",
    "numTotalResults": 10
  },
  "info": { 
    "warnings":{
      "unsupportedFilters": [
        "data_2295"
      ]
    }
  }
}
```

This response provides a warning message within the info section advising of unsupported filters which were ignored when the query was processed by the resources query engine. Please see the info part of the [IndividualResponse](https://app.swaggerhub.com/apis/VM172_1/vp_individuals/v0.2#/IndividualResponse) schema on swagger. 

[ ^ Back to the top](#top)

<hr>

<h2 id="info-endpoints"> Informational Endpoints </h2>

> Method : GET

The following endpoints respond with basic information related to this Beacon Implementation.

##### /info: 
Get information about the Beacon.
##### /service-info: 
Get this Beacon's basic metadata concerning its service, based on the [reference specification](https://github.com/ga4gh-discovery/ga4gh-service-info/).
##### /configuration: 
Get Beacon Configuration.
##### /entry_types: 
Get list of entry types in this Beacon.
##### /filtering_terms: 
Get information about available individual filtters for this beacon's entry types.
##### /map: 
Get the Beacon map with information related to the list of endpoints included in this Beacon instance.

[ ^ Back to the top](#top)

<hr>

<h2 id="deprecated-filters"> Deprecated Filters </h2>

These filters are currently supported, but will be removed from from future versions of this specification

<table>
<thead>
  <tr>
    <th>Filter name</th>
    <th>Type term</th>
    <th>Permitted values</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td rowspan="4">Sex</td>
    <td rowspan="4">obo:NCIT_C28421</td>
    <td>obo:NCIT_C16576</td>
  </tr>
  <tr>
    <td>obo:NCIT_C20197</td>
  </tr>
  <tr>
    <td>obo:NCIT_C124294</td>
  </tr>
  <tr>
    <td>obo:NCIT_C17998</td>
  </tr>
  <tr>
    <td>Diagnosis of the rare<br />disease</td>
    <td>sio:SIO_001003</td>
    <td>Any orphanet term. <b>e.g. ordo:Orphanet_558</b></td>
  </tr>
  <tr>
    <td>Phenotype</td>
    <td>sio:SIO_010056</td>
    <td>Any hpo term. <b>e.g. obo:HP_0001251</b></td>
  </tr>
  <tr>
    <td>Causative genes</td>
    <td>obo:NCIT_C16612</td>
    <td>any HGNC gene symbol</td>
  </tr>
  <tr>
    <td>Age this year</td>
    <td>obo:NCIT_C25150</td>
    <td>any integer</td>
  </tr>
  <tr>
    <td>Age at disease manifestation</td>
    <td>efo:EFO_0004847</td>
    <td>any integer</td>
  </tr>
  <tr>
    <td>Age at diagnosis</td>
    <td>obo:NCIT_C156420</td>
    <td>any integer</td>
  </tr>
  <tr>
    <td rowspan="9">Available materials</td>
    <td rowspan="9"></td>
        <td>Whole Genome Sequence</td>
  </tr>
  <tr>
    <td>Whole Exome Sequence</td>
  </tr>
  <tr>
    <td>Exome panel sequence</td>
  </tr>
  <tr>
    <td>RNA sequence</td>
  </tr>
  <tr>
    <td>methylomics</td>
  </tr>
  <tr>
    <td>Epigenomics</td>
  </tr>
  <tr>
    <td>Pedigree data</td>
  </tr>
  <tr>
    <td>Clinical data</td>
  </tr>
  <tr>
    <td>Biosamples</td>
  </tr>
</tbody>
</table>

[ ^ Back to the top](#top)

<hr>

<h2 id="swagger-auth"> Authentication using Header for Swagger </h2>

In Swagger, to query using the /individuals endpoint (which is a POST request), you have to authorize the query using the Authorize button beside available servers. 

![image](https://user-images.githubusercontent.com/24955128/203320000-a9cbc5a5-4c49-4a2b-8666-4e0cb17a5a62.png)

![image](https://user-images.githubusercontent.com/24955128/205334330-c90f760d-5d1e-4685-8640-030787233454.png)

Use one of the authentication token provided to perform record level queries.
![image](https://user-images.githubusercontent.com/24955128/205334443-fce92738-a515-4f0e-8f9f-b701561c283b.png)

> Developers Note: No matter the user agent being used to query (i.e., SwaggerUI, Postman, cURL, etc.,), the authentication header **auth-key is required**. 

[ ^ Back to the top](#top)

<hr>
