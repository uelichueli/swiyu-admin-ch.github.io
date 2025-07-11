---
title: "Roadmap for the swiyu Public Beta Trust Infrastructure"
permalink: /roadmap/
toc: true
toc_sticky: true
---

## Current stage and Gaps of Public Beta

The Public Beta environment aims to function as a basis for experimentation and integration efforts conducted by the ecosystem participants from both the public and the private sector. It provides the same technology that will be used by the productive environment in 2026, but with, as of now, reduced maturity and scope. At the moment operation and support of the Public Beta Trust Infrastructure are run on a best effort basis. It is planned to gruadually develop public beta into an integration environment that is run in parallel and equivalent to the productive infrastructure from Go-live in 2026 onwards.  

As regards the e-ID, the Public Beta Trust Infrastructure will mimic this credential type through a Beta-ID. Beta-IDs contain the same set of attributes as the e-ID defined in Art. 15 of the BGEID (Bundesgesetz über den elektronischen Identitätsnachweis und andere elektronische Nachweise). However, Beta-ID's technical features are restricted and the legal requirements defined in Section 3 of the BGEID do not apply. 

The following briefly outlines the current discrepancies between Public Beta and the targeted productive Trust Infrastructure of 2026 for each component. 

### Swiyu Base Registry 

Missing features required by the BGEID that will be addressed until the launch of the e-ID are: 

- The base registry cannot be audited yet. 

Other security and privacy related aspects that will be addressed include: 

- The read APIs of the base registry do not support caching yet.
- The request and verification of a proof-of-possession before data (e.g., DIDs or a status list) can be updated/deleted from the base registry has not been implemented yet. 

### Swiyu Trust Registry 

Missing features required by the BGEID that will be addressed until the launch of the e-ID are: 

- The tooling and process for verifying trusted identity information are not yet implemented at the FOJ.
- The automated verification of identifier proof-of-posessions as part of the trust registry onboarding is not yet implemented.
- The capabilities for anchoring VC schema and respective issuer/verifier trust statements are not yet provided (Note: These will only be available for actors fulfilling regulated functions).
- The base registry cannot be audited yet.
- The list and process to register non-compliant actors are not available yet.
- There is no support of multiple DIDs in a single trust statement (if this feature will be provided in the future is not yet decided). 

Other security and privacy related aspects that will be addressed include: 

- The read APIs of the trust registry do not support caching yet.
- The capability to present trust statements directly during presentation (client-to-client)  by issuers and verifiers (to avoid unnecessary calls to the trust registry) needs to be evaluated and is not yet implemented.
 

### Issuing (Generic Issuer & BCS) 

Missing features required by the BGEID that will be addressed until the launch of the e-ID are: 

- Key attestations to ensure hardware binding are not yet implemented or validated by the issuing components.



Other security and privacy related aspects that will be addressed include: 

- Payload encryption during issuance is not yet implemented.
- The features for software-based device binding are not yet implemented.
- The capability for direct (client-to-client) presentation of issuer trust statements is not yet implemented.
- A dedicated extension for managing keys through a HSM is not yet available. 
 

Other aspects to improve the user experience that will be addressed include: 

