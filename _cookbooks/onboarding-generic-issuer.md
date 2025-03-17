---
title: Getting started with the swiyu Generic Issuer
toc: true
toc_sticky: true
excerpt: Learn how to deploy the swiyu Generic Issuer Management Service
header:
  teaser: ../assets/images/cookbook_generic_issuer.jpg
---

{% capture notice-text %}

The cookbooks are currently only for internal testing and not yet intended for the public. Some links are therefore not yet accessible. We ask for your patience.

Additionly the cookbook describes the minimal set-up to create a verifiable credential in the swiyu-wallet.

{% endcapture %}

<div class="notice--danger">
  <h4 class="no_toc">Only for internal testing!</h4>
  {{ notice-text | markdownify }}
</div>

This software is a web server implementing the technical standards as specified in the [Swiss e-ID & Trust Infrastructure Initial Implementation](https://swiyu-admin-ch.github.io/initial-technology/). Together with the other generic components provided, this software forms a collection of APIs allowing issuance and verification of verifiable credentials without the need of reimplementing the standards.

[![ecosystem components](../../assets/images/components.png)](../../assets/images/components.png)

The **swiyu Generic Issuer Management Service** is the interface to trigger a credential offering. It should be only accessible from the issuers internal organization.

The **swiyu Generic Issuer OID4VCI Service** interacts with the wallets directly and must be publicly available.

As with all the generic issuance & verification services it is expected that every issuer and verifier hosts their own instance of the service.

The swiyu Generic Issuer Management Service is linked to the issuer oid4vci services through a database, allowing to scale the oid4vci service independently from the management service.

[![issuer flowchart](../../assets/images/cookbook_generic_issuer_model.png)](../../assets/images/cookbook_generic_issuer_model.png)

# Deployment instructions

> Please make sure that you did the following before starting the deployment:
>
> - Registered yourself on the swiyu Trust Infrastructure portal
> - Registered yourself on the api self service portal
> - Generated the signing keys file with the didtoolbox.jar
> - Generated a DID which is registered on the identifier registry
>
> The required steps are explained in the [Base- and Trust Registry Cookbook](https://swiyu-admin-ch.github.io/cookbooks/onboarding-base-and-trust-registry/)

## Set the environment variables

A sample compose file for an entire setup of both components and a database can be found in [sample.compose.yml](https://github.com/swiyu-admin-ch/eidch-issuer-agent-management/blob/main/sample.compose.yml) file. You will need to configure a list of environment variables.

### Issuer Agent Management

|---
| Name | Description | Example
| --- | --- |---
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

### Issuer Agent OID4VCI

|Name|Description|Example|
|---|---|---|
|EXTERNAL_URL|Publicly available URL of this Service||
|ISSUER_ID|Issuer DID|did:tdw:QmejrSkusQgeM6FfA23L6NPoLy3N8aaiV6X5Ysvb47WSj8:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:ff8eb859-6996-4e51-a976-be1ca584c124|
|DID_SDJWT_VERIFICATION_METHOD|DID+Verification Method|did:tdw:QmejrSkusQgeM6FfA23L6NPoLy3N8aaiV6X5Ysvb47WSj8:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:ff8eb859-6996-4e51-a976-be1ca584c124#assert-key-02|
|SDJWT_KEY|EC Private key used to sign credentials||

Please be aware that the the issuer-agent-oid4vci needs to be publicly accessible over a domain configured in `EXTERNAL_URL` so that a wallet can communicate with them.

At the moment the provided images cannot be used with arm based processors. For futher information, please consult the [Development instructions section](#deployment-instructions).

The latest images are available here:

- [issuer-agent-oid4vci](https://github.com/orgs/swiyu-admin-ch/packages/container/package/eidch-issuer-agent-oid4vci)
- [issuer-agent-management](https://github.com/orgs/swiyu-admin-ch/packages/container/package/eidch-issuer-agent-management)

## Create a verifiable credential schema

In order to support your use case you need to adapt the so-called issuer_metadata (see [sample.compose.yml](https://github.com/swiyu-admin-ch/eidch-issuer-agent-management/blob/main/sample.compose.yml#L85)).
Those metadata define the appearance of the credential in the wallet and what kind of credential formats are supported.
For further information consult the [VC visual presentation cookbook](https://swiyu-admin-ch.github.io/cookbooks/vc-visual-presentation/).

## Initialize the status list

Once the issuer-agent-management, issuer-agent-oid4vci and postgres instance are up and running you need to initialize the status list of your issuer so that you can issue credentials.

**Request to create and initialize an status list slot**

The following request needs to be run on your issuer-agent-management instance.
> **&#9432;** The maximum file size of the status list is currently 250kB. (Subject to evaluation and might change after public beta).

```bash
curl -X POST $ISSUER_AGENT_MANAGEMENT_URL/api/v1/status-list \
  -H "accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
  "type": "TOKEN_STATUS_LIST",
  "maxLength": 125000,
  "config": {
    "bits": 2
    }
}'
```

This results in a response like: Please store the `statusRegistryUrl` as it is required in the [Issue Credential call](#issue-credential).

```json
{
  "id": "EXAMPLE_ENTRY_UUID",
  "statusRegistryUrl": "https://status-reg.trust-infra.swiyu-int.admin.ch/api/v1/statuslist/EXAMPLE_STATUS_LIST_ID.jwt",
  "type": "TOKEN_STATUS_LIST",
  "maxListEntries": 125000,
  "remainingListEntries": 125000,
  "nextFreeIndex": 0,
  "version": "1.0",
  "config": {
    "bits": 2
  }
}
```

## Issue credential

You're now ready to issue credentials by using the issuer-agent-management API to create a credential offer for a holder. Here is an example of a request body for the offer creation:

```bash
curl -X POST $ISSUER_AGENT_MANAGEMENT_URL/api/v1/credentials \
  -H "accept: */*" \
  -H "Content-Type: application/json" \
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

To check the result create a qr code from the resulting offer_deeplink, which then can be scanned with the swiyu wallet.

## Update status

A credential can have one of the following status: `OFFERED`, `CANCELLED`, `IN_PROGRESS`, `ISSUED`, `SUSPENDED`, `REVOKED`, `EXPIRED`.
Using the Issuer Management service the status can be updated

```bash
curl -X PATCH $ISSUER_AGENT_MANAGEMENT_URL/api/v1/credentials/{credentialID}/status \
  -H "accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "credentialID": $CREDENTIAL_ID
    "credentialStatus": "ISSUED"
  }'
```

# Development instructions

Instructions for the development of the swiyu Generic Issuer can be found in the [GitHub repository](https://github.com/swiyu-admin-ch/eidch-issuer-agent-management).

## Create Images for ARM based processors

In order to get the sample compose running on an arm based env you first have to check out the [issuer-agent-management](https://github.com/swiyu-admin-ch/eidch-issuer-agent-management) and [issuer-agent-oid4vci](https://github.com/swiyu-admin-ch/eidch-issuer-agent-oid4vci) repositories.

To create an image you to run the following command in both repositories to create local images of the services:

```bash
./mvnw spring-boot:build image
```
