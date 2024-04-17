<h3 id="-biosamples-endpoint"> Biosamples endpoint</h3>

> **HTTP Request Method : POST**

Similarly to the `/individuals` endpoint, `/biosamples` endpoint returns the **__maximum value of biosamples within a specified range__** from an RD resource (usually the resource is a Biobank). Queries are performed in the same way as for `/individuals` endpoint, adding filters in the body of the request.

Please **do not use HTTP GET method** to query the biosamples endpoint, as it is **not permitted** per this specification, and will result in a 403 error response.

<h4 id="-list-of-filters-and-permitted-values-for-the-biosamples-endpoint-"> List of filters and permitted values for the biosamples endpoint </h4>

> **Note**: Elements within arrays in **value** fields are treated as **ORs**

<table>
<thead>
        <th>Concept</th>
        <th>Ontological Term</th>
        <th>Beacon Filter Type</th>
        <th>ID</th>
        <th>Operator</th>
        <th>Permitted Values</th>
</thead>
<tbody>
    <tr>
        <td rowspan="5"><b>Sex</b></td>
        <td rowspan="5">obo:NCIT_C28421</td>
        <td rowspan="5">Alphanumerical</td>
        <td rowspan="5">ncit:C28421</td>
        <td rowspan="5">=</td>
        <td>ncit:C16576</td>
    </tr>
    <tr><td>ncit:C20197</td></tr>
    <tr><td>ncit:C124294</td></tr>
    <tr><td>ncit:C17998</td></tr>
    <tr><td>An array of any of the above</td></tr>
    <tr>
        <td><b>Disease or Disorder</b></td>
        <td>obo:NCIT_C2991</td>
        <td>Ontology</td>
        <td>A single value or an array of orphanet terms. <b>e.g. ordo:Orphanet_558 or [ordo:Orphanet_558, ordo:Orphanet_773]</b></td>
        <td colspan="2">NA</td>
    </tr>
    <tr>
        <td><b>Year of birth</b></td>
        <td>obo:NCIT_C83164</td>
        <td>Numerical</td>
        <td>ncit:C83164 </td>
        <td>=, &gt;=, &gt;, &lt;=, &lt;</td>
        <td>any integer</td>
    </tr>
    <tr>
        <td><b>Age at diagnosis</b></td>
        <td>obo:NCIT_C156420</td>
        <td>Numerical</td>
        <td>ncit:C156420</td>
        <td>=, &gt;=, &gt;, &lt;=, &lt;</td>
        <td>any integer</td>
    </tr>
    <tr>
        <td rowspan="20"><b>Biospecimen Type</b></td>
        <td rowspan="20">obo:NCIT_C70713</td>
        <td rowspan="20">Alphanumerical</td>
        <td rowspan="20">ncit:C70713</td>
        <td rowspan="20">=</td>
        <td>obi:0000655 (blood specimen)</td>
    </tr>
    <tr><td>obi:0002512 (bone marrow)</td></tr>
    <tr><td>OBIB:0000036 (buffy coat)</td></tr>
    <tr><td>cl:2000001 (peripheral blood mononuclear cell)</td></tr>
    <tr><td>obi:0100016 (blood plasma specime)</td></tr>
    <tr><td>obi:0100017 (blood serum)</td></tr>
    <tr><td>uberon:0007795 (ascites fluid)</td></tr>
    <tr><td>obi:0002502 (cerebrospinal fluid)</td></tr>
    <tr><td>obi:0002507 (saliva)</td></tr>
    <tr><td>obi:0002503 (feces)</td></tr>
    <tr><td>obi:0000651 (urine)</td></tr>
    <tr><td>obi:0002599 (swab)</td></tr>
    <tr><td>obi:2000009 (bodily fluid specimen)</td></tr>
    <tr><td>obi:1200000 (FFPE specimen)</td></tr>
    <tr><td>obi:0000922 (frozen specimen)</td></tr>
    <tr><td>obi:0001472 (specimen with known storage state)</td></tr>
    <tr><td>obi:0001051 (DNA extract)</td></tr>
    <tr><td>obi:0000880 (RNA extract)</td></tr>
    <tr><td>obi:0001479 (specimen from organism)</td></tr>
    <tr><td>An array of any of the above</td></tr>    
</tbody>
</table>

[ ^ Back to the top](#top)

<hr>

<h3 id="-biosamples-filters-description-"> Biosamples Filters Description </h3>

**Sex**: The biological sex of the person the biosample belongs to.

**Disease or Disorder**: All rare diseases that have been diagnosed **from the biosample**, to encompase, but not distinguish between all levels of diagnosis such as definitive, differential, provisional, etc.,

**Year of birth**: The year of birth of the person who the biosample belongs to

**Age at diagnosis**: Age at the diagnosis of a rare disease. For biosamples with more than one rare disease, this filter will look at all age of manifestations independently. -/+ will be added to all age queries when executed by the query engine at the resource.

**Biospecimen Type**: One or more biospecimen type. The list is a list of ontology terms taken from MIABIS

[ ^ Back to the top](#top)

<hr>

<h3 id="-example-request-and-response-for-biosamples-"> Example request and response for biosamples </h3>

**EXAMPLE /biosamples REQUEST**

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
                "id": "ncit:C70713",
                "operator": "=",
                "value": "obi:0000655"
              }
        ],
        "requestedGranularity": "count"
    }
}
```

**EXAMPLE /biosamples RESPONSE**


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
                    "id": "ncit:C70713",
                    "operator": "=",
                    "value": "obi:0000655"
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
[Notes](#-notes-) about the `resultCount` and unsupported filters for the `/individuals` endpoint apply also for `biospecimens`

[ ^ Back to the top](#top)
