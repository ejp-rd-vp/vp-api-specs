# REST API specification for querying RD resources (record level queries)
![GitHub](https://img.shields.io/github/license/ejp-rd-vp/vp-api-specs)
![GitHub tag (latest by date)](https://img.shields.io/github/v/tag/vp-api-specs)

> This API specifications are defined in the contrast of the EJPRD project.

In this work we present API specifications for querying RD patient registries. We assume that the patient registries which implements this specification collects   [the set of common data elements for rare diseases registration](https://eu-rd-platform.jrc.ec.europa.eu/sites/default/files/CDS/EU_RD_Platform_CDS_Final.pdf) recommended by the European commission Joint Research Centre. In this specifications we also make use of ontological terms recommended by the [CDE semantic data model group](https://github.com/ejp-rd-vp/CDE-semantic-model)

## Specification
### individuals endpoint
> Method : POST

[/individuals](https://github.com/ejp-rd-vp/vp-api-specs/blob/develop/individuals_api.yml) endpoint returns count of individuals from a patient registry. This endpoint specification is drafted based on [beacon-v2 API specification](https://github.com/ga4gh-beacon/beacon-v2). The request can also contain filters which are CDE based filter parameters to filter individuals. These filters are provided as a part of request body. An example filters json is shown below.

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

##### List of all filters and allowed values
-- TODO--

## Helpful Tools

- The [Swagger Software](https://swagger.io "https://swagger.io") offers several free tools that help build APIs that are compliant with the OpenAPI specification:

  - [Swagger Editor](https://swagger.io/tools/swagger-editor/ "https://swagger.io/tools/swagger-editor/") can be used to build valid API calls.
  - [Swagger Inspector](https://inspector.swagger.io/builder "https://inspector.swagger.io/builder") is a browser extension that can be used to validate API calls.
  
- [OpenAPI Examples](https://github.com/OAI/OpenAPI-Specification/tree/master/examples "https://github.com/OAI/OpenAPI-Specification/tree/master/examples")