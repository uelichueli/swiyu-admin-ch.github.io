---
title: Getting started as a Generic Issuer
toc: true
toc_sticky: true
excerpt: Learn how to deploy the generic issuer management service
header:
  teaser: ../assets/images/cookbook_generic_issuer.jpg
---

This software is a web server implementing the technical standards as specified in the [Swiss e-ID & Trust Infrastructure Initial Implementation](https://swiyu-admin-ch.github.io/initial-technology/). Together with the other generic components provided, this software forms a collection of APIs allowing issuance and verification of verifiable credentials without the need of reimplementing the standards.

The Generic Issuer Management Service is the interface to offer a credential. It should be only accessible from the issuers internal organization.

As with all the generic issuance & verification services it is expected that every issuer and verifier hosts their own instance of the service.

The issuer management service is linked to the issuer signer services through a database, allowing to scale the signer service independently from the management service.

![issuer flowchart](../../assets/images/cookbook_generic_issuer_model.png)

# Deployment

> Please make sure that you did the following before starting the deployment:
> - Generated the signing keys file with the didtoolbox.jar
> - Generated a DID which is registered on the identifier registry
> - Registered yourself on the swiyuprobeta portal
> - Registered yourself on the api self service portal

## 1. Set the environment variables

A sample compose file for an entire setup of both components and a database can be found in [sample.compose.yml](https://github.com/swiyu-admin-ch/eidch-issuer-agent-management/blob/main/sample.compose.yml) file.

**Replace all placeholder <VARIABLE_NAME>**.

Please be aware that both the issuer-agent-management and the issuer-agent-oid4vci need to be publicly accessible over a domain configured in `EXTERNAL_URL` so that a wallet can communicate with them.

The latest images are available here:

- [issuer-agent-oid4vci](https://github.com/orgs/swiyu-admin-ch/packages/container/package/eidch-issuer-agent-oid4vci)
- [issuer-agent-management](https://github.com/orgs/swiyu-admin-ch/packages/container/package/eidch-issuer-agent-management)

## 2. Create a verifiable credentials schema

In order to support your use case you need to adapt the so-called issuer_metadata (see [sample.compose.yml](https://github.com/swiyu-admin-ch/eidch-issuer-agent-management/blob/main/sample.compose.yml#L85)).
Those metadata define the appearance of the credential in the wallet and what kind of credential formats are supported.
For further information consult the [VC visual presentation cookbook](https://swiyu-admin-ch.github.io/cookbooks/vc-visual-presentation/)

## 3. Initialize the status list

Once the issuer-agent-management, issuer-agent-oid4vci and postgres instance are up and running you need to initialize the status list of your issuer so that you can issue credentials.

**Request to create an status list slot**  
The url you'll receive in the response will be used in the next request as STATUS_JWT_URL

```bash
curl -X POST https://<SWIYU_STATUS_REGISTRY_API_URL>/api/v1/status/business-entities/<SWIYU_PARTNER_ID>/status-list-entries/ \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SWIYU_STATUS_REGISTRY_ACCESS_TOKEN>" \
  -d '{}'


```

The following request needs to be run on your issuer-agent-management instance.

```bash
curl -X POST https://<EXTERNAL_URL of issuer-agent-management>/status-list \
-H "Content-Type: application/json" \
-d '{
    "uri": "<STATUS_JWT_URL>",
    "type": "TOKEN_STATUS_LIST",
    "maxLength": 800000,
    "config": {
    "bits": 2
    }
  }'

```

## 4. Issue credential

You're now ready to issue credentials by using the issuer-agent-management API which is accessible under
https://<EXTERNAL_URL of issuer-agent-management>**/swagger-ui/index.html#/Credential%20API/createCredential** to create
a credential offer for a holder. Here is an example of a request body for the offer creation

```json
{
  "metadata_credential_supported_id": [
    #Identifier as configured in the credential_configurations_supported section of the issuer_metadata
    "myIssuerMetadataCredentialSupportedId"
  ],
  "credential_subject_data": {
    # Actual content of the credential aka offer data
    "lastName": "Example",
    "firstName": "Edward"
  },
  "offer_validity_seconds": 86400,
  "credential_valid_until": "2010-01-01T19:23:24Z",
  "credential_valid_from": "2010-01-01T18:23:24Z",
  "status_lists": [
    # Url of the status list created in previous step
    "https://example-status-registry-uri/api/v1/statuslist/05d2e09f-21dc-4699-878f-89a8a2222c67.jwt"
  ]
}
```

# Development

Instructions for the development of this component can be found in the [repository](https://github.com/swiyu-admin-ch/eidch-issuer-agent-management).
