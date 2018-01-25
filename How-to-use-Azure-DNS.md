#### Prerequisites
 You need the Azure Cli 2.0 tools to create a service principal for access to your DNS Zone.
Either install Azure Cli 2.0 locally  or use the Azure Cloud Shell

See https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest

#### Log-in to Azure
(not required when using the Azure Cloud Shell)

```
az login 
```
```json
[
  {
    "cloudName": "AzureCloud",
    "id": "12345678-9abc-def0-1234-567890abcdef",
    "isDefault": true,
    "name": "myAzureSubscription",
    "state": "Enabled",
    "tenantId": "11111111-2222-3333-4444-555555555555",
    "user": {
      "name": "someone@example.com",
      "type": "user"
    }
  }
]
```
#### List your DNS Zones

```
az network dns zone list
```

```json
[
  {
    "etag": "00000002-0000-0000-f641-73c64955d301",
    "id": "/subscriptions/12345678-9abc-def0-1234-567890abcdef/resourceGroups/exampledns_rg/providers/Microsoft.Network/dnszones/example.com",
    "location": "global",
    "maxNumberOfRecordSets": 5000,
    "name": "example.com",
    "nameServers": [
      "ns1-02.azure-dns.com.",
      "ns2-02.azure-dns.net.",
      "ns3-02.azure-dns.org.",
      "ns4-02.azure-dns.info."
    ],
    "numberOfRecordSets": 11,
    "resourceGroup": "exampledns_rg",
    "tags": {},
    "type": "Microsoft.Network/dnszones"
  }
]
```
#### Create a service principal
The service principal is used to grant acme.sh access to the DNS Zone using the id value from the previous commands output

(See https://docs.microsoft.com/en-us/cli/azure/ad/sp?view=azure-cli-latest#az_ad_sp_create_for_rbac for more details )  

```
az ad sp create-for-rbac --name  "AcmeDnsValidator" --role "DNS Zone Contributor" --scopes /subscriptions/12345678-9abc-def0-1234-567890abcdef/resourceGroups/exampledns_rg/providers/Microsoft.Network/dnszones/example.com
```

```json
{
  "appId": "3b5033b5-7a66-43a5-b3b9-a36b9e7c25ed",
  "displayName": "AcmeDnsValidator",
  "name": "http://AcmeDnsValidator",
  "password": "1b0224ef-34d4-5af9-110f-77f527d561bd",
  "tenant": "11111111-2222-3333-4444-555555555555"
}
```

##### Note: Dealing with multiple DNS Zones
Because by default acme.sh saves the credentials in ~/.acme.sh/account.conf you have to use the same credentials for all your DNS Zones*.

For example if you are also managing certificates for example.edu you can grant the the service principal acccess to the DNS Zone with:

```
az ad sp create-for-rbac --name  "AcmeDnsValidator" --role "DNS Zone Contributor" --scopes /subscriptions/12345678-9abc-def0-1234-567890abcdef/resourceGroups/exampledns_rg/providers/Microsoft.Network/dnszones/example.com /subscriptions/12345678-9abc-def0-1234-567890abcdef/resourceGroups/deleteme_rg/providers/Microsoft.Network/dnszones/example.edu

```
or grant access to the service principal after you created it
 ```
az role assignment create --assignee 3b5033b5-7a66-43a5-b3b9-a36b9e7c25ed --role "DNS Zone Contributor" --scope /subscriptions/12345678-9abc-def0-1234-567890abcdef/resourceGroups/deleteme_rg/providers/Microsoft.Network/dnszones/example.edu
```


\*If you want to use different credentials instead use the --accountconf to use a different configuration file 

#### You can now use acme.sh 


```
export AZUREDNS_SUBSCRIPTIONID="12345678-9abc-def0-1234-567890abcdef"
export AZUREDNS_TENANTID="11111111-2222-3333-4444-555555555555"
export AZUREDNS_APPID="3b5033b5-7a66-43a5-b3b9-a36b9e7c25ed"          # appid of the service principal
export AZUREDNS_CLIENTSECRET="1b0224ef-34d4-5af9-110f-77f527d561bd"   # password from creating the service principal

acme.sh --issue --dns dns_azure -d example.com -d www.example.com
```
