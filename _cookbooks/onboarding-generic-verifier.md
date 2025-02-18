---
title: Getting started as a Generic Verifier
toc: true
toc_sticky: true
excerpt: Learn how to deploy the generic verifier management service
header:
  teaser: ../assets/images/cookbook_generic_verifier.jpg
---

This software is a web server implementing the technical standards as specified in the [Swiss E-ID & Trust Infrastructure Initial Implementation](https://swiyu-admin-ch.github.io/initial-technology/). Together with the other generic components provided, this software forms a collection of APIs allowing issuance and verification of verifiable credentials without the need of reimplementing the standards.

The Generic Verifier Management Service is the interface to start a verification process. The service itself is and should be only accessible from inside the organization.

As with all the generic issuance & verification services it is expected that every issuer and verifier hosts their own instance of the service.

The verification management service is linked to the verification validator services through a database, allowing to scale the validator service independently of the management service.

![verifier flowchart](../../assets/images/cookbook_generic_verifier_model.png)

# Deployment instructions

> Please make sure that you did the following before starting the deployment:
> - Generated the signing keys file with the didtoolbox.jar
> - Generated a DID which is registered on the identifier registry
> - Registered yourself on the swiyuprobeta portal
> - Registered yourself on the api self service portal


## 1. Set the environment variables

A sample compose file for an entire setup of both components and a database can be found in [sample.compose.yml](https://github.com/swiyu-admin-ch/eidch-verifier-agent-management/blob/main/sample.compose.yml) file. **Replace all placeholder <VARIABLE_NAME>**. In addition to that you need to adapt the
[verifier metadata](https://github.com/swiyu-admin-ch/eidch-verifier-agent-management/blob/main/sample.compose.yml#L35) to your use case.
Those information will be provided to the holder on a dedicated endpoint serving as metadata information of your verifier.
The placeholder `${CLIENT_ID}` in your metadata file will be replaced on the fly by the value set for `VERIFIER_DID`.

Please be aware that both the verifier-agent-management and the verifier-agent-oid4vci need to be publicly accessible over a domain configured in `EXTERNAL_URL` so that
a wallet can communicate with them.

The latest images are available here:
- [verifier-agent-oid4vp](https://github.com/orgs/swiyu-admin-ch/packages/container/package/eidch-verifier-agent-oid4vp)
- [verifier-agent-management](https://github.com/orgs/swiyu-admin-ch/packages/container/package/eidch-verifier-agent-management)

## 2. Creating a verification

> For a detailled understanding of the verfication process and the data structure of verification please consult the 
> [DIF presentation exchange specification](https://identity.foundation/presentation-exchange/#presentation-definition).
> For more information on the general verification flow consult the [OpenID4VP specification](https://openid.net/specs/openid-4-verifiable-presentations-1_0-20.html)

Once the components are deployed cou can create your first verification. For this you first need to define a presentation
definition. Based on that definition you can then create a verification request for a holder as shown in the example below. 
In this case we're asking for a credential called "my-custom-vc" which should at least have the attributes 
firstName and lastName. The following request can be performed by using the swagger endpoint on https://<EXTERNAL_URL of verifier-agent-management>**/swagger-ui/index.html**

**Request**
```bash
curl -X 'POST' \
  'https://<EXTERNAL_URL verification agent management>/verifications' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json' \
  -d '{
  "presentation_definition": {
    "id": "00000000-0000-0000-0000-000000000000",
    "name": "Test Verification",
    "purpose": "We want to test a new Verifier",
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
    "input_descriptors": [
      {
        "id": "my-custom-vc",
        "name": "Custom VC",
        "purpose": "DEMO vc",
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
                "$.credentialSubject.firstName",
                "$.credentialSubject.lastName"
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

The response contains a verification_url which points to verification request just created. This link needs to be provided to the holder
in order to submit an response to the verification request.
```json
{
  ...
  "verification_url": "https://<EXTERNAL_URL verifieri agent oid4vp>/request-object/fc884edd-7667-49e3-b961-04a98e7b5600"
}
```
# Development instructions

Instructions for the development of this component can be found in the [Open Source repository](https://github.com/swiyu-admin-ch/eidch-verifier-agent-management/blob/main/README.md).
