# Decentralized Identifiers and the Eclipse Dataspace Connector

# Introduction

## Purpose and Target Audience

This document assumes a basic understanding of the concept of International Dataspaces and some knowledge about the Eclipse Dataspace Components within that context. Also, basic acquaintance with the mechanisms of public key infrastructures (PKI) and the domain name service (DNS) is required.

Its purpose is to provide an overview of what Decentralized Identifiers (DIDs) and Verifiable Credentials (VCs) are, why dataspaces need them, and how the Eclipse Dataspace Components (EDC) leverages DIDs and VCs to implement decentralized identity management.

## The Problem of Identity

Dataspaces enable the discovery and sharing of data between participants. Any such transaction is predicated on trust on many levels: trust that the data is accurate, that the transmission is secure, that agreed usage policies will be adhered to, etc. But the first and basic principle of trust must always be trust that the entity one is dealing with truly is who they claim to be. And trust needs continued verification – after all, today’s trusted third-party (which is only verified once) could become tomorrow’s attack vector.

In a dataspace, one might want to discover and use data from (or share data with) entities one does not have a previous legal relationship with, and do so in an automated way. To enable this, there needs to be a system of identity verification. A system that ideally would come without the need to trust a central authority. After all, who verifies the identity of the verifier?

But identity may not be enough. A verified entity might want to make claims about itself, claims to certain accreditations by third-party authorities (e.g., business licenses, GDPR compliance, carbon neutrality certifications, etc.). Such claims also must be verifiable. And, again, ideally without the need to contact any third party, whose identity in turn would have to be verified (and whose continued existence would be paramount), and who could potentially act as gatekeeper and exclude some entities from participating in the market (for whatever reasons).

Decentralized Identifiers aim to solve these challenges.

# Decentralized Identifiers

Decentralized Identifiers (DIDs) are globally unique identifiers that can be created and fully controlled by the owner (subject) of the identifier itself without the need for external authorities. As such, they are self-sovereign identities. They give entities (individual people, corporations, or digital entities like an EDC) the ability to de-couple sensitive information from the identifier itself, make them publicly discoverable (through various methods, see below), and enable verifiable credentials through use of cryptographic proof systems (e.g., PKI).

A DID is a simple three-part string:

![DID three-part string](images/parts-of-a-did-2.svg)

