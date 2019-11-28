# bankid-statuspage-api

The BankID Statuspage API is a REST service delivering information about operational status for BankID.
The response is a direct mirror of the JSON feed from a [statuspage](https://www.statuspage.io/) set up for this purpose.

The status overview is build up of four components:

* BankID
* BankID p� mobil
* BankID p� mobil for Telenor
* BankID p� mobil for Telia

"BankID p� mobil" is an aggregation of the "Telenor" and "Telia" components.

Authorization for the service is handled by the [BankID OIDC platform](https://confluence.bankidnorge.no/confluence/pdoidcl/introduction).

## Provisioning
BankID merchants and bank partners are eligible for access to the service.
Order access by filling out the form at the [BankID production service desk](https://servicedesk.bankidnorge.no/jira/servicedesk/customer/portal/3/group/9).

## Implementation guide

Step by step guide for how to integrate with the service.

### 1 Retrieve access token

Get access_token from BankID OIDC.

| HTTP Method | Production URL |
|---|---|
| GET | https://oidc.bankidapis.no/auth/realms/prod/protocol/openid-connect/token |

Required parameters:

| Body x-www-form-urlencoded  | Value|
|---|---|
|grant_type|client_credentials|
|scope|operational-status/read|
|client_id|\<client_id\>|
|client_secret|\<secret\>|

The response is a JSON with the access_token as one of multiple response elements.

More documentation about BankID OIDC token API:
[https://confluence.bankidnorge.no/confluence/pdoidcl/technical-documentation/rest-api/token](https://confluence.bankidnorge.no/confluence/pdoidcl/technical-documentation/rest-api/token)

### 2 Call the service

| HTTP Method | Production URL |
|---|---|
| GET | https://preview.bankidapis.no/operational-status/api/status |

Requires authorization header with Bearer token:

 | Header  | Value|
|---|---|
|Authorization|"Bearer \<access_token\>"|
 
## Request rate limit

Please limit the request rate to no more than **one request every 30 seconds**.

## Example response

Note that the response element component\[status\] is an enumeration of the following set:

|component\[status\]|
|---|
|operational|
|degraded_performance|
|partial_outage|
|major_outage|
|under_maintenance|

```
{
   ?page?: {
       ?id?: ?...?,
       ?name?: ?BankID?,
       ?url?: ?...statuspage.io?,
       ?time_zone?: ?Europe/Copenhagen?,
       ?updated_at?: ?2019-11-11T16:46:44.154+01:00"
   },
   ?components?: [
       {
           ?id?: ?...?,
           ?name?: ?BankID p� mobil for Telenor?,
           ?status?: ?operational?,
           ?created_at?: ?2016-10-13T01:48:15.190+02:00?,
           ?updated_at?: ?2019-11-11T16:46:44.107+01:00?,
           ?position?: 1,
           ?description?: null,
           ?showcase?: true,
           ?group_id?: ?...?,
           ?page_id?: ?...?,
           ?group?: false,
           ?only_show_if_degraded?: false
       },
       {
           ?id?: ?...",
           ?name?: ?BankID?,
           ?status?: ?operational?,
           ?created_at?: ?2016-10-13T01:48:15.166+02:00",
           ?updated_at?: ?2019-10-31T13:13:25.985+01:00",
           ?position?: 1,
           ?description?: null,
           ?showcase?: true,
           ?group_id?: null,
           ?page_id?: ?...?,
           ?group?: false,
           ?only_show_if_degraded?: false
       },
       {
           ?id?: ?...?,
           ?name?: ?BankID p� mobil for Telia?,
           ?status?: ?operational?,
           ?created_at?: ?2016-11-08T14:31:12.103+01:00",
           ?updated_at?: ?2019-10-31T01:00:34.215+01:00",
           ?position?: 2,
           ?description?: null,
           ?showcase?: true,
           ?group_id?: ?...?,
           ?page_id?: ?...?,
           ?group?: false,
           ?only_show_if_degraded?: false
       },
       {
           ?id?: ?...?,
           ?name?: ?BankID p� mobil?,
           ?status?: ?operational?,
           ?created_at?: ?2016-11-08T14:30:43.550+01:00?,
           ?updated_at?: ?2018-04-25T01:56:30.786+02:00?,
           ?position?: 3,
           ?description?: null,
           ?showcase?: false,
           ?group_id?: null,
           ?page_id?: ?...?,
           ?group?: true,
           ?only_show_if_degraded?: false,
           ?components?: [
               ?...?,
               ?...?
           ]
       }
   ]
}```
