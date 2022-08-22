# REST API specification for querying RD resources (record level queries)
![GitHub](https://img.shields.io/github/license/ejp-rd-vp/vp-api-specs)
![GitHub tag (latest by date)](https://img.shields.io/github/v/tag/vp-api-specs)

> This API specifications are defined in the context of the EJPRD project.

In this work we present API specifications for querying RD patient registries, biobanks and similar resources at the safe record level (i.e, resources whose available assets are described by RD patient data). Resources that implement this specification would ideally collect data based on the set of common data elements for rare diseases registration, as recommended by the European commission Joint Research Centre. In this specification, where possible, we also make use of ontological terms recommended by the CDE semantic data model group.

## Specification
### individuals endpoint
> Method : POST

[/individuals](https://github.com/ejp-rd-vp/vp-api-specs/blob/main/individuals_api.yml) endpoint returns the count of individuals from a RD resource. This endpoint specification is drafted based on [beacon-v2 API specification](https://github.com/ga4gh-beacon/beacon-v2). The request can also contain filters which are CDE based filter parameters to filter individuals. These filters are provided as a part of request body. An example filters json is shown below.

```JSON
"query": {
      "description": "Query to get count of female (obo:NCIT_C16576) individuals with diagnostic opinion (sio:SIO_001003)  marfan syndrome (ordo:Orphanet_558)",
      "filters": [
        {
          "type": "obo:NCIT_C28421",
          "id": "obo:NCIT_C16576",
          "operator": "="
        },
        {
          "type": "sio:SIO_001003",
          "id": "ordo:Orphanet_558",
          "operator": "="
        }
      ]
    }
```   

##### List of filters and permitted values
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


## Understanding the filters


All filters are to be handled independently, see below example for clarification.

```JSON
"query": {
      "filters": [
        {
          "type": "	sio:SIO_001003",
          "id": "ordo:Orphanet_34587",
          "operator": "="
        },
        {
          "type": "obo:NCIT_C16612",
          "id": "LAMP2",
          "operator": "="
        }
      ]
    }
```

This filter is asking for individuals which have been diagnosed with Danon disease (ordo:Orphanet_34587) and where LAMP2 gene has been identified as causative. These filters are handled independently, this means that individuals with danon disease where LAMP2 has been identified as a causative gene, specifically for Danon disease, will match the query. It also means that individuals with Danon disease and where LAMP2 has been identified as a causative gene for a second rare disease, other than Danon disease, will also match this query.


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
    <td>Diagnosis of the rare<br />disease</td>
    <td>All rare diseases that have been diagnosed within an individual, to encompase, but not distinguish between, all levels of diagnosis such as definitive, differential, provisional etc.</td>
    <td>To be handled independently of other filters</td>
  </tr>
  <tr>
    <td>Phenotypes observed</td>
    <td>HPO terms of all phenotypes observed within an individual</td>
    <td>To be handled independently of other filters</td>
  </tr>
  <tr>
    <td>Causative genes</td>
    <td>All genes which have been deemed as causative of one or more of the diagnosed rare diseased, encompassing and not distinguishing between all certainty levels of causality</td>
    <td>To be handled independently of other filters</td>
  </tr>
  <tr>
    <td>Age this year</td>
    <td>Age at the end of the current year</td>
    <td>-/+ 1 will be added to all age queries when executed by the query engine at the resource</td>
  </tr>
  <tr>
    <td>Age at disease <br /> manifestation</td>
    <td>Age at the manifestation of a rare disease</td>
    <td>For individuals with more than one rare disease this filter will look at all age of manifestations independently. <br /> -/+ 1 will be added to all age queries when executed by the query engine at the resource</td>
  </tr>
  <tr>
    <td>Age at diagnosis</td>
    <td>Age at the diagnosis of a rare disease</td>
    <td>For individuals with more than one rare disease this filter will look at all age of diagnosis independently. <br /> -/+ 1 will be added to all age queries when executed by the query engine at the resource</td>
  </tr>
  <tr>
    <td>Available materials</td>
    <td>A list of what information is available about an individual</td>
    <td></td>
  </tr>
</tbody>
</table>

## Understanding the query

Beacon queries require the use of the AND logical operator between filters and each filter can have at most one possible value.

To query for individuals with more than one instance of any of the filters you can send multiple of the same filter, such as in the below example:

```JSON
"query": {
      "filters": [
        {
          "type": "	sio:SIO_001003",
          "id": "ordo:Orphanet_34587",
          "operator": "="
        },
        {
          "type": "	sio:SIO_001003",
          "id": "ordo:Orphanet_1653",
          "operator": "="
        }
      ]
```

This query is looking for individuals with Danon disease ("ordo:Orphanet_34587") AND Dentin dysplasia ("ordo:Orphanet_1653").

There are no OR operators available with beacon queries.

All of the defined filters are optional, the user can provide as many or as few as wanted and the resource does not have to implement all filters.

If a user sends a query with a filter not supported by a resource then the resource should complete the query but ignore the unsupported filter(s) and respond as usual, but with a warning noting that certain filters were ignored as they are unsupported.

The warning messages will be provided within the 'info' section of the Beacon.

## Example Beacon usage with warning messages

An example request which can be sent to a resource is shown below:


```JSON
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "meta":{},
  "query":{
    "requestParameters": [],
    "filters": [
        {
          "type": "	sio:SIO_001003",
          "id": "ordo:Orphanet_34587",
          "operator": "="
        },
        {
          "type": "obo:NCIT_C16612",
          "id": "LAMP2",
          "operator": "="
        },
        {
          "type": "obo:NCIT_C28421",
          "id": "obo:NCIT_C16576",
          "operator": "="
        },
    ]
  }
}
```
This request is asking for females with Danon disease with LAMP2 being identified as a causative gene.

This request is sent to a resource which does not hold information about causative genes but does hold information about diagnoses and sex, an example response which could be returned is outlined below:

```JSON
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "meta": {},
  "responseSummary":{
    "exists": "true",
    "numTotalResults": 10
  },
  "info": {
    "warnings":{
      "unsupportedFilters": [
        "obo:NCIT_C16612"
      ]
    }
  }
}
```

This response provides a warning message within the info section advising of unsupported filters which were ignored when the query was processed by the resources Beacon engine.    

## Helpful Tools

- The [Swagger Software](https://swagger.io "https://swagger.io") offers several free tools that help build APIs that are compliant with the OpenAPI specification:

  - [Swagger Editor](https://swagger.io/tools/swagger-editor/ "https://swagger.io/tools/swagger-editor/") can be used to build valid API calls.
  - [Swagger Inspector](https://inspector.swagger.io/builder "https://inspector.swagger.io/builder") is a browser extension that can be used to validate API calls.
  
- [OpenAPI Examples](https://github.com/OAI/OpenAPI-Specification/tree/master/examples "https://github.com/OAI/OpenAPI-Specification/tree/master/examples")


