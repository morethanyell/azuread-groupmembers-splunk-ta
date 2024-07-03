# Azure Group Member Insight Splunk TA

This Splunk TA uses the `GET /groups/?$search="displayName:{keyword}"` parameter and loops through all the groups to get their members. The looping API call uses `GET /groups/{group_id}/members?$select=id,userPrincipalName,displayName,jobTitle,accountEnabled`. That means each event ingested into Splunk look something like:

```
{
   @odata.type: #microsoft.graph.user
   accountEnabled: true
   displayName: deGrasse Tyson, Neil 
   id: 81201000-501ce31b9d-6e2f738-36e8acb-a3eb1755
   jobTitle: Astrophysicist
   memberOfGroupDisplayName: Harvard-University-Science-Communicators
   memberOfGroupId: f79d732c-de0d87fa0d-5a6ec03-f0a1ba-a13092e43
   userPrincipalName: neil.degrasse.tyson@scienceyoutubers.com
}
```

Every event does not have time and uses the CURRENT_TIME or the local time of the Splunk server that's processing this collection.

## Why You Would Want to Use This
While the [Splunk Add-on for Microsoft Azure](https://splunkbase.splunk.com/app/3757) can collect groups and expand collections by members using Optional Parameters, it does not support using Microsoft Graph API's Advanced Query capabilities due to the limitations of `$search` and `$expand` being used together (see this [documentation](https://learn.microsoft.com/en-us/graph/aad-advanced-queries?tabs=http#query-scenarios-that-require-advanced-query-capabilities)).

Given this limitation, your organization may have use cases where you need to collect members of specific groups that match a particular nomenclature. For instance, your organization might need to list all users who are members of groups matching the name "MFA Bypass". 

Using the Splunk TA for Azure, it can be very resource-intensive (ingest, storage, CPU, and memory) to log everything and filter at the SPL level. This TA addresses that by allowing you to collect only the necessary group members based on specified display name patterns, making data collection more efficient and targeted.

## Usage
- Have a Client ID, Client Secret provisioned for you. This Client/App ID must have the same permissions as the prescribed by Splunk TA for Azure or Microsoft Cloud Services
- Install this TA on your Splunk Heavy Forwarder or SplunkCloud Victoria Ad Hoc (collector) node
- Under Configuration, enter the Client ID under username and Client Secret in the password
- Under Input, 
   - Give it a unique name that describe your use case
   - For interval, I recommend doing this very infrequently, like "once per day" or 86400
   - Specify your index
   - Under `Group Display Name Keyword`, give it the search keyword that will match Groups' `displayName`
      - For example, if you have heaps of groups that are named "*SNOW USERS*", such as "LATAM-SNOW-USERS", "EMEA-SNOW-USERS", "APAC-SNOW-USERS", enter in the textfield the value _SNOW USERS_
   - Enter the Tenant ID

## Support
If you want to support me, my paypal is daniel.l.astillero@gmail.com