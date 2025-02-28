---
title: Getting started with the swiyu Generic Issuer
toc: true
toc_sticky: true
excerpt: Learn how to deploy the swiyu Generic Issuer Management Service
header:
  teaser: ../assets/images/cookbook_generic_issuer.jpg
---

This software is a web server implementing the technical standards as specified in the [Swiss e-ID & Trust Infrastructure Initial Implementation](https://swiyu-admin-ch.github.io/initial-technology/). Together with the other generic components provided, this software forms a collection of APIs allowing issuance and verification of verifiable credentials without the need of reimplementing the standards.

[![ecosystem components](../../assets/images/components.png)](../../assets/images/components.png)

The **swiyu Generic Issuer Management Service** is the interface to trigger a credential offering. It should be only accessible from the issuers internal organization.

The **swiyu Generic Issuer OID4VCI Service** interacts with the wallets directly and must be publicly available.

As with all the generic issuance & verification services it is expected that every issuer and verifier hosts their own instance of the service.

The swiyu Generic Issuer Management Service is linked to the issuer oid4vci services through a database, allowing to scale the oid4vci service independently from the management service.

[![issuer flowchart](../../assets/images/cookbook_generic_issuer_model.png)](../../assets/images/cookbook_generic_issuer_model.png)

# Deployment instructions

> Please make sure that you did the following before starting the deployment:
> - Registered yourself on the swiyu Trust Infrastructure portal
> - Registered yourself on the api self service portal
> - Generated the signing keys file with the didtoolbox.jar
> - Generated a DID which is registered on the identifier registry

## Set the environment variables

A sample compose file for an entire setup of both components and a database can be found in [sample.compose.yml](https://github.com/swiyu-admin-ch/eidch-issuer-agent-management/blob/main/sample.compose.yml) file. You will need to configure a list of environment variables.

**Issuer Agent Management**
| Name | Description | Example |
| --- | --- |---|
|SPRING_APPLICATION_NAME|Name of your application|
|ISSUER_ID|The did you got in the [onboarding](https://swiyu-admin-ch.github.io/cookbooks/onboarding-base-and-trust-registry/#create-a-did-or-create-the-did-log-you-need-to-continue)| did:tdw:QmejrSkusQgeM6FfA23L6NPoLy3N8aaiV6X5Ysvb47WSj8:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:ff8eb859-6996-4e51-a976-be1ca584c124 |
|DID_STATUS_LIST_VERIFICATION_METHOD|DID + Verification Method|did:tdw:QmejrSkusQgeM6FfA23L6NPoLy3N8aaiV6X5Ysvb47WSj8:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:ff8eb859-6996-4e51-a976-be1ca584c124#assert-key-01|
|STATUS_LIST_KEY|EC Private key used to update the status list||
|SWIYU_PARTNER_ID|[swiyu Trust Infrastructure business partner ID](https://swiyu-admin-ch.github.io/cookbooks/onboarding-base-and-trust-registry/#business-partner-registration)|d33fab52-1657-4240-9189-97c33b949739|
|SWIYU_STATUS_REGISTRY_CUSTOMER_KEY|[Status Registry API Key](https://swiyu-admin-ch.github.io/cookbooks/onboarding-base-and-trust-registry/#get-api-keys-to-access-swiyu-apis)||
|SWIYU_STATUS_REGISTRY_CUSTOMER_SECRET|[Status Registry API Secret](https://swiyu-admin-ch.github.io/cookbooks/onboarding-base-and-trust-registry/#get-api-keys-to-access-swiyu-apis)|
|SWIYU_STATUS_REGISTRY_BOOTSTRAP_REFRESH_TOKEN|[Status Registry API Refresh Token](https://swiyu-admin-ch.github.io/cookbooks/onboarding-base-and-trust-registry/#get-api-keys-to-access-swiyu-apis)|
|SWIYU_STATUS_REGISTRY_TOKEN_URL|[OAuth Refresh URL](https://swiyu-admin-ch.github.io/cookbooks/onboarding-base-and-trust-registry/#authenticate-with-oauth2)|https://keymanager-prd.api.admin.ch/keycloak/realms/APIGW|
|SWIYU_STATUS_REGISTRY_API_URL|[Status Registry Base URL](https://swiyu-admin-ch.github.io/cookbooks/onboarding-base-and-trust-registry/#base-urls)|status-reg-api.trust-infra.swiyu-int.admin.ch|

**Issuer Agent OID4VCI**
|Name|Description|Example|
|---|---|---|
|EXTERNAL_URL|Publicly available URL of this Service||
|ISSUER_ID|Issuer DID|did:tdw:QmejrSkusQgeM6FfA23L6NPoLy3N8aaiV6X5Ysvb47WSj8:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:ff8eb859-6996-4e51-a976-be1ca584c124|
|DID_SDJWT_VERIFICATION_METHOD|DID+Verification Method|did:tdw:QmejrSkusQgeM6FfA23L6NPoLy3N8aaiV6X5Ysvb47WSj8:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:ff8eb859-6996-4e51-a976-be1ca584c124#assert-key-02|
|SDJWT_KEY|EC Private key used to sign credentials||


Please be aware that the the issuer-agent-oid4vci needs to be publicly accessible over a domain configured in `EXTERNAL_URL` so that a wallet can communicate with them.

The latest images are available here:

- [issuer-agent-oid4vci](https://github.com/orgs/swiyu-admin-ch/packages/container/package/eidch-issuer-agent-oid4vci)
- [issuer-agent-management](https://github.com/orgs/swiyu-admin-ch/packages/container/package/eidch-issuer-agent-management)

## Create a verifiable credential schema

In order to support your use case you need to adapt the so-called issuer_metadata (see [sample.compose.yml](https://github.com/swiyu-admin-ch/eidch-issuer-agent-management/blob/main/sample.compose.yml#L85)).
Those metadata define the appearance of the credential in the wallet and what kind of credential formats are supported.
For further information consult the [VC visual presentation cookbook](https://swiyu-admin-ch.github.io/cookbooks/vc-visual-presentation/).

## Initialize the status list

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

## Issue credential

You're now ready to issue credentials by using the issuer-agent-management API which is accessible under
https://<"EXTERNAL_URL of issuer-agent-management">/swagger-ui/index.html#/Credential%20API/createCredential to create
a credential offer for a holder. Here is an example of a request body for the offer creation

```bash
curl -X 'POST' \
  'https://{issuer-agent-management-url}/api/v1/credentials' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json' \
  -d '{
  "metadata_credential_supported_id": [
    "university_example_sd_jwt"
  ],
  "credential_subject_data": {
    "degree": "Test",
    "name": "Test", "average_grade": 10
  },
  "offer_validity_seconds": 86400,
  "credential_valid_until": "2010-01-01T19:23:24Z",
  "credential_valid_from": "2010-01-01T18:23:24Z",
  "status_lists": [
    "https://status-list-uri"
  ]
}'
```

## Update status
A credential can have one of the following status: `OFFERED`, `CANCELLED`, `IN_PROGRESS`, `ISSUED`, `SUSPENDED`, `REVOKED`, `EXPIRED`.
Using the Issuer Management service the status can be updated
```bash
curl -X 'PATCH' \
  'https://{issuer-agent-management-url}/api/v1/credentials/{credentialID}/status' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json' \
  -d '{
    "credentialID": $CREDENTIAL_ID
    "credentialStatus": "ISSUED"
  }'
```

# Development instructions

Instructions for the development of the swiyu Generic Issuer can be found in the [GitHub repository](https://github.com/swiyu-admin-ch/eidch-issuer-agent-management).
