---
title: Getting started with the swiyu Generic Verifier
toc: true
toc_sticky: true
excerpt: Learn how to deploy the swiyu Generic Verifier Management Service
header:
  teaser: ../assets/images/cookbook_generic_verifier.jpg
---

{% capture notice-text %}

Please be advised that the current system and its operations are provided on a best-effort basis and will continue to evolve over time. The security of the system and its overall maturity remain under development.

{% endcapture %}

<div class="notice--danger">
  <h4 class="no_toc">Public Beta</h4>
  {{ notice-text | markdownify }}
</div>

This software is a web server implementing the technical standards as specified in the ["Swiss Profile"](https://swiyu-admin-ch.github.io/specifications/interoperability-profile/). Together with the other generic components provided, this software forms a collection of APIs allowing issuance and verification of verifiable credentials without the need of reimplementing the standards.

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

A sample compose file for an entire setup of both components and a database can be found in [sample.compose.yml](https://github.com/swiyu-admin-ch/eidch-verifier-agent-management/blob/main/sample.compose.yml) file. You will need to configure a list of environment variables in the `.env` file and adapt the
[verifier metadata](https://github.com/swiyu-admin-ch/eidch-verifier-agent-management/blob/main/sample.compose.yml#L35) to your use case.
The metadata information will be provided to the holder on a dedicated endpoint (`/api/v1/openid-client-metadata.json`) serving as metadata information of your verifier.

### Verifier Agent Management

| Name       | Description                | Example |
| ---------- | -------------------------- | ------- |
| OID4VP_URL | URL of your OID4VP service |

### Verifier Agent OID4VP

| Name                    | Description                                                                                                                                                                                                                  | Example                                                                                                                                                     |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| EXTERNAL_URL            | publicly available URL of this service                                                                                                                                                                                       |                                                                                                                                                             |
| VERIFIER_DID            | DID you created during the [onboarding](https://swiyu-admin-ch.github.io/cookbooks/onboarding-base-and-trust-registry/#create-a-did-or-create-the-did-log-you-need-to-continue)                                              | did:tdw:QmejrSkusQgeM6FfA23L6NPoLy3N8aaiV6X5Ysvb47WSj8:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:ff8eb859-6996-4e51-a976-be1ca584c124        |
| DID_VERIFICATION_METHOD | VERIFIER_DID + Verification, which can be taken from the [onboarding process](https://swiyu-admin-ch.github.io/cookbooks/onboarding-base-and-trust-registry/#create-a-did-or-create-the-did-log-you-need-to-continue) method | did:tdw:QmejrSkusQgeM6FfA23L6NPoLy3N8aaiV6X5Ysvb47WSj8:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:ff8eb859-6996-4e51-a976-be1ca584c124#key-01 |
| VERIFIER_NAME           | Human readable name of your verifier, which will be displayed to the user in the swiyu wallet                                                                                                                                |
| SIGNING_KEY             | EC Private key, which can be taken from [onboarding process](https://swiyu-admin-ch.github.io/cookbooks/onboarding-base-and-trust-registry/#create-a-did-or-create-the-did-log-you-need-to-continue)                         |

Please be aware that the verifier-agent-oid4vci need to be accessible (configured in EXTERNAL_URL) so that a wallet can communicate with it.

The provided images can be used with arm based processors, but they are not optimized. For further information, please consult the [Development instructions section](#development-instructions).

The latest images are available here:

- [verifier-agent-oid4vp](https://github.com/orgs/swiyu-admin-ch/packages/container/package/eidch-verifier-agent-oid4vp)
- [verifier-agent-management](https://github.com/orgs/swiyu-admin-ch/packages/container/package/eidch-verifier-agent-management)

## Creating a verification

> For a detailed understanding of the verfication process and the data structure of verification please consult the
> [DIF presentation exchange specification](https://identity.foundation/presentation-exchange/#presentation-definition).
> For more information on the general verification flow consult the [OpenID4VP specification](https://openid.net/specs/openid-4-verifiable-presentations-1_0-20.html)

Once the components are deployed you can create your first verification request. For this you first need to define a presentation
definition. Based on that definition you can then create a verification request for a holder as shown in the example below.
In this case, we are asking for a credential called "my-test-vc" which should have the attributes
firstName, lastName and birthDate. The following request can be performed by using the swagger endpoint on $VERIFIER_AGENT_MANAGEMENT_URL**/swagger-ui/index.html**.

<div class="notice--warning">
  Please replace or set values, like presentation.id, input_descriptors[i].id and optionally accepted_issuer_dids, to get a valid request.
</div>

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
                                "$.lastName"
                            ]
                        },
                        {
                            "path": [
                                "$.birthDate"
                            ]
                        }
                    ]
                }
            }
        ]
    }
}'
```

<div class="notice--warning">
  ⚙️ Please store the id of the response as the value is required in the "Get the verification result" call referenced as "$VERIFICATION_ID".
</div>

**Response**

The response contains a verification_url which points to the verification request just created. This link needs to be provided to the swiyu wallet in order to submit a response to the verifier-agent-oid4vp. To use the link, create a qr code from the verification_url and scan it with the swiyu app.

```json
{
  ...
  "verification_url": "$VERIFIER_AGENT_OID4VCI_URL/request-object/fc884edd-7667-49e3-b961-04a98e7b5600"
}
```

## Get the verification result

**Request**

```bash
curl $VERIFIER_AGENT_MANAGEMENT_URL/api/v1/verifications/$VERIFICATION_ID
```

# Development instructions

Instructions for the development of the swiyu Generic Verifier can be found in the [GitHub repository](https://github.com/swiyu-admin-ch/eidch-verifier-agent-management/blob/main/README.md).

## Create Images for ARM based processors

In order to optimize the image for arm based systems, you first have to check out the [verifier-agent-management](https://github.com/swiyu-admin-ch/eidch-verifier-agent-management) and [verifier-agent-oid4vp](https://github.com/swiyu-admin-ch/eidch-verifier-agent-oid4vp) repositories.

To create an image you to run the following command in both repositories to create local images of the services:

```bash
./mvnw spring-boot:build-image
```

# Your Feedback?

We would be pleased if you spend about 3 additional minutes and give us feedback on the swiyu Public Beta Trust Infrastructure and your onboarding process! With Public Beta, we want to give ecosystem stakeholders the opportunity to gain initial experience and build their own use cases on the trust infrastructure of the future e-ID. Your [feedback](https://findmind.ch/c/feedback_publicbeta_infr_en) will help us to further develop and improve the touchpoints, and we greatly appreciate your support.

