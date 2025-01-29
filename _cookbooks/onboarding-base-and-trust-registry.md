---
title: Onboarding the Base & Trust Registry
toc: true
toc_sticky: true
excerpt: Learn how to onboard the swiyu trust infrastructure and manage your organisation
---

## Sign-in or up to ePortal

Login or sign up into ePortal via [AGOV](https://www.me.agov.admin.ch/registration?agovAq=100&source=idp) or CH-Login accounts in order to register to the Trust and Base Registries

 [All services · ePortal](https://eportal.admin.ch/start)

## Open *swiyu* Trust Infrastructure

Search for the _swiyu Trust Infrastructure_ service and enter it.



{% include figure popup=false image_path="../assets/images/welcome_to_eportal.png" alt="welcome to eportal" caption="This is a figure caption." %}



# Onboard the Base Registry

## Business Partner registration


⚙️ The businesspartner id created here will be referenced as SWIYU\_PARTNER\_ID

To join an existing business partner see: [Join an existing organsiation](#UserguideBase/TrustRegistry-join_organisation)

Register the business partner1 by providing a name2 and primary contact email.
1) Under "business partner" is understood any type of company, private or public institution, but also individuals (natural persons) can register themselves as an business partner on the _**swiyu** Trust Infrastructure_ and use it.
2) This name can not be changed. To appear with another name you will have to register a new business partner.

![base registry enrollment](../../assets/images/base_registry_enrollment.png)

## Get API keys to access swiyu APIs
--------------------------------------

Go to the API [self service portal](http://selfservice.api.admin.ch/api-selfservice) to register for the ecosystem APIs.

If you are registered with multiple business partners you can click the business partner ID on the top right in order to select with which one you want to subscribe. 
![api selfservice list of apis](../../assets/images/api_selfservice.png)

Subscribe with your business partner to both _swiyu Core Business Service_ APIs (status & identifier)

Select an API and press **Subscribe.** You will be prompted to create a new Application or select an existing one.
![create or select an application](../../assets/images/create_select_application.png)
![create application](../../assets/images/create_application.png)

**Important:**

<p> ⚙️ The output of the application creation will be refrenced as SWIYU\_STATUS\_REGISTRY\_CUSTOMER\_KEY / SWIYU\_STATUS\_REGISTRY\_CUSTOMER\_SECRET / SWIYU\_STATUS\_REGISTRY\_BOOTSTRAP\_REFRESH\_TOKEN / SWIYU\_STATUS\_REGISTRY\_ACCESS\_TOKEN </p>

Safely store your keys this is the only time they are shown to you. It is possible to create new ones if necessary.  
If you don't refresh your token for too long it might expire and you will need to create new tokens here.

### Authenticate with OAuth2

Use the Access token as Bearer token ([RFC 6750](https://datatracker.ietf.org/doc/html/rfc6750)) when connecting to the subscribed Authoring API.

If you want to create a new Access token without manual UI interaction you can use the OAuth refresh token flow for the issuer [https://keymanager-prd.api.admin.ch/keycloak/realms/APIGW](https://keymanager-prd.api.admin.ch/keycloak/realms/APIGW) to get valid access tokens for API access.

### Base URLs

⚙️ The status authoring url will be referenced as SWIYU\_STATUS\_REGISTRY\_API\_URL, the key manager as KEY\_MANAGER 
Use the [Swagger Editor](https://editor.swagger.io/) for convenience.

| Environment | Identifier Authoring | Status Authoring | Key manager |
| --- | --- | --- | --- |
| Ref | [identifier-reg-api-r.trust-infra.swiyu.admin.ch](http://identifier-reg-api-r.trust-infra.swiyu.admin.ch/) | [status-reg-api-r.trust-infra.swiyu.admin.ch](http://status-reg-api-r.trust-infra.swiyu.admin.ch/) | [keymanager-nprd.api.admin.ch](https://keymanager-nprd-intra.api.admin.ch) |
| Int-Prod | [identifier-reg-api.trust-infra.swiyu-int.admin.ch](https://identifier-reg-api.trust-infra.swiyu-int.admin.ch/) | [status-reg-api.trust-infra.swiyu-int.admin.ch](https://status-reg-api.trust-infra.swiyu-int.admin.ch/) | [keymanager-prd.api.admin.ch](https://keymanager-prd-intra.api.admin.ch) |
| Prod | [identifier-reg-api.trust-infra.swiyu.admin.ch](https://identifier-reg-api.trust-infra.swiyu.admin.ch/) | [status-reg-api.trust-infra.swiyu.admin.ch](https://status-reg-api.trust-infra.swiyu.admin.ch/) | [keymanager-prd.api.admin.ch](https://keymanager-prd-intra.api.admin.ch) |
| Swagger | [OpenAPI spec](api/identifier_authoring.yml) | [OpenAPI spec](api/status_authoring.yml)

In the next step you will need your business partner ID. You can find it in the **swiyu Trust Infrastructure** dashboard.
![swiyu dashboard](../../assets/images/swiyu_dashboard.png)

## Onboard business partner on the Base Registry

### Create DID space

In order to onboard on the Base Registry you will first need to reserve some space.

_POST: \[IDENTIFIER-AUTHORING\]/api/v1/identifier/business-entities/{businessEntityId}/identifier-entries_

**API Response 201**
```yaml
<pre>{<br/>&emsp;"id": "18fa7c77-9dd1-4e20-a147-fb1bec146085",<br />&emsp;"identifier_registry_url": "https://identifier-reg.trust-infra.swiyu-int.admin.ch/api/v1/did/18fa7c77-9dd1-4e20-a147-fb1bec146085/did.jsonl"<br>}</pre>
```

The identifier\_registry\_url is used in the next step when creating the did log.

The id is required when uploading your did log.

### create a DID (or create the DID log you need to continue)

A Decentralized Identifier (DID) is a globally unique identifier that allows individuals and entities to create and manage their own digital identities independently of centralized authorities. To actively participate in the swiyu ecosystem as an Issuer or Verifier, you must create at least one DID and upload the resulting DID log content to the base registry. New DIDs can be created using the DID-Toolbox, since it involves a set of steps that are error prone or need some time to get familiar with and one might end up with invalid DIDs.

We recommend creating separate DIDs for each role (e.g., separate DIDs for issuers and verifiers).

**Currently, the swiyu ecosystem supports the following DID method: did:tdw, version 0.3.**

#### Prerequisites (using the JAR file)

Before using the DID-Toolbox, ensure your system meets the following requirements:

*   Java Runtime Environment (JRE) 21 or Higher: The DID-Toolbox requires Java JRE version 21 or above. Verify that Java is installed on your machine.
*   Internet Connection: Required for downloading the tool.
*   Operating System: Compatible with major operating systems, including Windows, macOS, and Linux. Ensure your OS is up to date to avoid compatibility issues.
*   Sufficient Disk Space: Allocate enough disk space for the tool and the generated key materials. 100 MB should suffice, depending on the number of DIDs you intend to generate.

#### Downloading the DID-Toolbox

The current release can be downloaded from [Releases · e-id-admin/didtoolbox-java](https://github.com/e-id-admin/didtoolbox-java/releases)

#### Quickstart – Create Your First DID

The Quickstart option is designed for users who want to rapidly set up one or mutliple DIDs without getting too much into the DID method internals. This automates the generation of necessary asymmetric key pairs and generates the initial DID log content, which must be uploaded to the Base Registry later in the process (see Upload DID log).

###### Command Syntax

⚙️ The generated pem .didtoolbox/assert-key-01 fille will be referenced as "assert-key-01"

To run the DID-Toolbox using the Quickstart option, use the following command structure:

**Command Samples**

```
# Parameter
java -jar didtoolbox.jar create --identifier-registry-url <identifier_registry_url>
 
# Example
java -jar didtoolbox.jar create --identifier-registry-url https://identifier-reg.trust-infra.swiyu-int.admin.ch/api/v1/did18fa7c77-9dd1-4e20-a147-fb1bec146085/did.jsonl
```

*   create: Command to create a new DID
*   <identifier\_registry\_url>: URL received as a result of DID space creation from step "Create DID space"

For advanced usage or detailed parameter descriptions, please refer to [e-id-admin/didtoolbox-java](https://github.com/e-id-admin/didtoolbox-java).

###### What Happens Upon Execution

*   Key Pair Generation: Three key pairs are created and stored in the .didtoolbox directory (output directory, will be created automatically) in PEM format  
    **Take good care of the generated key material. You will need it again later on (e.g. to configure it in your Issuers and/or Verifiers, see:** [Issuer-Management](https://github.com/admin-ch-ssi/mirror-issuer-agent-management) & [Verifier-Management](https://github.com/admin-ch-ssi/mirror-verifier-agent-management)
    *   DID Update Key Pair:
        *   id\_ed25519: Private key (not password protected)
        *   id\_ed25519.pem: Public key
    *   DID Authentication Key Pair:
        *   auth-key-01: Private key (not password protected)
        *   auth-key-01.pem: Public key
    *   DID Assertion Key Pair:
        *   assert-key-01: Private key (not password protected)
        *   assert-key-01.pem: Public key
*   DID Log Generation: A DID log line is generated and output to the standard console (stdout). You can redirect this output to a file if necessary. **This is the output you need to continue with the step "Upload DID log".**

###### DID Log Content

⚙️ The did generated in this step will be referenced as ISSUER\_ID or VERIFIER\_DID

The generated DID log content should look similiar as shown below. After creation, it consists of a single, albeit lengthy, line.

**DID log Sample**
```yaml
["1-Qmdc45SbY6miLmcw2EyAysLy2A99TeiQqVXkkyh6qzsLTm","2025-01-07T09:06:06Z",{"method":"did:tdw:0.3","scid":"QmU49w8drdPUk4g8NXsLqVRqLRz588N99tBSRRBLoxXHow","updateKeys":["z6Mkn9mdkU9YnexYS2fqMRkTrpJMBNx344KNb4cAgWFFVWQE"],"prerotation":false,"portable":false},{"value":{"@context":["https://www.w3.org/ns/did/v1","https://w3id.org/security/multikey/v1"],"id":"did:tdw:QmU49w8drdPUk4g8NXsLqVRqLRz588N99tBSRRBLoxXHow:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:18fa7c77-9dd1-4e20-a147-fb1bec146085","authentication":["did:tdw:QmU49w8drdPUk4g8NXsLqVRqLRz588N99tBSRRBLoxXHow:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:18fa7c77-9dd1-4e20-a147-fb1bec146085#auth-key-01"],"assertionMethod":["did:tdw:QmU49w8drdPUk4g8NXsLqVRqLRz588N99tBSRRBLoxXHow:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:18fa7c77-9dd1-4e20-a147-fb1bec146085#assert-key-01"],"verificationMethod":[{"id":"did:tdw:QmU49w8drdPUk4g8NXsLqVRqLRz588N99tBSRRBLoxXHow:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:18fa7c77-9dd1-4e20-a147-fb1bec146085#auth-key-01","controller":"did:tdw:QmU49w8drdPUk4g8NXsLqVRqLRz588N99tBSRRBLoxXHow:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:18fa7c77-9dd1-4e20-a147-fb1bec146085","type":"JsonWebKey2020","publicKeyJwk":{"kty":"OKP","crv":"Ed25519","kid":"auth-key-01","x":"CyWSTgeCUzaD4lUWT07vMg-GsTWNOwnEFF7Rfu7OrWU"}},{"id":"did:tdw:QmU49w8drdPUk4g8NXsLqVRqLRz588N99tBSRRBLoxXHow:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:18fa7c77-9dd1-4e20-a147-fb1bec146085#assert-key-01","controller":"did:tdw:QmU49w8drdPUk4g8NXsLqVRqLRz588N99tBSRRBLoxXHow:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:18fa7c77-9dd1-4e20-a147-fb1bec146085","type":"JsonWebKey2020","publicKeyJwk":{"kty":"OKP","crv":"Ed25519","kid":"assert-key-01","x":"GTMNlEdWeP-AB40XXG19R57_TUOsgWY4kypRG4ZrQWQ"}}]}},{"type":"DataIntegrityProof","cryptosuite":"eddsa-jcs-2022","created":"2025-01-07T09:06:06Z","verificationMethod":"did:key:z6Mkn9mdkU9YnexYS2fqMRkTrpJMBNx344KNb4cAgWFFVWQE#z6Mkn9mdkU9YnexYS2fqMRkTrpJMBNx344KNb4cAgWFFVWQE","proofPurpose":"authentication","challenge":"1-Qmdc45SbY6miLmcw2EyAysLy2A99TeiQqVXkkyh6qzsLTm","proofValue":"z4GG3MaCgwTWH5hEi7C1DyAJzr3VFbfmT9s1PN5Pr4BxgvYSbYsgn5kYAgwxFwXrGC8Wdm45HScq72xkujvPcFhm9"}]
```

In the example above the DID is the following

**DID sample**

```yaml
did:tdw:QmU49w8drdPUk4g8NXsLqVRqLRz588N99tBSRRBLoxXHow:identifier-reg.trust-infra.swiyu-int.admin.ch:api:v1:did:18fa7c77-9dd1-4e20-a147-fb1bec146085
```



#### Additional Information

*   Output Directory: The .didtoolbox directory is automatically created in the current working directory. Ensure you have the necessary permissions to create and write to this directory.
*   Multiple DIDs: If you create multiple DIDs, please make sure to rename the .didtoolbox directory (or move/rename the files) after each creation run, since the key material will re-generated on each run and therefore overwritten.
*   Security: Keep your private keys secure. Do not share them or expose them in unsecured environments.
*   Using Existing DIDs: While the Quickstart option generates new DIDs and key material, future versions of the DID-Toolbox may support importing and managing existing DIDs. 

### Upload DID log

Use the Identifier API to upload your DIDLog.

_PUT: \[IDENTIFIER-AUTHORING\]/api/v1/identifier/business-entity/{businessEntityId}/identifier-entries/{identifierRegistryEntryId}_

Add the did:tdw log you created earlier as string body (not JSON).

Make sure the content-type is set to "application/jsonl+json"

Now you are registered on the base registry and be able to configure your issuer component. 

# Use / Integrate the Trust Infrastructure

To be able to interact with the swiyu eco system you need to host either a an swiyu issuer and/or an swiyu verifier. Please 

1.  Integrate the swiyu issuer and swiyu verifier beta according to the documentation 
2.  Create a verifiable credential schema with the help of the Design Guidelines
3.  Create verifications schemas (link to document on github), for example building upon the Beta-ID (demo credential with dummy data)
4.  Issue and verify demo verifiable credentials on your own systems
5.  Manage the data of your entity


# User management

## Invite members to your business partner


Go to e-portal and click on _manage users:_
![invite members](../../assets/images/invite_members.png)

Generate as many invitation codes as you need and make sure to add the appropriate roles.
![generate invitation codes](../../assets/images/create_codes.png)

## Join an existing business partner

If your business partner is already registered on ePortal

[T](https://eportal.admin.ch/start)o Join an already existing business parter press the _Redeem invitation code_ button on the top right.
![redeem invitation code](../../assets/images/redeem_code.png)
![redeem invitation code - step 2](../../assets/images/redeem_code_2.png)
