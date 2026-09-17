# Azure Lighthouse onboarding template

`ClientOnboarding.json` grants Divine Technology Solutions delegated access to a customer's Azure
subscription via Azure Lighthouse.

**The customer deploys this, not DTS.** Azure rejects a tenant granting itself access, so there is
no DTS-side path around it.

## Deploying it

The canonical copy is served at **<https://bedrock.dtschurch.com/Onboarding.json>**, and the
one-click link is that URL on the portal's template route:

```
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fbedrock.dtschurch.com%2FOnboarding.json
```

Run it in your own tenant, at **subscription scope**, as a user holding **Owner** who is a member
(not a guest) of that tenant. You will not be asked for a resource group — this is a
subscription-scope template.

Or with the CLI:

```bash
az deployment sub create \
  --name dts-lighthouse-onboarding \
  --location eastus \
  --template-file ClientOnboarding.json \
  --parameters @ClientOnboarding.parameters.json
```

## What it grants

| Principal | Role |
|---|---|
| DTS Team | **Contributor** |
| DTS Team | **User Access Administrator**, limited to assigning the roles below to managed identities |

Assignable to managed identities in your tenant: Contributor, Reader, Monitoring Contributor,
Storage Blob Data Contributor, Key Vault Secrets User, Storage Account Contributor.

It **cannot** grant Owner, custom roles, or any role carrying `DataActions` — Azure Lighthouse does
not support delegating those. DTS cannot read Key Vault secret values or blob contents through this
delegation.

You can revoke all of it yourself, at any time, from **Service providers → Service provider offers**
in the Azure portal. No call to DTS required.

## Changing it

Don't edit this copy. It is generated from DTS's internal source of truth and will be overwritten.

`mspOfferName` derives the registration GUID, so **changing the name creates a second delegation
rather than updating the first**. When redeploying over an existing delegation, pass the offer name
already in place.