(Source: https://www.w3.org/TR/did-core/)

The **scheme** simply identifies the string as a DID. The **DID method** specifies the mechanism used to create, resolve, update, and deactivate (CRUD) the DID itself as well as its associated DID document (see below). The **DID method-specific identifier** specifies where the DID document may be found.

There are numerous ways DIDs may be implemented, and there is a growing list of such implementations. These implementations are called methods. They each have their own specification and are usually associated with specific verifiable data registries (see Verifiable Credentials below). **DID resolvers** are used to handle each specific method implementation for locating the document associated with a DID. Later in this document, a method will be introduced in the context of the EDC called DID:Web.

Next in the above string, the second integral part of a DID is its associated DID document. This is a simple JSON file that contains information associated with the DID, such as the public key of its owner (also known as the DID controller) and the location where further information (such as verifiable credentials) may be found (a service endpoint). A sample DID document would look like this:

```
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/ed25519-2020/v1"
  ]
  "id": "did:example:123456789abcdefghi",
  "authentication": [{
    // used to authenticate as did:...fghi
    "id": "did:example:123456789abcdefghi#keys-1",
    "type": "Ed25519VerificationKey2020",
    "controller": "did:example:123456789abcdefghi",
    "publicKeyMultibase": "zH3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"
  }]
}
```
(Source: https://www.w3.org/TR/did-core/)

If another entity supplies a verifiable credential that was signed by the issuing authority with its private key, one can easily discover the public key and check if the credential really was issued by the authority.

# Verifiable Credentials

Verifiable Credentials are a W3C Standard. A verifiable credential in essence is a claim that an entity (the **holder** of the credential) makes about itself, which another entity (the **issuer** of the credential) bestowed upon that holder, and that a third entity wants to check (the **verifier**).

![verifiable credentials ecosystem](images/ecosystem-2.svg)

 (Source: https://www.w3.org/TR/vc-data-model/)

As an example, consider the case of a driver’s license. A public authority (e.g., department of motor vehicles) issues a driver a license to operate a motor vehicle. In a routine check, a police officer may want to verify the authenticity of the license presented by the driver. They would do so by checking with the public authority through a data registry. Verifiable credentials as envisioned by the W3C transfers that process in its entirety to the digital world in a machine-readable way.

In the above diagram, the verifiable data registry can be implemented by using a DID method.

# Identity Hubs

The holder of verifiable credentials as described above needs some way to store those credentials digitally and securely, and ideally in such a way as to allow programmatic access to them by verifiers. This is where identity hubs come into play.

Taking the driver’s license example from above, an identity hub fulfills the role of the wallet of the driver, where the license is kept (the concept of digital wallets is also widely known today in the cryptocurrency world to securely store one’s digital assets). But an identity hub provides more than just storage of verifiable credentials.

![identity hub](images/topology-2.svg)

(Source: https://identity.foundation/identity-hub/spec/)

As shown in the topology above, in addition to storing verifiable credentials, identity hubs (through standardized APIs) enable the exchange of messages between participants.

Identity hubs act as the service endpoints defined in DID documents:

```
{
  "id": "did:example:123",
  "service": [{
    "id":"#hub",
    "type": "IdentityHub",
    "serviceEndpoint": {
      "instances": ["https://hub.example.com", "https://example.org/hub"]
    }
  }]
}
```
(Source: https://identity.foundation/identity-hub/spec/)

# DID:Web

DID:Web is a specific DID method. It is based on the domain name system (DNS) and allows participants to bootstrap trust based on a domain’s existing reputation. Essentially, by trusting the domain resolution, a DID document stored at a specific location on a web server is considered trustworthy because the entity in control of the domain also has control over documents posted on the servers in their domain.

Taking an example DID of *did:web:example.com*, the associated DID document would look like this:

```
{
  "@context": "https://www.w3.org/ns/did/v1",
  "id": "did:web:example.com",
  "verificationMethod": [{
     "id": "did:web:example.com#owner",
     "type": "Secp256k1VerificationKey2018",
     "owner": "did:web:example.com"
  }],
  "authentication": [
     "did:web:example.com#owner"
  ]
}
```
(Source: https://w3c-ccg.github.io/did-method-web/)

A DID-Web resolver would simply follow the standardized path of https://example.com/.well-known/did.json to retrieve this DID document. Alternatively, if a specific path would be provided in the DID, as in *did:web:example.com:did-storage:did1*, the resolver would look for the DID document at https://example.com/did-storage/did1/did.json.

DID-Web is a very simple, but also practical solution. It avoids the complexity of blockchains, but provides an easy path to later switching to a different DID method, which could also be a blockchain based one). DID:Web uses an existing distributed trust network: DNS. Note that this does, of course, come with its own set of questions around security (a simple hijacked local hosts file pointing to a fraudulent IP for the domain comes to mind). It is, however, a solution which can be implemented relatively easily today which only relies on well-known and mature technology readily available.

# The EDC Solution

The Eclipse Dataspace Components implement decentralized identifiers and use DID:Web as the default method. However, as the EDC are extensible with custom modules additional DID methods can be easily added. It even supports the replacement of decentralized identity with centralized identity providers if a dataspace should desire to do so.

The identification verification process looks like this (both “Participant” and “Provider” are participants in a dataspace):

![EDC DID workflow](images/did-edc.png)

The two entities iterate through the following steps:
1.  The participant EDC sends its DID to the provider.
1.	The provider checks if the domain provided with the DID can be resolved and is trusted (e.g., not part of some blacklist).
1.	See 2.
1.	The provider resolves the DID provided by the participant (did:web:foo.com:guid).
1.	The provider requests the EDC DID document from the participant’s web server.
1.	The participant’s web server provides the EDC DID document.
1.	The provider resolves the participant’s general company DID (did:web:foo.com).
1.	The provider’s DID-Web resolver forwards the EDC DID document to its own EDC.
1.	The participant’s web server provides the company DID document.
1.	The provider’s DID-Web resolver forwards the company DID document to its own EDC.

Optionally, corporate verifiable credentials may be exchanged and verified:

11. The provider requests the corporate authorizations from the participant.
12. The participant provides its credentials.
13. The provider verifies the corporate authorizations (note that this would involve another iteration of DID verification, but external to the dataspace).

And the original flow continues:

14. The provider verifies the participant’s identity and claims.
15. The provider acknowledges the identification to the participant.

The EDC’s modular architecture allows for easy addition of different DID resolvers, thereby allowing effortless switching between different DID methods, using several methods concurrently in a hybrid setup, or even using a universal DID resolver to manage DID resolution externally. One could also (e.g., for purely intra-organizational purposes) use a DID resolver for a centralized identity solution.

# References & Resources

EDC project: https://projects.eclipse.org/projects/technology.dsconnector

EDC GitHub repository: https://github.com/eclipse-dataspaceconnector/DataSpaceConnector

EDC YouTube Channel: https://www.youtube.com/channel/UCYmjEHtMSzycheBB4AeITHg

Decentralized Identifiers: https://www.w3.org/TR/did-core/

DID-Web: https://w3c-ccg.github.io/did-method-web/

Verifiable Credentials: https://www.w3.org/TR/vc-data-model/

Identity Hub: https://identity.foundation/identity-hub/spec/
