# Version4 Specifications

# REST API specification for querying RD resources (Virtual Platform Level 2)

> This API specification is defined in the context of the EJPRD project, complying with the latest [Beacon v2 Specification](https://github.com/ga4gh-beacon/beacon-v2).

In this work, we present API specification for querying RD patient registries, biobanks and similar resources at the safe record level (i.e, resources whose available assets are described by RD patient data). Resources that implement this specification would ideally collect data based on the set of common data elements for rare diseases registration, as recommended by the European commission Joint Research Centre.
<hr>

## Specification

The request and response conforms to the [Beacon Reference Framework](https://github.com/ga4gh-beacon/beacon-v2). This Specification defines two types of endpoints - **[The Query Endpoints](./docs/QUERY_ENDPOINTS.md)** and **[The Informational Endpoints](./docs/INFORMATIONAL_ENDPOINTS.md)**. 

[Informational Endpoints](./docs/INFORMATIONAL_ENDPOINTS.md) are simple GET requests without needing a request body, and respond with information relavant to this Beacon Specification. These are: /info, /configuration, /entry_types, /filtering_terms and /map. A special /service-info endpoint (also a GET request), responds with metadata relevant to this Beacon using the [GA4GH ServiceInfo format](https://github.com/ga4gh-discovery/ga4gh-service-info/). 

[Query Endpoints](./docs/QUERY_ENDPOINTS.md) require the requester to provide a JSON body and send request using the POST method. This document defines three query endpoints to query resources using filters - [/individuals](./docs/INDIVIDUALS.md), [/biosamples](./docs/BIOSAMPLES.md) and [/catalogs](./docs/CATALOGS.md). 

## Contents

* [Query Endpoints](./docs/QUERY_ENDPOINTS.md)
    * [Individuals endpoint](./docs/INDIVIDUALS.md)
      * [List of filters](./docs/INDIVIDUALS.md#list-of-filters-and-permitted-values-for-the-individuals-endpoint)
      * [Filters description](./docs/INDIVIDUALS.md#individuals-filters-description)
      * [Example request & response](./docs/INDIVIDUALS.md#example-request-and-response)
    * [Biosamples endpoint](./docs/BIOSAMPLES.md)
      * [List of filters](./docs/BIOSAMPLES.md#list-of-filters-and-permitted-values-for-the-biosamples-endpoint)
      * [Filters description](./docs/BIOSAMPLES.md#biosamples-filters-description)
      * [Example request & response](./docs/BIOSAMPLES.md#example-request-and-response-for-biosamples)
    * [Catalogs endpoint](./docs/CATALOGS.md)
      * [Version 1.0](./docs/CATALOGS.md#version-10)
        * [List of filters](./docs/CATALOGS.md#list-of-filters-and-permitted-values-for-version-10)
        * [Resources schema](./docs/CATALOGS.md#resources-schema-for-version-10)
        * [Example request & response](./docs/CATALOGS.md#example-request-and-response-for-version-10)
      * [Version 2.0](./docs/CATALOGS.md#version-20)
        * [List of filters](./docs/CATALOGS.md#list-of-filters-and-permitted-values-for-version-20)
        * [Resource schema](./docs/CATALOGS.md#resources-schema-for-version-20)
        * [Example request & response](./docs/CATALOGS.md#example-request-and-response-for-version-20)     
* [Authentication using Header(s)](./docs/AUTHENTICATION.md)
* [Understanding the query](./docs/UNDERSTANDING_QUERY.md)
    * [Syntax & Usage of Beacon Query with Filters](./docs/UNDERSTANDING_QUERY.md#syntax-and-usage)
        * [Multi-Filter (AND) query](./docs/UNDERSTANDING_QUERY.md#beacon-queries-using-multiples-of-the-same-type-of-filter-and-logical-operator-between-filters)
        * [Same Filter (AND) query](./docs/UNDERSTANDING_QUERY.md#beacon-queries-using-multiple-values-as-in-phenotype-or-disease-filters-or-logical-operator-between-filter-values)
        * [Multi-Filter (OR) query using **Arrays**](./docs/UNDERSTANDING_QUERY.md#beacon-queries-using-multiples-of-the-same-type-of-filter-or-logical-operator-between-filters)
    * [Partial query matches with warning messages](./docs/UNDERSTANDING_QUERY.md#partial-request-and-response-with-warning-message-example)
* [Understanding the response with ranges (for /individuals and /biospecimens)](./docs/UNDERSTANDING_RESPONSE_WITH_RANGES.md)
* [Informational Endpoints](./docs/INFORMATIONAL_ENDPOINTS.md)
* [Authentication Using Header for Swagger](./docs/AUTHENTICATION_SWAGGER.md)

## Try out the API

Latest version (v4.0) of this specification is available on Swagger here: https://app.swaggerhub.com/apis/DVS6_1/virtual-platform_beacon_api/v4.0
