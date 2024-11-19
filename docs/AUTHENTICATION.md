# Authentication using Header 

Since the specification allows for record level queries of individuals, additional information is required in the request header to verify the requester is authorised:

<table>
<thead>
<th>Header Attribute</th>
<th>Header Value</th>
<th>Purpose</th>
</thead>
<tbody>
<tr><td>auth-key</td><td>Token provided by resource</td><td>Indicates requester is authorised (required)</td></tr>
<tr><td>Authorization</td><td>Bearer Token</td><td>The access token issued by LS AAI</td></tr>
</tbody>
</table>

For now, the auth key is still used for some resources, but it will soon be deprecated and replaced by Bearer tokens. The absence of a token indicates that a user has not logged in. The tokens are issued by LS AAI using the OIDC protocol. Resources should register themselves with LS AAI. To validate the token, both the VP Portal and the resources need to know each other's client IDs.

Configuration URL: https://login.aai.lifescience-ri.eu/oidc/.well-known/openid-configuration
VP Portal Client ID: 5c13620c-da38-4c9b-bb79-c4c278c2f64c
OIDC realm: https://login.aai.lifescience-ri.eu/oidc/


> **Note:** Presence of a bearer token is equivalent to auth-token:True

> **Note:** Implementers can provide distinct auth-keys to each requester or a single auth-key to all requesters.

To see this live in action in Swagger UI, see [Authentication in Swagger](#-authentication-using-header-for-swagger-).
