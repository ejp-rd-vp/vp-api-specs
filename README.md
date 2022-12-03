# REST API specification for querying RD resources (record level queries)
![GitHub](https://img.shields.io/github/license/ejp-rd-vp/vp-api-specs)
![GitHub tag (latest by date)](https://img.shields.io/github/v/tag/vp-api-specs)

> This API specification is defined in the context of the EJPRD project, complying with the latest [Beacon v2 Specification](https://github.com/ga4gh-beacon/beacon-v2).

In this work, we present API specification for querying RD patient registries, biobanks and similar resources at the safe record level (i.e, resources whose available assets are described by RD patient data). Resources that implement this specification would ideally collect data based on the set of common data elements for rare diseases registration, as recommended by the European commission Joint Research Centre. In this specification, where possible, we also make use of ontological terms recommended by the CDE semantic data model group.

## Try out the API:
See this API come to life in Swagger [here](https://app.swaggerhub.com/apis/VM172_1/vp_individuals/v1.0#/).

# Specification
The request and response conforms to the [Beacon Reference Framework](https://github.com/ga4gh-beacon/beacon-v2). This Specification defines two types of endpoints - **The Informational Endpoints** and **The Query Endpoints**. 

Informational Endpoints are simple GET requests without needing a request body, and respond with information relavant to this Beacon Specification. These are: /info, /configuration, /entry_types, /filtering_terms and /map. A special /service-info endpoint (also a GET request), responds with metadata relevant to this Beacon using the [GA4GH ServiceInfo format](https://github.com/ga4gh-discovery/ga4gh-service-info/). 

This specification defines POST endpoints (aka Query Endpoints) to request information about resources. Each endpoint makes use of the [Filters](http://docs.genomebeacons.org/filters/) capability of Beacon API, and the following filters are to be used to query resources in EJP:

<h4 id="filters_table"> List of filters and permitted values</h4>

**(Arrays are treated as ORs)**

<table>
    <thead><tr>
        <th>CDE Concept</th><th>CDE Term</th>
        <th>Beacon Filter Type</th>
        <th>ID</th>
        <th>Operator</th>
        <th>Permitted Values</th>
        <th>Endpoint Use</th>
    </tr>
    </thead>
    <tbody>
    <tr>
      <td rowspan="4"><b>Sex</b></td><td rowspan="4">obo:NCIT_C28421</td>
        <td rowspan="4">Alphanumerical</td>
        <td rowspan="4">NCIT_C28421</td>
        <td rowspan="4">=</td>
        <td>NCIT_C16576</td>
        <td rowspan="4">Individuals</td>
    </tr>
    <tr>
        <td>NCIT_C20197</td>
    </tr>
    <tr>
        <td>NCIT_C124294</td>
    </tr>
    <tr>
        <td>NCIT_C17998</td>
    </tr>
    <tr>
      <td><b>Disease or Disorder</b></td><td>obo:NCIT_C2991</td>
        <td>Ontology</td>
        <td>A single value or an array of orphanet terms. <b>e.g. Orphanet_558 or [Orphanet_558, Orphanet_773]</b></td>
        <td>NA</td>
        <td>NA</td>
        <td>Individuals/Catalog</td>
    </tr>
    <tr>
      <td><b>Phenotype</b></td><td>sio:SIO_010056</td>
        <td>Ontology</td>
        <td>A single value or an array of HPO terms. <b>e.g. HP_0001251 or [HP_0001251, HP_0012250]</b></td>
        <td>NA</td>
        <td>NA</td>
        <td>Individuals/Catalog</td>
    </tr>
    <tr>
      <td><b>Causative Genes</b></td><td>edam:data_2295</td>
        <td>Alphanumerical</td>
        <td>data_2295 </td>
        <td>=</td>
        <td>any HGNC gene symbol</td>
        <td>Individuals/Catalog</td>
    </tr>
    <tr>
      <td><b>Age this year</b></td><td>obo:NCIT_C83164</td>
        <td>Numerical</td>
        <td>NCIT_C83164 </td>
        <td>=, &gt;=, &gt;, &lt;=, &lt;</td>
        <td>any birth year as an integer</td>
        <td>Individuals</td>
    </tr>
    <tr>
      <td><b>Symptom Onset</b></td><td>obo:NCIT_C124353</td>
        <td>Numerical</td>
        <td>NCIT_C124353</td>
        <td>=, &gt;=, &gt;, &lt;=, &lt;</td>
        <td>any integer</td>
        <td>Individuals</td>
    </tr>
    <tr>
      <td><b>Age at diagnosis</b></td><td>obo:NCIT_C156420</td>
        <td>Numerical</td>
        <td>NCIT_C156420</td>
        <td>=, &gt;=, &gt;, &lt;=, &lt;</td>
        <td>any integer</td>
        <td>Individuals</td>
    </tr>
    <tr>
  <td rowspan="9"><b>Available Materials</b></td><td rowspan="9">NA</td>
        <td rowspan="9"><phanumerical</td>
        <td rowspan="9">Available Materials</td>
        <td rowspan="9">=</td>
    </tr>
    <tr>
        <td>Whole Genome Sequence</td>
         <td rowspan="8">Individuals/Catalog</td>
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
        <td><b>ID</td><td>NA</td>
    <td>Alphanumerical</td>
    <td>id</td>
        <td>=</td>
        <td>any String</td>
        <td>Catalog</td>
</tr>
            <tr>
        <td><b>Name</td><td>NA</td>
    <td>Alphanumerical</td>
    <td>name</td>
        <td>=</td>
        <td>any String</td>
        <td>Catalog</td>
</tr>
            <tr>
        <td><b>Description</td><td>NA</td>
    <td>Alphanumerical</td>
    <td>description</td>
        <td>=</td>
        <td>any String</td>
        <td>Catalog</td>
</tr>
            <tr>
        <td><b>Organisation</td><td>NA</td>
    <td>Alphanumerical</td>
    <td>organisation</td>
        <td>=</td>
        <td>any String</td>
        <td>Catalog</td>
</tr>
            <tr>
      <td rowspan="3"><b>Resource Types</b></td><td rowspan="3">NA</td>
        <td rowspan="3">Alphanumerical</td>
        <td rowspan="3">resourceTypes</td>
        <td rowspan="3">=</td>
        <td>PatientRegistryDataset</td>
        <td rowspan="3">Catalog</td>
    </tr>
    <tr>
        <td>BiobankDataset</td>
    </tr>
    <tr>
        <td>KnowledgeBase</td>
    </tr>
    </tbody>
</table>


The request body and response format are described [here](#request_body).

More details on Informational Endpoints [here](#info) and Query Endpoints [here](#query).

<h2 id="auth_header"> Authentication using Header </h2>

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

## Understanding the filters


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

## Understanding the query

There are 3 types of Beacon query that this specification currently supports: 
1. [Ontology Query](http://docs.genomebeacons.org/filters/#simple-curie-based-filters-query)
2. [Alphanumerical Query](http://docs.genomebeacons.org/filters/#exact-value-query)
3. [Numerical Query](http://docs.genomebeacons.org/filters/#numerical-value-query)

<h5>Beacon queries require the use of the AND logical operator between filters:</h5>

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

<h5>Beacon queries using multiples of the same type of filter:</h5>
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

<h5>Beacon queries using multiples values as an OR in phenotype or disease filters</h5>

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

The warning messages will be provided within the 'info' section of the Beacon.

<h2 id="query">Query Endpoints</h2>

### individuals endpoint
> Method : POST

[/individuals](https://github.com/rini21/vp-api-specs-beaconised/blob/main/individuals_api.yml) endpoint returns the count of individuals from a RD resource. Filters are provided as a part of the request body. An example request JSON is shown below.

<h5 id="request_body"> Query Request Body: </h5>

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

**EXAMPLE REQUEST**

```JSON
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "meta":{
  "apiVersion": "v2.0",
    "beaconId": "Unique Beacon ID in reverse domain name notation",
    "returnedSchemas": {
      "entityType": "string",
      "schema": "string"
    }
  },
  "query":{
    "requestParameters": [],
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

**RESPONSE**


```JSON
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
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
The filter **SHOULD** be one of the terms from the [filters and permitted values table](#filters_table). Please note that not all resources will support all of the filters. In such cases the response should include a [warning message in the 'info' part](#info_response) indicating which requested filters are unsupported and these were not included in the query.


> Method : POST

[/catalogs] endpoint returns the metadata of RD resource. Filters are provided as a part of the request body. An example request JSON is shown below.

<h5 id="request_body"> Query Request Body: </h5>

```JSON
{ 
"$schema": "https://json-schema.org/draft/2020-12/schema",
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

**RESPONSE**
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

<h2 id="info_response"> Example Beacon usage with warning messages </h2>

**REQUEST**

```JSON
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "meta":{
  "apiVersion": "v2.0",
    "beaconId": "Unique Beacon ID in reverse domain name notation",
    "returnedSchemas": {
      "entityType": "string",
      "schema": "string"
    }
  },
  "query":{
    "requestParameters": [],
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

```JSON
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
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

This response provides a warning message within the info section advising of unsupported filters which were ignored when the query was processed by the resources query engine. Please see the info part of [IndividualResponse](https://app.swaggerhub.com/apis/VM172_1/vp_individuals/v1.0#/IndividualResponse) schema on swagger. 


<h2 id="deprecated filters"> Deprecated Filters </h2>
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


<h2 id="swagger_auth_header"> Authentication using Header for Swagger </h2>
In Swagger, to query using the /individuals endpoint (which is a POST request), you have to authorize the query using the Authorize button beside available servers. 

![image](https://user-images.githubusercontent.com/24955128/203320000-a9cbc5a5-4c49-4a2b-8666-4e0cb17a5a62.png)

![image](https://user-images.githubusercontent.com/24955128/205334330-c90f760d-5d1e-4685-8640-030787233454.png)

Use one of the authentication token provided to perform record level queries.
![image](https://user-images.githubusercontent.com/24955128/205334443-fce92738-a515-4f0e-8f9f-b701561c283b.png)

> Developers Note: No matter the user agent being used to query (i.e., SwaggerUI, Postman, cURL, etc.,), the authentication header **auth-key is required**. 

<h2 id="info"> Informational Endpoints </h2>

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
