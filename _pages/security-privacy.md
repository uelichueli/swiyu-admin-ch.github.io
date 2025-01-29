---
permalink: /security-privacy/
title: Security and Privacy
toc: true
---

## Notes on Privacy
This document defines the technical profile of the Swiss Public Beta Trust Infrastructure. As such it shows how the referenced specifications can, and sometimes have to be, implemented in the context of the Swiss Public Beta Trust Infrastructure.

The aim is to select features, provide guidelines and to define a set of requirements for new or existing specifications to enable interoperability between Issuers, Wallets and Verifiers of Credentials in the Swiss trust infrastructure for its public beta version.

> [!NOTE]
> This profile focuses on the Public Beta release and is not complete. It reflects the current state of implementation and will evolve in the future to fullfil the requirements of the Swiss trust infrastructure.

### Unlinkability
The target audience of the document are early adopters who want to use the Public Beta of the Swiss Trust Infrastructure in 2025 to:
- experience the Confederations infrastructure and assess how it works
- experiment with their use cases 
- test the integration of their products and systems 
- conduct experiences with regard to interoperability across sectors aswell as regarding international interoperability 
- get ready for the productive environment in 2026 

### Unobservability
The following specifications are in scope of this Swiss Profile for the Public Beta Infrastructure:

- Issuer & Verifier identification (DID:TDW, respectively the renamed method DID:WEBVH) 
- Protocol for issuance of the Verifiable Credentials (OID4VCI)
- Protocol for online presentation of Verifiable Credentials (OID4VP)
- Credential Format (SD-JWT VC)
- Credential Status (Token Status List)
- Cryptographic Device Binding
- Trust Protocol
- Crypto Suites

The following assumptions apply:

- Issuers and Verifiers cannot pre-discover the capabilities of Wallets.
- Mechanisms that enables Wallets to discover the capabilities of Issuers and Verifiers.
- Mechanisms that enables Issuers and Verifiers to discover the capabilities of each other exist.

### Countermeasures
The following items are currently out of scope but might be added in future versions:

- Protocol for presentation of Verifiable Credentials for offline use-cases, e.g. using BLE

## Notes on Levels of Assurance
The following table defines which specification must be supported by an actor (Issuer, Verifier, Wallet) in order to comply with this profile.

| Specification     | Issuer   | Wallet   | Verifier |
|-------------------|----------|----------|----------|
| OID4VP            | **MAY**  | **MUST** | **MUST** |
| OID4VCI           | **MUST** | **MUST** | **MAY**  |
| DID:TDW/DID:WEBVH | **MUST** | **MUST** | **MUST** |
| SD-JWT VC profile | **MUST** | **MUST** | **MUST** |
| Status List       | **MUST** | **MUST** | **MUST** |
| Trust Protocol    | **MAY**  | **MUST** | **MUST** |

## Notes on IT Security
Issuers and Verifiers use [DID:TDW](https://identity.foundation/trustdidweb/) version 0.3 as identifiers.

### DID:TDW/DID:WEBVH
Implementations of this profile:

- **MUST** set `portable` to `false` in the first DID log entry.
- **MUST** set `method` to `did:tdw:0.3` in the first DID log entry.
