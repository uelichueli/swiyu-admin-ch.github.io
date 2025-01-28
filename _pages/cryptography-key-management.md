---

permalink: /cryptography-key-management/
toc: true
---
# Test with not Title in Header 

## Current Situation

For the first release the following technologies will be supported:

| Category | Selection |  
| ---------|----------|
| Credential format | SD-JWT (used by fedpol for e-ID issuing and supported by the federal wallet) |
| Credential signature scheme | ECDSA – P256 |
| Hardware based holder binding enforcement | Yes |
| Identifiers as proposed by the e-ID Act | DID-Documents |
| Revocation mechanisms | Token Status Lists |
| Communication protocols | OID4VCI/VP |

*An updated more detailed list of supported technologies and references to more detailed specifications can be found under:*
[Tech Roadmap](./tech-roadmap.md)

The following subject areas were the main drivers in this choice:

### Timely delivery
A multitude of Swiss services await a government issued digital identity (e.g. in the areas of public sector login, digitalization in the healthcare, protection of youth and minors). Therefore, a delay in providing the e-ID is highly unfortunate. It is the assessment of the e-ID development program, that by using the selected technologies timely delivery is most feasible.

### Hardware holder binding and availability of cryptographic devices
Amendments made by the Swiss parliament to the  [e-ID Act](https://www.parlament.ch/de/ratsbetrieb/suche-curia-vista/ratsunterlagen?AffairId=20230073#Default=%7B%22k%22%3A%22PdAffairId%3A20230073%22%2C%22r%22%3A%5B%7B%22n%22%3A%22PdDoctypeDe%22%2C%22t%22%3A%5B%22%5C%22%C7%82%C7%824661686e65%5C%22%22%5D%2C%22o%22%3A%22and%22%2C%22k%22%3Afalse%2C%22m%22%3Anull%7D%5D%7D), postulate that during issuance a binding between holders and their e-ID must be ensured. This will be achieved by binding the digital credential to a device (with a cryptoprocessor) in control of the user during the issuance process. Given the current landscape of mobile devices, hardware binding functionalities are limited. Only ECDSA signatures are commonly available as hardware-backed keys on Android and iOS devices. 
While available in theory, currently the Confederation is not in possession of implementations, which combine existing hardware backed keys and unlinkable proof mechanisms that have been independently vetted and have a proven secure and performant usage over significant numbers of people. 
