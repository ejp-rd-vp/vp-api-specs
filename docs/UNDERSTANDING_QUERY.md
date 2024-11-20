# Understanding the query

This section explains how to perform queries to Beacon services compliant with the specs.

* [Syntax & Usage of Beacon Query with Filters](#syntax-and-usage)
  * [Multi-Filter (AND) query](#beacon-queries-using-multiple-filters-and-logical-operator-between-filters)
  * [Same Filter (AND) query](#beacon-queries-using-multiples-of-the-same-type-of-filter-and-logical-operator-between-filters)
  * [Multi-Filter (OR) query using **Arrays**](#beacon-queries-using-multiples-of-the-same-type-of-filter-or-logical-operator-between-filters)
* [Partial query matches with warning messages](#partial-request-and-response-with-warning-message-example)

## Syntax and Usage

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

[ ^ Back to the top](#understanding-the-query)

As shown above, different types of filters can be sent in a single query. These are further elucidated below. 

### Beacon queries using multiple filters (AND logical operator between filters)

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


[ ^ Back to the top](#understanding-the-query)

### Beacon queries using multiples of the same type of filter (AND logical operator between filters)

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

[ ^ Back to the top](#understanding-the-query)

### Beacon queries using multiples of the same type of filter (OR logical operator between filters)

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

[ ^ Back to the top](#understanding-the-query)

## Partial Request and Response with warning message Example

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
          "operator": "=",
          "value": "ACTN4"
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
         "setType": "dataset",
         "exists": true,
         "resultCount": 20,
         "results":[{}],
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
         "edam:data_2295"
      ]
    }
  }
}
```

This response provides a warning message within the info section advising of unsupported filters which were ignored when the query was processed by the resources query engine. Please see the info part of the [IndividualResponse](https://app.swaggerhub.com/apis/DVS6_1/virtual-platform_beacon_api/v2.0#/IndividualResponse) schema on swagger. 


[ ^ Back to the top](#understanding-the-query)