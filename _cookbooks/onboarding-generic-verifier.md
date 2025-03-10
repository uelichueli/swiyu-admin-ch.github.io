---
title: Getting started with the swiyu Generic Verifier
toc: true
toc_sticky: true
excerpt: Learn how to deploy the swiyu Generic Verifier Management Service
header:
  teaser: ../assets/images/cookbook_generic_verifier.jpg
---

{% capture notice-text %}

The cookbooks are currently only for internal testing and not yet intended for the public. Some links are therefore not yet accessible. We ask for your patience.

{% endcapture %}

<div class="notice--danger">
  <h4 class="no_toc">Only for internal testing!</h4>
  {{ notice-text | markdownify }}
</div>

This software is a web server implementing the technical standards as specified in the [Swiss E-ID & Trust Infrastructure Initial Implementation](https://swiyu-admin-ch.github.io/initial-technology/). Together with the other generic components provided, this software forms a collection of APIs allowing issuance and verification of verifiable credentials without the need of reimplementing the standards.

[![ecosystem components](../../assets/images/components.png)](../../assets/images/components.png)

The **swiyu Generic Verifier Management Service** is the interface to start a verification process. The service itself is and should be only accessible from inside the organization.

The **swiyu Generic Verifier OID4VP Service** interacts with the wallets directly and must be publicly available.

As with all the generic issuance & verification services it is expected that every issuer and verifier hosts their own instance of the service.

The verification management service is linked to the verification validator services through a database, allowing to scale the validator service independently of the management service.

[![verifier flowchart](../../assets/images/cookbook_generic_verifier_model.png)](../../assets/images/cookbook_generic_verifier_model.png)

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

A sample compose file for an entire setup of both components and a database can be found in [sample.compose.yml](https://github.com/swiyu-admin-ch/eidch-verifier-agent-management/blob/main/sample.compose.yml) file. You will need to adapt the
[verifier metadata](https://github.com/swiyu-admin-ch/eidch-verifier-agent-management/blob/main/sample.compose.yml#L35) to your use case.
Those information will be provided to the holder on a dedicated endpoint (`/api/v1/openid-client-metadata.json`) serving as metadata information of your verifier.
The placeholder `${CLIENT_ID}` in your metadata file will be replaced on the fly by the value set for `VERIFIER_DID`.

### Verifier Agent Management

| Name       | Description                | Example |
| ---------- | -------------------------- | ------- |
| OID4VP_URL | URL of your OID4VP service |

### Verifier Agent OID4VP

| Name                    | Description                                                                                                                                                                 | Example                                                                                                                                                     |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| EXTERNAL_URL            | publicly available URL of this service                                                                                                                                      |                                                                                                                                                             |
| VERIFIER_DID            | DID you got during the [onboarding](https://swiyu-admin-ch.github.io/cookbooks/onboarding-base-and-trust-registry/#create-a-did-or-create-the-did-log-you-need-to-continue) | did:tdw:QmejrSkusQgeM6FfA23L6NPoLy3N8aaiV6X5Ysvb47WSj8:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:ff8eb859-6996-4e51-a976-be1ca584c124        |
| DID_VERIFICATION_METHOD | DID+Verification method                                                                                                                                                     | did:tdw:QmejrSkusQgeM6FfA23L6NPoLy3N8aaiV6X5Ysvb47WSj8:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:ff8eb859-6996-4e51-a976-be1ca584c124#key-01 |
| VERIFIER_NAME           | Human readable name of your verifier                                                                                                                                        |
| SIGNING_KEY             | EC private key                                                                                                                                                              |

Please be aware that the verifier-agent-oid4vci need to be publicly accessible over a domain configured in `EXTERNAL_URL` so that a wallet can communicate with them.

At the moment the provided images cannot be used with arm based processors. For futher information, please consult the [Development instructions section](#deployment-instructions).

The latest images are available here:

- [verifier-agent-oid4vp](https://github.com/orgs/swiyu-admin-ch/packages/container/package/eidch-verifier-agent-oid4vp)
- [verifier-agent-management](https://github.com/orgs/swiyu-admin-ch/packages/container/package/eidch-verifier-agent-management)

## Creating a verification

> For a detailed understanding of the verfication process and the data structure of verification please consult the
> [DIF presentation exchange specification](https://identity.foundation/presentation-exchange/#presentation-definition).
> For more information on the general verification flow consult the [OpenID4VP specification](https://openid.net/specs/openid-4-verifiable-presentations-1_0-20.html)

Once the components are deployed you can create your first verification request. For this you first need to define a presentation
definition. Based on that definition you can then create a verification request for a holder as shown in the example below.
In this case we are asking for a credential called "my-test-vc" which should have the attributes
first_name, last_name, and birth_date. The following request can be performed by using the swagger endpoint on $VERIFIER_AGENT_MANAGEMENT_URL**/swagger-ui/index.html**

**Request**

```bash
curl -X POST $VERIFIER_AGENT_MANAGEMENT_URL/api/v1/verifications \
  -H "accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "accepted_issuer_dids": [],
    "jwt_secured_authorization_request": true,
    "presentation_definition": {
        "id": "00000000-0000-0000-0000-000000000000",
        "name": "Test Verification",
        "purpose": "We want to test a new Verifier",
        "input_descriptors": [
            {
                "id": "11111111-1111-1111-1111-111111111111",
                "format": {
                    "vc+sd-jwt": {
                        "sd-jwt_alg_values": [
                            "ES256"
                        ],
                        "kb-jwt_alg_values": [
                            "ES256"
                        ]
                    }
                },
                "constraints": {
                    "fields": [
                        {
                            "path": [
                                "$.vct"
                            ],
                            "filter": {
                                "type": "string",
                                "const": "my-test-vc"
                            }
                        },
                        {
                            "path": [
                                "$.last_name"
                            ]
                        },
                        {
                            "path": [
                                "$.birth_date"
                            ]
                        }
                    ]
                }
            }
        ]
    }
}'
```

**Response**

The response contains a verification_url which points to the verification request just created. This link needs to be provided to the holder in order to submit a response to the verification request. To use the link create a qr code from the verification_url and scan it with the swiyu app.

```json
{
  ...
  "verification_url": "$VERIFIER_AGENT_OID4VCI_URL/request-object/fc884edd-7667-49e3-b961-04a98e7b5600"
}
```

# Development instructions

Instructions for the development of the swiyu Generic Verifier can be found in the [GitHub repository](https://github.com/swiyu-admin-ch/eidch-verifier-agent-management/blob/main/README.md).

## Create Images for ARM based processors

In order to get the sample compose running on an arm based env you first have to check out the [issuer-agent-management](https://github.com/swiyu-admin-ch/eidch-issuer-agent-management) and [issuer-agent-oid4vci](https://github.com/swiyu-admin-ch/eidch-issuer-agent-oid4vci) repositories.

To create an image you to run the following command in both repositories to create local images of the services:

```bash
./mvnw spring-boot:build image
```