- The [OCA specification](https://swiyu-admin-ch.github.io/specifications/oca/) for improved credential processing and visualization is not yet implemented 

### Swiyu Wallet 

Missing features required by the BGEID that will be addressed until the launch of the e-ID are: 

- There is no backup (import/export) function available yet. (Note: this feature will only be available for credentials that are not hardware-bound).
- Non-compliant actors cannot be reported yet. 

Other security and privacy related aspects that will be addressed include: 

- Trust statements presented by the issuer/verifier cannot be processed.
- The capability for verifying the check-app attestation is not yet provided.
- The features for software-based device binding are not yet implemented.
- Payload encryption during issuance and verification flows is not yet implemented.
- There is no capability for holders to view their activities in an activity/transaction log as well as to give or deny their consent for such logging. 

Other aspects to improve the user experience that will be addressed include: 

- The [OCA specificaton](https://swiyu-admin-ch.github.io/specifications/oca/) for improved credential processing and visualization is not yet implemented.
- A combined presentation of multiple credentials is not yet possible.
- The visualization of trust statements is not yet finalized (e.g., concerning unknown actor, not in base registry, or issuer/verifier trust statements). 
- The presentation of credentials offline is not yet possible.


### Swiyu Check App 

Security and privacy related aspects that will be addressed include: 

- Payload encryption during verification flow is not yet implemented. 

Aspects to improve the user experience that will be addressed include: 

- The combined verification of multiple credentials is not yet possible. 

### Swiyu Generic Verifier 

Security and privacy related aspects that will be addressed include: 

- Payload encryption during verification flow is not yet implemented. 

Aspects to improve the user experience that will be addressed include: 

- The combined verification of multiple credentials is not yet possible. 

## Roadmap

The following table provides a reference that indicates which standards are currently employed within the swiyu Public Beta Trust Infrastructure. While the Confederation aims to provide a degree of assurance and stability for integrators even at this early stage, evolution seems inevitable. Consequently, set of selected standards will be updated or extended if perspectives within the implementing organizations change. Changes will be considered, especially if they benefit privacy-protection for users, increase the security and stability of the overall system, or if standards converge to serve the purpose of fostering interoperability. 

A high-level overview of the further development of the swiyu Public Beta Trust Infrastructure is provided on [GitHub](https://github.com/orgs/swiyu-admin-ch/projects/1/views/7). We invite interested parties to subscribe via [GitHub discussions](https://github.com/orgs/swiyu-admin-ch/discussions/11) or [RSS feed](https://swiyu-admin-ch.github.io/release-announcements/) to our release announcements to be informed about upcoming changes. We currently differentiate between support for [Public Beta](https://www.eid.admin.ch/en/public-beta-e) and the initial Go-live support.

### Initial Supported Technical Standards

| Aspect      | Current Hypothesis   | Public Beta Support   | Initial Go-live Support |  
| ----------- | ----------- |----------- |----------- |
| **Identifiers**       | Decentralized Identifiers (**DIDs**) v1.0 according to [W3C](https://www.w3.org/TR/did-core/) <br> DID Method: **[did:tdw/did:webvh](https://identity.foundation/trustdidweb/)**     | **SELECTED** <br> Hosted on central base registry provided by Confederation | **CANDIDATE** Final decision for productive system pending |
| **Status Mechanisms**       | [Statuslist](https://datatracker.ietf.org/doc/draft-ietf-oauth-status-list/) | **SELECTED** | **HIGH** |
| **Trust Protocol**       | [Trust protocol based on VCs](https://swiyu-admin-ch.github.io/specifications/trust-protocol/)  | **SELECTED** <br> Initial support of the "identity" trust statement by Confederation | **HIGH** <br> Additional support of issuer & verifier legitimacy (per VC schema) |
| **Communication Protocol (Issuance/Verification)**       | OID4VC/OID4VP <br> [Issuance](https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0-ID1.html) <br> [Verification](https://openid.net/specs/openid-4-verifiable-presentations-1_0-ID2.html)  | **SELECTED** <br> In accordance with [Swiss profile](https://swiyu-admin-ch.github.io/specifications/interoperability-profile/) | **HIGH** |
| **Payload Encryption**       | [JWE](https://www.rfc-editor.org/rfc/rfc7516.html) as proposed by the communication protocol  | **CANDIDATE** | **CANDIDATE** |
| **VC-Format/Signature-Scheme Combination**       | [SD-JWT VC](https://datatracker.ietf.org/doc/draft-ietf-oauth-sd-jwt-vc/) & ECDSA   | **SELECTED** <br> In accordance with [Swiss profile](https://swiyu-admin-ch.github.io/specifications/interoperability-profile/) | **HIGH** |
| **Device Binding Scheme**       | **Hardware** based device binding depending on capabilities provided by [Apple](https://developer.apple.com/documentation/cryptokit/secureenclave) or [Android](https://source.android.com/docs/security/features/keystore) mobile devices <br> **Software** based device binding implemented by wallets  |  Hardware  **SELECTED** <br> Software **UNSUPPORTED**  | Hardware **HIGH** <br> Software **HIGH** |
| **VC appearance**       | Visualization of Verifiable Credential with [OCA](https://swiyu-admin-ch.github.io/specifications/oca/) | **UNSUPPORTED** | **HIGH**|

**Probability interpretation:** <br>
- **UNSUPPORTED** = No Solution provided, <br>
- **OPEN** = Options are being assessed, <br>
- **CANDIDATE** = Standard/Specification is in selection, <br>
- **HIGH** = Current hypothesis for implementation, <br>
- **SELECTED** = Chosen for system

The versions of the referenced specifications may change. In particular, the e-ID program is planning to upgrade most of the implemented specifications to newer and more mature versions until the Go-live.

### Additional open points

The delivery of all open points noted here is currently undated.

- The implementation of [privacy-preserving hardware device binding](https://swiyu-admin-ch.github.io/technology-stack/#credential-storage-and-device-binding)
- The implementation of [batch-issuance](https://swiyu-admin-ch.github.io/specifications/interoperability-profile/#privacy-considerations)

A more detailed roadmap for the swiyu Trust Infrastructure will follow.
