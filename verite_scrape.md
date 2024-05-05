
URL: https://developers.circle.com/verite/docs/issuance
Title: Issuance
Description: undefined
Keywords: undefined

This tutorial will walk you through issuing verifiable credentials using the verite sdks. For additional context, see [Issuance Flow](/verite/docs/issuing-credentials). A complete example of building an issuer is available at our [demo-issuer](https://github.com/circlefin/verite/packages/demo-issuer) demo.

For the sake of this demo, we will be using Decentralized Identifiers (DIDs) to identify the issuer (you) and subject (the person or entity the credential is about), as well as JSON Web Tokens (JWTs) as the means of signing and verifying the credentials. Strictly speaking, you do not need to use JWTs, but as they are industry-standard and tooling extensively available, they are used throughout this sample implementation.

In practice, you are not limited to using DIDs to identify and prove ownership; anything with a public/private key pair can effectively be used to identity a credential's subject and later prove that identity. Public blockchain addresses offer less privacy and rotation/recovery capabilities than DIDs, but can simplify VC handling in some architectures where DIDs are inhibitive. To make them subjects of verifiable credentials, blockchain addresses can be expressed as DID-like URI's using the [did:pkh method](https://github.com/w3c-ccg/did-pkh/blob/main/did-pkh-method-draft.md), based on the [CAIP-10 URI scheme](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-10.md). To prove these, you will need a signature to recover the public key of a given address; the off-chain signatures used to "connect wallet" in most contemporary web3 apps and dApps is adequate for such purposes.

##
Prerequisites: Issuer Setup
[](#prerequisites-issuer-setup)
[See issuer setup instructions](/verite/docs/issuer-setup).

##
Step 1: Create a DID for your issuer
[](#step-1-create-a-did-for-your-issuer)
In order to issue a credential, you must have some way of identifying yourself as an Issuer. This allows the credential to be "verifiable" by a 3rd party.

To start, you should create a DID keypair.

JavaScript
import { randomDidKey } from "verite"
import { randomBytes } from "crypto"

const issuerDidKey = randomDidKey(randomBytes)
That keypair should look like the following:

JavaScript
{
  id: 'did:key:z6Mkf2wKCqtkNcKB9kRdHnEjieCLJPSfgwuR19fxBhioAwR7#z6Mkf2wKCqtkNcKB9kRdHnEjieCLJPSfgwuR19fxBhioAwR7',
  controller: 'did:key:z6Mkf2wKCqtkNcKB9kRdHnEjieCLJPSfgwuR19fxBhioAwR7',
  publicKey: Uint8Array(32) [...],
  privateKey: Uint8Array(64) [...]
}
Keep this keypair safe. You will be using this to sign all Verifiable Credentials. If you lose a keypair, you won’t be able to sign future credentials with the same key, and may temporarily lose the ability for verifiers to verify your credentials.

###
Optional step: Use did:web
[](#optional-step-use-didweb)
In order to leverage trust already established by your domain name, you can expose your did:key (created above) on your domain via the [did:web](https://w3c-ccg.github.io/did-method-web/) method and use that as reference.

For example, instead of listing the issuer of your VCs as did:key:z6Mkf2wKCqtkNcKB9kRdHnEjieCLJPSfgwuR19fxBhioAwR7, you could issue your VCs from did:web:example.com.

To do this, you need to create a .well-known/did.json file on your domain, which will allow did resolvers to find your public keys. An added benefit of did:web is that it will allow you to rotate your keys at your desire; previously-issued VCs will no longer verify against a rotated key.

We won’t go into the specifics in this article, but you can read more about did:web [here](https://w3c-ccg.github.io/did-method-web/), and you can set up a did:web in minutes using [this tutorial](https://spruceid.dev/docs/didkit/did-web).

##
Step 2: Receive a subject’s (end-user) DID
[](#step-2-receive-a-subjects-end-user-did)
In order to issue a Verifiable Credential, you must know where to issue the credential. This can be a subject’s DID or any other public key (such as an ethereum address, etc). In this case, we’ll continue using DIDs.

Generally, you would follow the [Presentation Exchange (PEx)](https://identity.foundation/presentation-exchange/) flow as a means of allowing the subject to submit their DID as well as a request for a specific type of VC.

To do so, you need to create a [Credential Manifest](https://identity.foundation/credential-manifest/) showcasing what attestations you offer as Verifiable Credentials. In this example, we will offer a "Know Your Customer, Anti-Money Laundering" attestation (KYC/AML Attestation), meaning we are compliant with US regulations and have checked your account to determined you are not a bad actor. The benefit of this type of VC is that another service can be compliant with regulations without any personal information exposed in the VC.

JavaScript
import { buildKycAmlManifest } from "verite"

const manifest = buildKycAmlManifest({ id: issuerDidKey.controller })
This manifest contains instructions for the subject to use to request a VC.

The subject uses that manifest to build a “Credential Application”, which serves as a request for a VC. For example, a subject could create the application as such:

JavaScript
import { randomDidKey, composeCredentialApplication } from "verite"

// The subject needs a did:key, generate a random one:
const subjectDidKey = randomDidKey(randomBytes)

const application = await composeCredentialApplication(subject, manifest)
##
Step 3: Issue the Verifiable Credential
[](#step-3-issue-the-verifiable-credential)
Once the subject has requested a VC and submitted their DID (as part of their credential application), the Issuer can create a VC.

For our example, we're building a VC containing a KYC/AML Attestation. This attestation is quite simple in Verite. It defines an authority which has performed proper KYC/AML checks on the subject (in this example the authority is Verite, with the DID of did:web:verite.id), so that will used in the issuer field.

From the Credential Application, the issuer also confirms:

- Who to issue the VC to -- the subject identifier is the holder field from the VP in the Credential Application
- That the subject actually controls the identifier by signing a proof (along with the challenge)
- Any other inputs requested by the manifest (if specified)
We transport Verifiable Credentials using a Verifiable Presentation. The presentation allows the issuer to bundle the credentials before sending to the subject, along with signed proof.

Putting this together, we can do the following:

JavaScript
import {
  composeFulfillmentFromAttestation,
  validateCredentialApplication,
  buildIssuer,
  KYCAMLAttestation
} from "verite"

const decodedApplication = await validateCredentialApplication(application)

const attestation: KYCAMLAttestation = {
  type: "KYCAMLAttestation",
  process: "https://verite.id/definitions/processes/kycaml/0.0.1/usa",
  approvalDate: new Date().toISOString()
}
const credentialType = "KYCAMLCredential"
const issuer = buildIssuer(issuerDidKey.subject, issuerDidKey.privateKey)

const presentation = await composeFulfillmentFromAttestation(
  issuer,
  decodedApplication.holder,
  manifest,
  attestation,
  credentialType
)
The subject can then decode the presentation and store their Verifiable Credential for future use.

🎉 That’s it. You have now issued a Verifiable Credential.

You can view this demo as a full working example in our [demo-issuer](https://github.com/circlefin/verite/tree/main/packages/demo-issuer) demo.

Updated 6 months ago


URL: https://developers.circle.com/verite/docs/how-to-create-a-verifiable-credentials-registry-smart-contract-in-solidity
Title: How To Create a Verifiable Credentials Registry Smart Contract in Solidity
Description: undefined
Keywords: undefined


URL: https://developers.circle.com/verite/docs/verification-patterns-part-2
Title: Verification Patterns, Part 2
Description: undefined
Keywords: undefined

Part 2 of this 2-part series explains the [did:pkh](https://github.com/w3c-ccg/did-pkh/blob/main/did-pkh-method-draft.md)/[CACAO](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-74.md#simple-summary) variation for Verite data models and flows, which provides an entry path for wallets that may not support sufficient functionality for emerging decentralized identity patterns

Since some wallets may not themselves be willing to embed protocol-specific logic (interaction with verifiers) or more general verifiable-credential logic, we have to find a kind of "minimum viable" level of support for today’s non-DID-enabled crypto wallets. While handling DIDs and signing Verifiable Presentations brings a kind of secure indirection that enables portability and multi-chain wallets, these properties are not strictly essential to the core functionality of Verite. For this reason, we define a crypto wallet that can receive and pass to dApps a Verifiable Credential issued against its blockchain address adequate, with a few adjustments and supplements.

##
Phase 0: Issuance Directly to Crypto Wallet
[](#phase-0-issuance-directly-to-crypto-wallet)
In a crypto-wallet centric end-to-end flow, the trust model is different and the interplay between credential wallet and crypto wallet can be greatly simplified. The credentials themselves must also be slightly different– instead of obtaining the credential subject DID directly from the wallet to which they are being issued, the issuer will use a credential subject identifier based on a specific blockchain address controlled by that wallet. Using DID terminology, rather than attest to the controller of a wallet, it attests only to a specific address controlled by that wallet.

This greatly simplifies the ownership question, by relying on native mechanisms for proving ownership of the address– at the time of issuance, as well as at time of verification of the credentials.

###
Two Options of Expressing a Blockchain address as a DID (and as a VC subject)
[](#two-options-of-expressing-a-blockchain-address-as-a-did-and-as-a-vc-subject)
Instead of defining the subject of the VC as a chain-agnostic DID provided by the wallet, the issuer will deterministically generate a DID from the blockchain address controlled by the connected wallet. Multiple DID methods allow this possibility; we’ll describe two of them, assuming a wallet with an Ethereum address (referred to as ETH_ADDRESS).

- did:key method - issue against a crypto wallet’s public key: If the issuer has the wallet address ETH_ADDRESS and any signature over a known message, the corresponding public key can be recovered using the ecrecover mechanism ported over from Bitcoin in the early days of Ethereum. In this way. the issuer can deterministically obtain a[ did:key](https://w3c-ccg.github.io/did-method-key/) DID that will be used as the value of credentialSubject.id. This is the method Circle will begin with, for ease of implementation by participants.
- In this case, the mapping is: did:key:<ecrecover(ETH_ADDRESS, signature)>
- For blockchains that do not use a Bitcoin-style pay2hash address system, like Solana and Polkadot, no recovery from a signature is necessary because the base form of the address is already a public key supported by [multibase](https://datatracker.ietf.org/doc/html/draft-multiformats-multibase)and thus by [did:key](https://w3c-ccg.github.io/did-method-key/#introduction).
- did:pkh method - issue against a crypto wallet’s public address: Other DID methods, like [did:pkh](https://github.com/w3c-ccg/did-pkh/blob/main/did-pkh-method-draft.md), allow DIDs to be defined directly based on blockchain addresses in their commonly-used, public-facing forms. Long term, this is the preferred method. Among other advantages, the implementation across chains is more apparent.
- In this case, the mapping is: did:pkh:eip155:1:<ETH_ADDRESS>. eip155 here refers to the EVM namespace (defined in EIP155), while 1 refers to the ethereum mainnet according to EIP155.
- Just as the did:key URI scheme relies on the [multibase](https://datatracker.ietf.org/doc/html/draft-multiformats-multibase) registry, so does the did:pkh URI scheme rely on the ChainAgnostic Standards Alliance’s [namespace registry](https://github.com/ChainAgnostic/namespaces) to add non-EVM namespaces.
- In cases where human-readability is important, end-users can introspect the VC and see their familiar address, as opposed to a public key that in pay2hash systems like BTC or ETH, they might never have seen or know they control
###
Sign-In With Ethereum and dApp-native Identity
[](#sign-in-with-ethereum-and-dapp-native-identity)
Wallets that have not incorporated decentralized identity capabilities rarely support JWT _signing _features, or other token mechanics that are common to the traditional web2 identity world. For this reason, many web3 sites and dApps have started using the wallet connection pattern to create a more feature-rich and auditable session mechanism via offchain message signing (i.e. personal_sign on EVM systems). Equivalents for other blockchain ecosystems, such as Solana, are forthcoming.

In the case of issuance, this signature is enough to extract the crypto wallet’s public key, as mentioned above. Importantly, though, it also enables delegated keys to sign offchain events without another onerous or fraught wallet-interaction, as we will see below.

##
Phase 1: Off-chain Verification
[](#phase-1-off-chain-verification)
###
Variant: Crypto-Wallet only with only VC storage capabilities
[](#variant-crypto-wallet-only-with-only-vc-storage-capabilities)
At verification time, when a wallet "connects” to a dApp by providing an off-chain signature over a structured [authentication message](https://github.com/ethereum/EIPs/blob/5e9b0fe0728e160f56dd1e4cbf7dc0a0b1772f82/EIPS/eip-4361.md#informal-message-template), the dApp will have the wallet’s address (and live proof-of-control, if the authentication message included a secure nonce) so it can simply compare this address with the corresponding DID:PKH credentialSubject.id of the VC. This way, the verifier will not need to do an ownership check on the VC, and the dApp can trust the verifier to have received credentials from the right wallet because it, too, will require a wallet connection and prove ownership of the same wallet.

Without necessarily even having to parse, validity-check, or introspect the verifiable credentials, any wallet that can store them (whether as JWTs or even as raw text files) can submit them directly to verifiers, as shown below.


Note: while it is recommended that crypto wallets parse verifiable credentials and check their validity or safety, crypto wallets could in theory allow blind passthrough if the user can assume the responsibility for their contents. In the Verite case, there are little security concerns or abuses possible.

By itself, however, this bare VC is inferior to a VP from a full-featured decentralized-identity wallet, since it does not contain a non-repudiable off-chain wallet signature for auditing purposes. Or, to put it another way, it is only as trustworthy as the authentication of the wallet that sent it to you, and there is little standardization of the receipts you keep of crypto-wallet authentication to replay it for a future auditor or security review.

While the corner cases of impersonation or exfiltrated VCs might be vanishingly rare, the "audit trail” of a bare VC is weaker than a VC wrapped in a timestamped signature. For this reason, we encourage Verite dApps to create a functional equivalent to a verifiable presentation in the form of a signed presentation receipt (signed with a session-specific ephemeral key) for logging purposes. To accomplish this, we return to the Sign-In With Ethereum pattern to elaborate on its key delegation feature.

####
Sign-In With Ethereum Flow
[](#sign-in-with-ethereum-flow)
As mentioned above, we support the emerging standard approach of the "Sign-In With Ethereum” mechanism which defines a sign-in message that includes a domain-binding anchor, an ephemeral session key, and other caveats. While the ephemeral session key property was not essential to the issuance wallet connection, it can be useful in the verification situation for more trustless (and auditable) logging.

By generating an ephemeral key and including it in the initial wallet-connection message for the crypto wallet to sign over upon authenticating itself to the dApp, the wallet effectively "delegates” that ephemeral key for the duration of the session. In UX terms, this saves the user from a distinct off-chain wallet signature at each confirmation or consent event. Carefully defining the [other properties](https://eips.ethereum.org/EIPS/eip-4361#message-field-descriptions) of the SIWE message, dApps can secure and constrain that delegation, link to an applicable terms-of-service agreement, enable DNS-based domain-checks analogous to the "lock symbol” in modern browsers, etc.

Once the user has "connected” their wallet by signing this SIWE message, a CACAO is generated as a receipt of that session (and of the delegation of signing rights to the key). This allows the dApp to use smoother UX than requiring a full off-chain wallet signature to confirm each consent event or internal transaction (such as the presentation of VCs in a signed VP). But it also provides a compact and tamperproof way of encapsulating each event or internal transaction as a time-stamped and signed object for logging purposes– this makes each event as verifiable as an off-chain (or on-chain) signature, via the indirection of the delegated key.

####
Ownership Verification
[](#ownership-verification)
You could say that the crypto wallet delegates the encapsulation and signature of a VP to the dApp, which creates a short-lived key with which to sign the VP, which is a kind of standardized logging object for a presentation event. This allows the verifier to confirm that the dApp is interacting on behalf of the wallet. Since the Verifier has confirmed control of the wallet address with a SIWE message, and the VC is issued to the address directly, there is no ownership verification needed as with a decentralized wallet; thanks to the CACAO, future auditors can replay these interactions and re-check signatures and tamper-proof objects to confirm all of these transactions trustlessly.

####
Detailed Flow
[](#detailed-flow)

- Wallet initiates DeFi transaction with dApp.
- dApp generates a session-specific ephemeral signing key and encoded it in the SIWE message for the wallet to sign. This generated session key will delegate the wallet for future signings, once after wallet vouches it (by signing the CACAO).
- Once the wallet has signed it and returned it to the dApp, the signature and message are encoded into a compact [CACAO](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-74.md) session receipt for logging and forensic purposes (if needed).
- Next the dApp lets the verifier know about the session, by POSTing the receipt to an endpoint (eg. [signIn](https://github.com/circlefin/verite-minter-allowlist/blob/main/backend/src/index.js#L136)). The signed receipt also includes [caveats](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-74.md#test-cases), a domain-binding, an expiration, and other constraints to secure the delegation and the transfer of the session parameters.
- The verifier saves the CACAO. The verifier only uses this CACAO in the scope of this verification session (to prove the VP signed by the ephemeral key). Once the CACAO verification step is completed, the session object will be updated.
- Instead of sending the wallet to verify directly with the verifier (as in the previous post), the wallet will submit the VC directly to the dapp (or an agent/service it trusts). The dApp presents the prompt to verify.
- Wallet submits the bare VC.
- Subsequent requests from the dApp will include a reference to the session which the verifier can use if they need to check signatures made by that key. The VC(s) submitted by the dApp in this case will not be signed in a VP with the wallet’s key; instead, it/they will be put into a VP and signed by the dApp using the ephemeral key (the signing key mentioned in the first step above) delegated to it by the SIWE message. Introspection into the CACAO is required to extract the public key that signed the VP, as well as a signature from the wallet key over the public key.
- When all the information is submitted to the verifier, the verifier needs to examine the ownership of the credential:
- Extract the public key of the session signing key from the resources section of the CACAO
- Use the public key of the session signing key to validate the VP’s signature. This is to ensure that the dApp properly (which held the key and got user consent to delegate signing rights to it) signed the VP and that it has not been tampered with in transport.
- Compare iss in CACAO with the wallet’s DID in VC (in this case a did:pkh representing the wallet address as a long URI). They should match, if the dApp’s SIWE message. conforms to the SIWE specification. This is to check the wallet which vouched the session key is the subject (and holder) of the VC, which is also connected to the dApp with a signature over a nonce, included in the CACAO to keep future auditors from having to trust the verifier.
##
Conclusion:
[](#conclusion)
Circle’s implementation of the Verite protocol allows us to serve our customers and the dApps they interact with equally, putting the rigor of our KYC processes at the service of a process that is auditable and verifiable end-to-end without duplicating KYC process or PII from those processes across the chain of asset custody. We are proud to be driving the Verite process, and welcome more implementations, whether end-to-end issuer+verifier solutions like ours or more focused implementations that bring more wallets and more users into the ecosystem.

As the Circle team updates its documentation and sample implementation to reflect the new patterns and flows, we will continue to work with them to share the insights we are gaining from our exploratory work with dApps and clients.

Updated 6 months ago


URL: https://developers.circle.com/verite/docs/verite-protocol-repositories
Title: Open-Source Repositories
Description: undefined
Keywords: undefined


URL: https://developers.circle.com/verite/docs/implementing-a-full-featured-verite-identity-wallet
Title: Implementing a full-featured Verite Identity Wallet
Description: undefined
Keywords: undefined

This guide is written for developers seeking to integrate the "wallet-bound" Verite flows and data models into custodial or non-custodial wallets. What follows is best understood as a checklist for adding "identity wallet" capabilities (handling Verifiable Credentials natively and as flexible as built-for-purpose identity wallets do) to an existing mobile wallet, whether non-custodial, custodial, or semi-custodial (MPC, multi-sig, etc).

##
Minimal Wallet Requirements - Summary
[](#minimal-wallet-requirements---summary)
- Generate (or supply) and manage decentralized identifiers for credential subject
- did:key (offchain, local-only) is recommended for staging/test-net development
- Generate VC/VP-JWT signatures
- Support credential storage and retrieval
- Support credential issuance and exchange flows described in this document
##
Architectural Foundations
[](#architectural-foundations)
Verite uses a decentralized identity architecture, in which the individual (subject/holder) directly requests and receives credentials from an issuer, storing them in a wallet that governs a unique and long-lasting DID. In this model, individuals decide when and with whom they want to share their credentials, referred to as verifiers, or more generally, relying parties. In some architectures and use-cases, these counterparties may know the wallet and its user only by a DID, or by a DID and the contents of credentials managed by the wallet.


The relying party verifies the credential, without necessarily needing to contact the issuer, through an extensible mechanism enabled by the VC specification. The verification process is designed to be maximally privacy-respecting and cryptographically secure.

###
Concepts Overview
[](#concepts-overview)
Verite's basis is the [W3C Verifiable Credentials (VCs) Data Model](https://www.w3.org/TR/vc-data-model). A credential is a claim made by an issuer about an individual (subject), and a verifiable credential is a cryptographically secure wrapper around that credential enabling verification and handling by common, standards-based libraries such as JWT tooling.

Following is a list of concepts used in Verite.

- W3C Verifiable Credentials: flexible, tamper-evident way for an issuer to make a claim about a subject in a way that is independently verifiable and privacy-preserving. VCs are the data model used for Verite claims
- Verifiable Presentations: also defined in the VC spec, are a way to securely package a set of VCs for transmission to a relying party, in a way that also allows the subject to prove control over the credentials.
- Identifiers, Decentralized Identifiers: an "identifier" refers to both a subject and an issuer of a VC. The identifier data type is a URI, one of which is a [W3C Decentralized Identifiers (DIDs)](https://www.w3.org/TR/did-core/), which is used in Verite implementations. Verite does not require the use of DIDs, but these are one way to implement identifiers that minimize correlatability and enable proof of control over credentials.
- Credential Manifest: standard from the Decentralized Identity Foundation (DIF) for requesting and receiving a credential. [Credential Manifest](https://identity.foundation/credential-manifest/) allows an issuer to describe (in a machine-readable way) what types (schemas) of credentials they issue, and what their requirements are. It also describes the format for a subject/holder to submit an application for a credential that conforms to those requirements in a machine-readable way.
- Presentation Exchange: A DIF standard enabling a verifier to describe what types of credentials they require from a subject/holder, and how the subject/holder can send a submission. See [DIF Presentation Exchange](https://identity.foundation/presentation-exchange)
- Wallet and Credential Interactions: wallet interaction protocols use a lightweight flow loosely based on the work-in-progress [DIF Wallet and Credential Interactions](https://identity.foundation/waci-presentation-exchange/) spec.
###
Specifications and Libraries Used
[](#specifications-and-libraries-used)
See the [list of specifications and spec-conforming libraries](/verite/docs/specifications-and-libraries) used by Verite.

##
Credential Flows
[](#credential-flows)
The credential flows demonstrate use of a non-custodial credential and identifier wallet, but are easily adapted to hosted wallets, combination crypto+identity wallets, and various variations on the described flow. See the wallet-bound variants of the [issuance](/verite/docs/issuing-credentials#issuance-flow) and [verification](/verite/docs/verifying-credentials#verification-flow) flow sections elsewhere in the docs for context.

###
Issuance Flow
[](#issuance-flow)
####
Overview
[](#overview)
This flow enables a subject to send prerequisite information to the issuer before credential issuance. Prerequisites include:

- subject-controlled identifier (e.g. decentralized identifier) the credential should be issued to, along with a proof of control of the identifier
- optionally, an issuer may request input, in verifiable credential form or otherwise (not currently represented in the verite sample implementation or documentation)
The process is initiated by the wallet holder scanning a QR code, which allows the wallet to determine the input requirements, asking for consent before sending (typically including cryptographically signed proof). The issuer validates the request, issues a credential to the identifier specified by the subject, and returns the credential

This flow is based on the [DIF wallet and credential interaction (draft) specification](https://github.com/decentralized-identity/wallet-and-credential-interactions), which enables use of links or QR codes for wallet-initiated interactions.

####
Details
[](#details)


-
Recipient initiates the credential request process by scanning QR code with their mobile wallet

-
The wallet decodes the QR code, which provides an issuer endpoint with more details to continue the interaction:

-
QR code returns the URL from which to receive information at the challengeTokenUrl:

JSON
{
  "challengeTokenUrl": "https://verite.id/..."
}
-
Wallet GETs the payload:

GET {{challengeTokenUrl}}
-
The return type is a JWM containing a [credential manifest](https://identity.foundation/credential-manifest/#credential-application), credential request endpoint, and a challenge to sign.

JSON
{
  "id": "4487e7d1-7d10-4075-a923-bae9332266c1",
  "type": "CredentialOffer",
  "from": "did:key:z6Mkgw8mPijYRa3TkHSYtQ4P7S2HGrcJBwzdgjeurqr9Luqb",
  "created_time": "2021-09-14T01:22:05.816Z",
  "expires_time": "2021-10-14T01:22:05.816Z",
  "reply_url": "https://...",
  "body": {
    "challenge": "d273da29-74dd-46de-a53c-1677c51cc700",
    "manifest": {}
  }
}
-
Depending on the wallet implementation, the wallet generally prompts the user for approval to proceed, notifying which data is to be shared (from the credential manifest)

-
On approval, the wallet generates/provides a decentralized identifier, builds the credential request, and signs it to achieve [proof of identifier control](https://identity.foundation/presentation-exchange/#proof-of-identifier-control).

- In the Verite demo, the wallet generates a did:key DID, builds a [credential application](https://identity.foundation/credential-manifest/#credential-application), and signs the credential application, along with the challenge, with the private key corresponding to the DID.
- The Verite library exposes a convenience method createCredentialApplication for this purpose -- this is used by demo-wallet in preparing a credential application.
- Note: the Verite demo does not require the wallet to submit credentials for issuance, as the credential recipient is already known to the issuer, but the credential manifest and credential application structures allow for this if needed.
- Example: [Credential Application](#credential-application)
-
The wallet sends the credential application to the issuer's endpoint

POST {{replyUrl}}
-
The issuer issues the credential and returns it to the wallet

- The issuer accepts and validates the input and, on success, issues a Verifiable Credential.
- In the Verite demo, the issuer replies with a [credential fulfillment](https://identity.foundation/credential-manifest/#credential-fulfillment)-structured result containing a JWT-encoded Verifiable Presentation.
- Examples:
- [Credential Fulfillment](#credential-fulfillment)
- [Decoded Credential](#decoded-credential)
-
Wallet stores credential

###
Verification Flow
[](#verification-flow)
####
Overview
[](#overview-1)
This flow enables a verifier or relying party to request credentials and proof requirements from the subject/holder.

The process is initiated by the wallet holder scanning a QR code, which allows the wallet to determine the credential/proof requirements, asking for consent before sending (typically including cryptographically signed proof).

This flow is based on the [DIF wallet and credential interaction (draft) specification](https://github.com/decentralized-identity/wallet-and-credential-interactions), which enables use of links or QR codes for wallet-initiated interactions.

####
Details
[](#details-1)
The exchange flow similarly initiates with the credential holder performing a QR scan. See message samples in [Presentation Exchange](#presentation-exchange)


##
Appendix: Message Samples
[](#appendix-message-samples)
This section contains examples of the messages referenced above.

###
Credential Issuance
[](#credential-issuance)
####
QR Code
[](#qr-code)
Credential subjects initiate credential issuance flows by scanning a QR code, provided by the issuer, with their credential wallet.

Sample:

{
    "challengeTokenUrl": "https://verite.id/..."
}
####
Credential Offer
[](#credential-offer)
An issuer credential offer tells the wallet how to initiate a credential issuance request. It is a simple JWM wrapper around a DIF Credential Manifest.

Sample:

JSON
{
  "id": "4487e7d1-7d10-4075-a923-bae9332266c1",
  "type": "CredentialOffer",
  "from": "did:key:z6Mkgw8mPijYRa3TkHSYtQ4P7S2HGrcJBwzdgjeurqr9Luqb",
  "created_time": "2021-09-14T01:22:05.816Z",
  "expires_time": "2021-10-14T01:22:05.816Z",
  "reply_url": "https://...",
  "body": {
    "challenge": "d273da29-74dd-46de-a53c-1677c51cc700",
    "manifest": {}
  }
}
Details:

- from: who the message is from; in this case, the issuer
- reply_url: the URL the wallet should send the credential application to
- body.challenge: a challenge the wallet should sign when proving control, to prevent replays
- body.manifest: follows the DIF Credential Manifest spec
####
Credential Application
[](#credential-application)
A credential application is sent from the wallet to the issuer before issuance. It contains the recipient identifier and other information required for issuance. This follows the DIF Credential Manifest Spec.

What follows is a JSON object containing the same contents as a Verifiable Presentation in JWT form; there is no proof object, because it would be signed and transmitted as a JWT.

JSON
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "credential_application": {
    "id": "2ce196be-fcda-4054-9eeb-8e4c5ef771e5",
    "manifest_id": "KYCAMLManifest",
    "format": {
      "jwt_vp": {
        "alg": ["EdDSA"]
      }
    }
  },
  "presentation_submission": {
    "id": "b4f43310-1d6b-425d-84c6-f8afac3fe244",
    "definition_id": "ProofOfControlPresentationDefinition",
    "descriptor_map": [
      {
        "id": "proofOfIdentifierControlVP",
        "format": "jwt_vp",
        "path": "$.holder"
      }
    ]
  },
  "verifiableCredential": [],
  "holder": "did:key:z6MkjFFeDnzyKL7Q39aNs1piGo27b12upMf1MmSDQcABJmmn",
  "type": ["VerifiablePresentation", "CredentialApplication"]
}
####
Credential Fulfillment
[](#credential-fulfillment)
What follows is a JSON object containing the same contents as a Verifiable Presentation in JWT form; there is no proof object, because it would be signed and transmitted as a JWT.

JSON
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "type": ["VerifiablePresentation", "CredentialResponse"],
  "holder": "did:key:z6Mkgw8mPijYRa3TkHSYtQ4P7S2HGrcJBwzdgjeurqr9Luqb",
  "credential_fulfillment": {
    "id": "5f22f1ea-0441-4041-916b-2504a2a4075c",
    "manifest_id": "KYCAMLManifest",
    "descriptor_map": [
      {
        "id": "KYCAMLCredential",
        "format": "jwt_vc",
        "path": "$.verifiableCredential[0]"
      }
    ]
  },
  "verifiableCredential": [] // Credential would be found here, as a JWT, i.e. ["eyJhbG..."]
}
####
Decoded Credential
[](#decoded-credential)
The following represents the intermediate form of a JWT-encoded verifiable credential post-verification and post-decoding to restore the "credential" (i.e., combining fields from both the payload and the protected headers of the JWT token):

JSON
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    { "@vocab": "https://verite.id/identity/" }
  ],
  "type": ["VerifiableCredential", "KYCAMLCredential"],
  "credentialSubject": {
    "KYCAMLAttestation": {
      "type": "KYCAMLAttestation",
      "process": "https://verite.id/definitions/processes/kycaml/0.0.1/usa",
      "approvalDate": "2021-09-14T02:00:07.540Z"
    },
    "id": "did:key:z6Mkjo9pGYpv88SCYZW3ZT1dxrKYJrPf6u6hBeGexChJF4EN"
  },
  "issuer": {
    "id": "did:web:verite.id"
  },
  "credentialStatus": {
    "id": "http://192.168.1.16:3000/api/revocation/05c74310-4810-4ec4-8402-cee4c28dda91#94372",
    "type": "StatusList2021Entry",
    "statusPurpose": "revocation",
    "statusListIndex": "94372",
    "statusListCredential": "http://192.168.1.16:3000/api/revocation/05c74310-4810-4ec4-8402-cee4c28dda91"
  },
  "issuanceDate": "2021-09-14T02:00:07.000Z"
}
###
Presentation Exchange
[](#presentation-exchange)
####
QR Code
[](#qr-code-1)
Credential holders initiate credential exchange flows by scanning a QR code, provided by the verifier, with their credential wallet.

Sample:

JSON
{
  "challengeTokenUrl": "https://verite.id/..."
}
####
Presentation Request
[](#presentation-request)
JSON
{
    "id": "1308e77f-9ab0-4de7-97a8-ad2111b585bf",
    "type": "VerificationRequest",
    "from": "did:key:z6MkizuwMHiYpZrBAn64ZnbS2cz5og7iGqAa3nV3EuTj4aaZ",
    "created_time": "2021-09-14T20:19:32.655Z",
    "expires_time": "2021-10-14T20:19:32.655Z",
    "reply_url": "http://192.168.1.16:3000/api/verification/1308e77f-9ab0-4de7-97a8-ad2111b585bf/submission",
    "body": {
        "status_url": "http://192.168.1.16:3000/api/verification/1308e77f-9ab0-4de7-97a8-ad2111b585bf/callback",
        "challenge": "e0e52794-7889-451c-bb05-28d8cff9ed13",
        "presentation_definition": {
            "id": "KYCAMLPresentationDefinition",
            ...
        }
    }
}
Details:

- from: who the message is from; in this case, the issuer
- reply_url: the URL the wallet should send the credential submission to
- body.challenge: a challenge the wallet should sign when proving control, to prevent replays
- body.presentation_definition: this follows the DIF Presentation Definition spec
- body.status_url: url returning verification results when complete
####
Presentation Submission
[](#presentation-submission)
Note: In the Presentation Object that follows (a signed VP in JWT form), the verifiableCredential object contains one signed verifiable credentials. For clarity & exemplarity, a non-JWT expression in JSON-LD format is included, but the same credential, expressed as a JWT as it is in the rest of this guide, could be substituted for it in JWT format, i.e. verifiableCredential": ["eyJhbGc..."]. JSON-LD and JWT credentials could even be combined, if the relying party was expecting multiple VCs and processed both kinds, i.e. verifiableCredential": ["eyJhbGc...", {"@Context":..."proof":...}].

JSON
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "presentation_submission": {
    "id": "d885c76f-a908-401a-9e41-abbbeddfe886",
    "definition_id": "KYCAMLPresentationDefinition",
    "descriptor_map": [
      {
        "id": "KYCAMLCredential",
        "format": "jwt_vc",
        "path": "$.presentation.verifiableCredential[0]"
      }
    ]
  },
  "verifiableCredential": [
    {
      "@context": [
        "https://www.w3.org/2018/credentials/v1"
      ],
      "type": ["VerifiableCredential", "KYCAMLCredential"],
      "credentialSubject": {
        "id": "did:key:z6Mkjo9pGYpv88SCYZW3ZT1dxrKYJrPf6u6hBeGexChJF4EN",
        "KYCAMLAttestation": {
          "type": "KYCAMLAttestation",
          "process": "https://verite.id/definitions/processes/kycaml/0.0.1/usa",
          "approvalDate": "2021-09-14T02:00:07.540Z"
        }
      },
      "issuer": {
        "id": "did:web:verite.id"
      },
      "proof": {...}
    }

  ]
}
####
Response
[](#response)
JSON
{
  "status": "approved"
}
Updated 6 months ago


URL: https://developers.circle.com/verite/docs/benefits-use-cases
Title: Benefits & Use Cases
Description: undefined
Keywords: undefined

The decentralized protocols that Verite employs are general purpose and can support a broad number of use cases across healthcare, creative arts, entertainment, gaming, agriculture, education and other domains.

Verite’s specific focus, however, is to provide a powerful, interoperable, free, open path for identity to unlock new value while meeting compliance needs in crypto finance and the USDC ecosystem.

Verite's digital identity attestations enable such benefits as KYC compliance and lower DeFi collateral requirements without the need for participants to disclose personal data. The decentralized approach avoids risks resulting from silos of identity data and allows individuals and institutions to transact seamlessly over open networks.

The benefits and use cases include the following possibilities:

##
Individuals and Institutions
[](#individuals-and-institutions)
- Reducing centralized barriers to entry can make finance can more inclusive and enable more people to engage safely in the global economy
- Institutions can engage with the DeFi ecosystem with the comfort that their transactions meet regulatory requirements
- Individuals and institutions can decide how and when their identity information is shared
- Accredited investor credentials can unlock security token exchange and access to permissioned DeFi protocols
- Insurance credentials can unlock sophisticated DeFi and physical-world transactions
- Artists and other content creators can earn royalty splits mapped to their identities as content is distributed
- NFT provenance can be tracked when collectibles transition to new owners
##
The Crypto Ecosystem
[](#the-crypto-ecosystem)
- Marketplaces, exchanges, and smart contracts can validate customer identity without storing personal identity data
- Lending protocols can use decentralized credit and risk scoring to reduce collateralization requirements
- FX markets can become more efficient atop compliant open global networks
- Crypto wallets can become identity wallets and help meet regulatory obligations (including the [Travel Rule](/verite/docs/travel-rule-research) and its international permutations) when transacting without leaking sensitive personal information
##
Regulators
[](#regulators)
- Law enforcement can have a clearer path to identifying financial crime without hindering innovation and growth
Updated 5 months ago


URL: https://developers.circle.com/verite/docs/verification
Title: Verification
Description: undefined
Keywords: undefined

This tutorial will walk you through consuming and verifying verifiable credentials using the verite sdks. For additional context about the verification and credential exchange flow, see [Consuming Verifiable Credentials](/verite/docs/verifying-credentials).

##
Step 1: Expose Verification Requirements
[](#step-1-expose-verification-requirements)
The verifier first needs to inform credential holders of their input requirements. Verite uses the [Presentation Exchange (PEx)](https://identity.foundation/presentation-exchange/) approach for this purpose, in which verifiers express these requirements as a Presentation Definition.

Verite exposes library helpers that create a few predefined types of presentation definitions, for example a KYC verification offer:

JavaScript
import { buildKycVerificationOffer } from "verite"

const offer = buildKycVerificationOffer(
  uuidv4(),
  verifierDidKey.subject,
  "https://test.host/verify"
)
##
Step 2: Receive Presentation Submission
[](#step-2-receive-presentation-submission)
The verification offer wraps a presentation definition, which contains instructions for the credential holder to determine what sort of inputs the verifier requires.

The verification offer also informs the credential holder where to submit the response.

The credential holder uses the presentation definition to gather the required inputs, form a Presentation Submission, submit proof, and send it back to the verifier. The credential holder can take their verifiable presentation, extract the credential they'd like verified, and submit it to the verifier.

Verite exposes libraries to help with this. First, an example of extracting the verifiable credential from a verifiable presentation and decoding it:

JavaScript
import {
  verifyVerifiablePresentation,
  verifyVerifiableCredential
} from "verite"

const decoded = await verifyVerifiablePresentation(presentation)
const vc = decoded.verifiableCredential[0]
const decodedVc = await verifyVerifiableCredential(vc.proof.jwt)
Then, an example of building the presentation submission using the decoded verifiable credential:

JavaScript
import { composePresentationSubmission } from "verite"

const submission = await composePresentationSubmission(
  clientDidKey,
  offer.body.presentation_definition,
  decodedVc
)
##
Step 3: Verify Submission
[](#step-3-verify-submission)
The verifier verifies the submission and proceeds with the corresponding workflow, returning a status to the credential holder about whether the verification succeeded.

ts
await validateVerificationSubmission(
  submission,
  offer.body.presentation_definition
)
##
Summary
[](#summary)
Putting this together, we can do the following:

JavaScript
// 1. VERIFIER: Discovery of verification requirements
const offer = buildKycVerificationOffer(
  uuidv4(),
  verifierDidKey.controller,
  "https://test.host/verify"
)

// 2. CLIENT: Decode the verifiable credential from the presentation and create verification submission (wraps a presentation submission)
const decoded = await verifyVerifiablePresentation(presentation)
const vc = decoded.verifiableCredential[0]
const decodedVc = await verifyVerifiableCredential(vc.proof.jwt)

const submission = await composePresentationSubmission(
  clientDidKey,
  offer.body.presentation_definition,
  decodedVc
)

// 3. VERIFIER: Verifies submission
await validateVerificationSubmission(
  submission,
  offer.body.presentation_definition
)
You can view this demo as a full working example in our [demo-issuer](https://github.com/circlefin/verite/tree/main/packages/demo-verifier) demo.

Updated 6 months ago


URL: https://developers.circle.com/verite/docs/wallets-overview
Title: Overview
Description: undefined
Keywords: undefined

##
Landscape
[](#landscape)
In the world of cryptocurrency and web3, wallets are always walking a tightrope between adding new features and minimizing liabilities (security, maintenance, reputation). You want to incorporate features and interfaces that bring new value to your users (and more or more valuable users to your wallet); but you also need to avoid sticking your neck out and being the first (or only) wallet to introduce a new attack vector. You also have to balance engineering efforts and maintenance liabilities for any new feature-- it might start as a bespoke integration or experimental branch, but over time you are hoping the interfaces harmonize with others and standards emerge for shared security and reasonable debugging.

So where do verifiable credentials fit in? What is the reasonable engineering path for giving your users direct control over sensitive off-chain data and verifiable consent capabilities?

Some key questions to ask yourself:

- What use-cases do you want to start with? [Overview of use-cases](#use-cases)
- Do your users (or a reliably-gated subset of them) have ways to verifiably prove exclusive control? [Identity Assurance](#identity-assurance)
- What is your users' and user-cases' tolerance for extending the wallet? [Extensability and User Trust](#extensability-and-user-trust)
- Where does Verite sit in your more exotic or complex wallet configurations? [Architectural options](#architectural-options)
- Are you interested in supporting general-purpose Verifiable Credentials, and a Verite profile within that, or would you rather start small with ONLY Verite credentials? [Supporting Verite versus Supporting All Verifiable Credentials](#verite-support-versus-vc-support)
- Interested in starting even smaller? Some Verite products facilitate [Easy Integration Paths](#easy-integration-paths), with full SDKs coming soon.
##
Use-Cases
[](#use-cases)
In the short-term, the following use-cases are going live in at least one product offering in 2022:

- VC-based gating of KYB status and domicile (US y/n) for a "company wallet" (i.e., Compliant and Auditable Institutional DeFi)
- VC-based gating of Investor Accreditation for a "company wallet" (US-domiciled wallets)
The following are in research and design phase, being co-developed by participants and adopters:

- KYC'd individual wallet (custodial and self-custodial)
- Non-US KYB status (including interoperability with GLEIF and FincID credentialing)
- FATF-compliant reporting for custodial-to-custodial transactions (incl transactions involving 1 non-custodial wallet)
- Currency controls/FX reporting and CBDC use-cases
- Verifiable credit-assessment and forensic-sourcing
##
Identity Assurance
[](#identity-assurance)
Different use-cases have different requirements and risk tolerances for "identity assurance," i.e., the degree of certainty or verifiability that the wallet is being controlled by the expected and authorized entity (usually a human). If control of a wallet can be transfered as easily as a private key in plaintext or a recovery phrase, your wallet's identity assurance is probably inadequate to most Verite use-cases without including some kind of third-party identity-assurance supplement (e.g. a "liveness API" from an IDV business that has previously enrolled the controller of the wallet). Verite works best with wallets that support on-device biometric unlock and/or hardware signing as an authentication factor to minimize the risks associated with stolen, borrowed, rented, or sold wallets.

##
Extensability and User Trust
[](#extensability-and-user-trust)
Finding the simplest engineering path to meet the required non-technical requirements is often complicated by user experience, and specifically user tolerance for extending their software or altering their user experience. If your users are comfortable opting into an "experimental" branch of software, or using a "private build" of a provisioned/custom wallet, this is a lesser consideration; if they have to apply browser extensions or wallet plug-ins, this can drastically change the business calculus. For this reason, Verite is working on multiple form-factors and integration points in parallel, to give wallets multiple options.

##
Architectural Options
[](#architectural-options)
In today's crypto space, wallets are not homogeneous; the division of concerns between cryptography, [confidential per-user] storage, multi-device sync, and key custody can lead to complex arrangements of outsourcing and white-labeling. A solution that only works for monadic wallets would not have legs in 2022, let alone 2025.

Verite's handling of verifiable credentials is best understood as a User-Experience capability, since wallet users have to understand the redirections and reliance between parties that leads them to "accept credentials" into their wallet and consent to their sharing with various front-ends (and, some day, directly with on-chain entities).

###
Storage of Verifiable Credentials
[](#storage-of-verifiable-credentials)
The Verite standards strive to remain maximally unopinionated and flexible about storage, as wallets today hardly have a standardized way of handling file storage in-wallet or in vaults. We have prototyped both a "split wallet" design, where verifiable credentials and user consent to shared are handled by an entirely separate piece of software (i.e., a device-specific browser-extension for conveying identity data) and a "minimum viable hybrid wallet", a cryptocurrency wallet which only handles Verite credentials in their simplest form in instance/device-specific local storage. Storage-supplemental dapps and PWAs are also under prototyping efforts in parallel, to allow an unmodified, traditional Wallet-Connect wallet to spin up a cross-device "vault" for verifiable credentials, with delegated signing rights over its contents for cleaner audit trails. We look forward to publishing the results of this research as replicable and even standardizable primitives soon.

###
Key Custody
[](#key-custody)
Similarly, we have worked hard to keep direct access to keys optional to allow the kinds of custodial arrangements, including multi-party-computation and multi-signatory wallet architectures that are increasingly central to institutional handling of digital assets and complex custody/key-management flows. This means that wallets with supplemental hardware for signing or wallets utilizing multi-signature or multiparty-computation circuits are at no disadvantage. Over time, we believe the growing complexity abstracted by the concept of a wallet may well be the tailwind needed to standardize on wallet identity at a higher layer, e.g. with Decentralized Identifiers.

##
Verite-Support-versus-VC-Support
[](#verite-support-versus-vc-support)
Verite could be the first you're hearing about Verifiable Credentials, or Verite could be one use-case in a broader Verifiable Credential strategy you've already been researching. Verite implementers to date have fallen at various points on the spectrum between these two poles, so we try to stay honest about the complexity of incrementalizing these efforts.

For implementers interested primarily or exclusively in Verite, we generally advise a "minimum viable complexity" approach: if you are targeting one or two use cases as an end unto itself, you can greatly simplify the implementation by thinking of Verifiable Credentials as a JWT you get from one domain and send to another with user consent. Much of the complexity of filtering and handling VCs differently by type can be hard-coded; much of the complexity of validating issuers can be outsourced to commercial or public verifiers, which allow this to parameterized and rely on registries of trusted issuers.

At the other end of the spectrum, it could be argued that implementing VCs today requires something like a "choice of stack". Verifiable Credential tooling is still in a somewhat early phase of its adoption; while there are ways to cross-verify and translate between different "flavors" of Verifiable Credentials, most open-source implementations and tools available today have one or two "native" verifiable credential encoding forms and secondary support for others. That said, Verite is built to what we believe to be the more promising and backwards-compatible stacks, all of which is open-source and most of which is governed and iterated through the [Decentralized Identity Foundation](https://identity.foundation/). Wallets that handle JWT Verifiable Credentials with JSON Schema semantics get access to many emerging ecosystems and open platforms like Microsoft's Entra credentialing platform, the "Web5" efforts led by Block, employment credentials from Workday and Affinidi, etc. Open-source tooling from Spruce Systems, TBD, Transmute, and ConsenSys are good places to start if you are interested in general-purpose Verifiable Credential tooling that can be easily configured to handle Verite Credentials and turnkey revocation/status publication infrastructure.

##
Easy Integration Paths
[](#easy-integration-paths)
At present, the two main options to consider are whether you want to attest to the controller of an blockchain address or to the controller of a specific wallet, which may control multiple addresses in addition to a DID (wallet identifier). For more information, read the [identifier scheme considerations](/verite/docs/identifier-methods#wallet-based-versus-address-based-holder-identification-schemes) and compare the [address-bound credential exchange flow](/verite/docs/verifying-credentials#address-bound-verification-flow) and the [wallet-bound credential exchange flow](/verite/docs/verifying-credentials#wallet-bound-verification-flow).

To make this more concrete, we have provided examples of simple and compound address-bound credentials. Click here for an example of wallet-bound credential, meant to be issued to and "presented" verifiably by a DID-capable wallet.

##
Liability and Auditing Considerations
[](#liability-and-auditing-considerations)
Wallets do not hold outsized responsibility in Verite, thanks to the tamper-proofing inherent in Verifiable Credentials (integrity checks can be performed at issuance and again at presentation using standard JWT tooling, since Verite VCs are simply garden-variety JWT tokens).

Few Verifiable Credentials use-cases necessitate logging of VC events. Depending on the credential-type, though, composite wallets (i.e., wallet integrating both crypto capabilities and identity/credential-management features) may want to log issuance events, particularly for credentials with a short shelf-life that need to be periodically reissued; more generally, they may want to filter verifiable credentials and handle them differently by type and/or by issuer.

Composite wallets, on the other hand, may well need more involved logging to vouchsafe their internal separation of concerns. For example, if a crypto wallet delegates identity functions or signing rights to a dapp, both dapp and wallet should log the delegation event in a replayable compact form (our lightweight implementation models this in the form of CACAO receipts). Similarly, if verifiable credentials are stored anywhere outside the controlling wallet proper, this delegation (and storage events and perhaps even retrieval events over which the wallet has signed) should be logged.

Updated 6 months ago


URL: https://developers.circle.com/verite/docs/example-address-token
Title: Example Address Token
Description: undefined
Keywords: undefined

##
Example Address-Bound JWT
[](#example-address-bound-jwt)
In practice, a wallet that wants to receive and share the simpler address-bound VCs simply needs to be able to handle (and verify signatures on) JWTs that look like this:

JSON
{
  "sub": "did:pkh:eip155:1:0xb9ff5450db13a154d3cc40eb57a619e59bb7d8b9",
  "nbf": 1660857957,
  "iss": "did:web:assets.circle.com",
  "exp": 1663449957,
  "vc": {
    "@context": [
      "https://www.w3.org/2018/credentials/v1",
      "https://verite.id/identity"
    ],
    "type": [
      "VerifiableCredential",
      "KYBPAMLAttestation",
      "EntityAccInvAttestation"
    ],
    "id": "d49b865e-86f1-4954-a2c3-b94f206ca668",
    "credentialSubject": [{
        "type": "EntityAccInvAttestation",
        "approvalDate": "2022-08-18T21:25:57.285Z",
        "process": "https://verite.id/definitions/processes/kycaml/0.0.1/generic--usa-entity-accinv-all-checks",
        "type": "EntityAccInvAttestation"
    },{
        "type": "KYBPAMLAttestation",
        "approvalDate": "2022-08-18T21:25:57.285Z",
        "process": "https://verite.id/definitions/processes/kycaml/0.0.1/generic--usa-legal_person",
        "type": "KYBPAMLAttestation"
    }],
    "credentialStatus": {
      "id": "https://issuer-smokebox.circle.com/api/v1/issuance/status/0#1161",
      "type": "StatusList2021Entry",
      "statusPurpose": "revocation",
      "statusListIndex": "1161",
      "statusListCredential": "https://issuer-smokebox.circle.com/api/v1/issuance/status/0"
    },
  }
}
##
Example Address-Bound Verifiable Credential (Decoded)
[](#example-address-bound-verifiable-credential-decoded)
The mandatory properties of a JWT that are redundant to mandatory properties in the Verifiable Credentials data model get converted deterministically (and can, of course, be round-tripped). Note that sub gets moved into the id property inside [each] credentialSubject object rather than staying at the top level.

JSON
{
  "issuer": "did:web:assets.circle.com",
  "issuanceDate": "2022-08-18T23:25:57Z",
  "expirationDate": "2022-09-17T23:25:57Z",
  "vc": {
    "@context": [
      "https://www.w3.org/2018/credentials/v1",
      "https://verite.id/identity"
    ],
    "type": [
      "VerifiableCredential",
      "KYBPAMLAttestation",
      "EntityAccInvAttestation"
    ],
    "id": "d49b865e-86f1-4954-a2c3-b94f206ca668",
    "credentialSubject": [{
        "id": "did:pkh:eip155:1:0xb9ff5450db13a154d3cc40eb57a619e59bb7d8b9",
        "type": "EntityAccInvAttestation",
        "approvalDate": "2022-08-18T21:25:57.285Z",
        "process": "https://verite.id/definitions/processes/kycaml/0.0.1/generic--usa-entity-accinv-all-checks",
        "type": "EntityAccInvAttestation"
    },{
        "id": "did:pkh:eip155:1:0xb9ff5450db13a154d3cc40eb57a619e59bb7d8b9",
        "type": "KYBPAMLAttestation",
        "approvalDate": "2022-08-18T21:25:57.285Z",
        "process": "https://verite.id/definitions/processes/kycaml/0.0.1/generic--usa-legal_person",
        "type": "KYBPAMLAttestation"
    }],
    "credentialStatus": {
      "id": "https://issuer-smokebox.circle.com/api/v1/issuance/status/0#1161",
      "type": "StatusList2021Entry",
      "statusPurpose": "revocation",
      "statusListIndex": "1161",
      "statusListCredential": "https://issuer-smokebox.circle.com/api/v1/issuance/status/0"
    }
  }
}
##
Example Address-Bound Verifiable Credential (Annotated)
[](#example-address-bound-verifiable-credential-annotated)
JSON
{
  "issuer": "did:web:assets.circle.com",
We recommend handling this property carefully in your UX, since users generally know more about "Circle.com" than they understand anything else you could display to them about a credential. See below on how to fetch and derive the public key needed for verifying signatures on the JWT from this string.

JSON
  "issuanceDate": "2022-08-18T23:25:57Z",
  "expirationDate": "2022-09-17T23:25:57Z",
It's up to wallet how to display or handle expired credentials, but an expired credential can still be valid (if the public key fetched from the issuer still verifies the signature). We recommend hiding expired credentials by default but allowing them to be found with toggles or settings.

JSON
  "vc": {
    "@context": [
      "https://www.w3.org/2018/credentials/v1",
      "https://verite.id/identity"
    ],
    "type": [
      "VerifiableCredential",
      "KYBPAMLAttestation",
      "EntityAccInvAttestation"
    ],
In many use-cases, a token actually contains multiple credential/claim types, each expressed as a distinct (and fully-formed) credentialSubject object with a type property in this list. The semantics for these types is explained in [this blog post](/verite/docs/crossfunctionality).

JSON
    "id": "d49b865e-86f1-4954-a2c3-b94f206ca668",
Verite mandates a unique identifier for each credential to facilitate cross-organization (and even forensic) audit trails. We recommend a standard UUID, which can simplify how wallets handle multiple such tokens.

JSON
    "credentialSubject": [{
Note that multiple credentials are put into the VC as multiple co-equal credentialSubject objects-- each with its own id and type. See the [VC data model spec](https://www.w3.org/TR/vc-data-model/#credential-subject) for guidance.

JSON
      "id": "did:pkh:eip155:1:0xb9ff5450db13a154d3cc40eb57a619e59bb7d8b9",
Note that an explanatory prefix before the address, specified in the cross-chain [did:pkh](https://github.com/w3c-ccg/did-pkh/blob/main/did-pkh-method-draft.md#examples) specification based on the [CAIP-10](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-10.md#test-cases) address URN scheme, shows this to be an Ethereum (eip155) Mainnet (1) address. This same convention is used for CACAO receipts of offchain signature-based authentication event, i.e. "web3 wallet connections".

JSON
        "type": "EntityAccInvAttestation",
        "approvalDate": "2022-08-18T21:25:57.285Z",
        "process": "https://verite.id/definitions/processes/kycaml/0.0.1/generic--usa-entity-accinv-all-checks",
        "type": "EntityAccInvAttestation"
    },{
        "type": "KYBPAMLAttestation",
        "approvalDate": "2022-08-18T21:25:57.285Z",
        "process": "https://verite.id/definitions/processes/kycaml/0.0.1/generic--usa-legal_person",
        "type": "KYBPAMLAttestation"
    }],
    "credentialStatus": {
      "id": "https://issuer-smokebox.circle.com/api/v1/issuance/status/0#1161",
This URL can simplify on-going verification-- rather than ask the wallet to re-present this credential at each future transaction or event, a verifier can simply re-check this URL at each transaction taking place between the first verification and the expiration of the credential.

JSON
      "type": "StatusList2021Entry",
      "statusPurpose": "revocation",
      "statusListIndex": "1161",
      "statusListCredential": "https://issuer-smokebox.circle.com/api/v1/issuance/status/0"
    }
  }
}
##
Verifying a did:web Credential
[](#verifying-a-didweb-credential)
The did:web: prefix lets you know you're dealing with a DNS-based DID publication mechanism conformant with the [DID-Web Method Specification](https://w3c-ccg.github.io/did-method-web/). The rest of the iss/issuer string after the prefix provides a domain (and, optionally, a query string), the controller of which is also the controller and subject of a DID Document. At any given time, you can get the current DID document (containing a public key for verifying signatures) from {the domain}/.well-known/did.json. In the example above, that means [https://assets.circle.com/.well-known/did.json](https://assets.circle.com/.well-known/did.json), which you can confirm in your browser.

Updated 6 months ago


URL: https://developers.circle.com/verite/docs/verite-service
Title: Verite Verification Service
Description: undefined
Keywords: undefined

#
Getting Started
[](#getting-started)
[Verite](/verite/docs/verite-protocol-introduction) is an open framework for proving identity claims in Web3 without exposing sensitive personal information. Web apps, mobile apps and smart contracts get the benefit of identity-verified participants from the simple check of a Verite credential.

Our program is in Early Access right now. If you would like to integrate your dApp, Wallet or Issuer with the Circle Verification APIs. Please fill out the [contact us form](https://www.circle.com/en/verite#contact-us-form).

##
How Verite Works
[](#how-verite-works)
The overall Verite flow contains both issuance and verification. Users (wallets) acquire verifiable credentials (VC) in the issuance flow and verify the VC in the verification flow before engaging DeFi transactions.


Verite Flow

Updated 6 months ago


URL: https://developers.circle.com/verite/docs/verite-protocol-introduction
Title: Introduction
Description: undefined
Keywords: undefined

##
Decentralized Identity for the Crypto Economy
[](#decentralized-identity-for-the-crypto-economy)
Verite presents an open, shared, interoperable architecture for identity and trust in the global crypto financial ecosystem. It unites several identity standards to enable scalable, reliable, privacy-preserving ways to leverage identity and to ensure regulatory compliance when engaging in blockchain and smart contract activity.

Technical barriers have challenged progress on identity protocols throughout the history of the internet, but these barriers have diminished through technical advancements in cryptography and blockchains which deliver new trust and transaction architectures. Verite's collection of decentralized identity protocols have become possible and practical.

Misaligned incentives and entrenched business models have also hindered progress on identity standards. But now strong economic incentives exist: powerful new markets built on blockchains enable everyone around the world to connect, transact, invest, and generally engage in a shared global economy. But unlike the internet of content, global finance is heavily regulated to protect consumers and prevent financial crime, and any identity model must also enable individuals and institutions to comply accordingly.

These three forces -- new technologies for cryptographic trust and transactions, high-value market opportunities and the prospect of increased global financial inclusion, and the need for identity solutions to mitigate risk and meet regulatory compliance requirements -- accelerate progress toward decentralized identity standards for the next era of internet applications.

##
Identity Problems
[](#identity-problems)
Web identity models typically involve centralized services and silos in which private identity data is tracked, often bought and sold, breached or otherwise exposed, and generally managed outside of the control of the individuals who own those identities.

Such centralized web identity models also prove a particularly poor fit for decentralized blockchains, which to varying degrees are designed to enable trust while avoiding sensitive personal data exposure.

But the absence of an identity model poses its own problems and stunts the growth of crypto finance: it prevents broader commercial and institutional adoption, blocks key use cases and market opportunities, and risks legal non-compliance. Addressing these issues requires a new privacy-preserving decentralized identity model.

##
The Verite Solution
[](#the-verite-solution)
Verite is a set of free open source decentralized identity protocols and data models to facilitate interoperability between Verite products present and future. These protocols allow people and institutions to cryptographically prove claims about their identities, and allow services to attest to those claims, while avoiding exposure of sensitive data. For example, an individual might prove KYC, credit, insurance, accredited investor status, or similar identity claims to an application or smart contract while preserving privacy.

Unlike centralized identity architectures, Verite's decentralized approach enables people and institutions to control how their identity data is accessed and avoids the sensitivity of identity data being used to justify vendor lock-in or "toll-roads". Verite is not a token and requires no specific blockchain, but functions across many major blockchain and wallet ecosystems, never storing ANY sensitive or de-anonymizing data on-chain.

Verite empowers developers, financial institutions, regulators, and individuals with an open, free, interoperable identity model for decentralized finance. Interoperability ensures that applications, services, wallets, and smart contracts can interact with one another following a shared set of standard rules and interfaces.

##
How to Use this Site
[](#how-to-use-this-site)
The following articles, tutorials, and accompanying source code are illustrative and educational, primarily intended for review by developers of Verite-compatible and/or Verite-reliant systems. Because verifiable credentials are not yet a standard or uniform component of crypto wallets and other web3 identity systems, there is extra attention and explanation given to integrating those capabilities into various contemporary architectures and data flows. See, in particular, the "Entry-Points" section for high-level overviews tailored to dapps looking to solve their identity problems by relying on Verite products, "identity providers" (knowers of their customers) looking to add value to their customers with Verite portability, and crypto wallets looking to connect the two.

Updated 5 months ago


URL: https://developers.circle.com/verite/docs/verifying-credentials
Title: Verifying Credentials
Description: undefined
Keywords: undefined

As users collect more credentials, having to manually manage them (figuring out which are current, and which are appropriate for a given relying party) becomes infeasible. Unlike unstructured credentials (e.g., many .pdf files), which would require a user to manually select which data to share, verifiable credentials lend themselves to credential exchange protocols that are easier for end-users. These protocols are typically implemented for the user through the user's wallet (or other software agents) interacting with the requesting party.

Verite uses the concepts and data models from DIF's [Presentation Exchange](https://identity.foundation/presentation-exchange) for this purpose. This document describes how a consumer of Verifiable Credentials, referred to as a verifier or relying party, informs users what types/formats of credentials they accept, and how the user's wallet/agent uses this information to select the appropriate credentials and respond. Note that what follows assumes a wallet that controls a fully-featured DID like did:ion rather than a crypto-wallet; the latter has a slightly different trust model and identity-binding model, which leads to different requirements for Verifiable Presentation (i.e. proving liveness and consent at time of credential exchange).

##
Presentation Requests and Definitions
[](#presentation-requests-and-definitions)
A [presentation definition](https://identity.foundation/presentation-exchange/#presentation-definition) is the way a relying party describes the inputs it requires, proof formats, etc. A [presentation request](https://identity.foundation/presentation-exchange/#presentation-request) is a generic term for a transport conveying this. It's meant to be flexibly embedded in a variety of transports, such as OIDC or [WACI](https://identity.foundation/waci-presentation-exchange/). Verite uses a JSON object that somewhat resembles the schema defined by WACI, but with additional fields including a challenge and reply URL.

##
Wallet Interactions
[](#wallet-interactions)
Assuming a mobile wallet stores the credentials, for the convenience of the user a verifier may initiate the process of sending the presentation request either by scanning a QR code (desktop) or a deep-link (mobile). Due to size limitations of a QR code, wallet and credential interactions often do not include the full presentation request in the QR code; instead the QR code encodes an endpoint with a unique URL. The wallet decodes the QR code, subsequently retrieving the presentation request from that endpoint.

[See example Verite Presentation Request](/verite/docs/message-formats-samples#presentation-submission)

##
Credential Submission
[](#credential-submission)
The wallet parses the Presentation Definition object to determine what types of inputs, proofs, and formats the verifier requires. The wallet displays a summary of the information requested to the wallet holder, asking for approval and/or asking the user to select the desired credential(s) from the set of matches. On confirmation, the wallet gathers the credentials and creates a verifiable presentation containing the credential and signs the presentation with the credential subject’s private key. It embeds the VP in a [presentation submission](/verite/docs/message-formats-samples#presentation-submission), and signs it along with the challenge to provide [proof of identifier control](https://identity.foundation/presentation-exchange/#proof-of-identifier-control).

Finally, the wallet sends the packaged credential to the reply_url contained in the presentation request.

[See example Verite presentation submission](/verite/docs/message-formats-samples#presentation-submission).

##
Verification
[](#verification)
The verifier receives the presentation submission, unwraps it, and maps the presentation to the original presentation request. Mapping the submission to the original request can be done in many ways. The Verite demos use a JWT in the reply_url to store the mapping. Next, the verifier verifies the submitted contents against the required inputs, ensures its signed by the subject's keys, and checking the credential's status to determine if it is revoked out not.

Verification cannot always occur immediately. In these cases, the presentation request has an optional status_url that can be used to check its status.

There is no required output or side-effect of verification. However, we have a pattern for [integrating with Ethereum](/verite/docs/smart-contract-patterns) that models how an on-chain Verification Registry can capture verification events in a way that's easy to consume on-chain. A web app that is not constrained by on-chain data availability, however, might simply update its state and allow the user to continue some action.

##
Verification Flow
[](#verification-flow)
At a high level, the verification flow for an identity wallet looks like this:


However, when this maps to an on-chain use-case, where Dapps are requiring off-chain documentation to trust a crypto address, this flow can get a lot more complicated depending on the types of credentials and the identity-proofing they require.

In what follows, we will zoom in one two very different applications of this high-level flow that come from two different architectures: first, we will break down the flow for credentials issued to a user-controlled "identity wallet", then, we will break down the flow for address-specific credentials (signed over by only a crypto-currency wallet), showing the differences in both procedure and trust model.

###
Wallet-Bound Verification Flow
[](#wallet-bound-verification-flow)
In this example, a user wants to verify credentials issued to, and stored in a mobile app that function as an "identity wallet," i.e., a wallet controlling a DID for identity applications rather than a "cryptocurrency wallet" controlling a keypair for onchain/payment purposes. (Note, some wallets combine both sets of capabilities, but for simplicity's sake, our sample implementation treats them as distinct and independent applications.)

Some things to note:

- The QR code is provided as a way of connecting a browser-based dApp interaction with a mobile-based identity wallet; different architectures (i.e., browser-based identity wallets without the cross-device requirement) can use different mechanisms to bootstrap the wallet-verifier relationship.

- Verifier prompts user for the Ethereum address the Verification Record will be bound to
- User provides their Ethereum address (e.g. copy pasting, or by connecting a blockchain wallet)
- Verifier generates a JWT that encodes the user's address, that will later be used to generate the URL the mobile identity wallet will submit to.
- Verifier shows QR Code
- User scans QR Code with their mobile identity wallet.
- Identity wallet parses the QR code, which encodes a JSON object with a challengeTokenUrl property.
- Wallet performs a GET request at that URL to return a Verification Offer, a wrapper around a [Presentation Request](https://identity.foundation/presentation-exchange/#presentation-request), with three supplementary properties:
- A unique identifier (such as a UUID) or other logging metadata.
- The verifier's unique identifier, i.e. it's "DID" (including offchain DIDs like did:pkh, did:web or did:key)
- A URL for the wallet to submit the [Presentation Submission](https://identity.foundation/presentation-exchange/#presentation-submission), using the unique JWT generated earlier.
- The identity wallet prompts the user to select credential(s) from the set of matches.
- Identity wallet prepares a [Presentation Submission](https://identity.foundation/presentation-exchange/#presentation-submission) including:
- The wallet's DID, control of which is proven by a signature on the submission object by that DID's private key. In the Verite examples, the holder's DID must match the credentialSubject.ids of the presented VCs, thus verifying both liveness and control of the identifier against which the credentials were issued.
- Any Verifiable Credential(s) necessary to complete verification.
- A fresh signature over the above (and the challenge/nonce provided by the verifier).
- Wallet submits the Presentation Submission to the URL found in the Verification Offer (reply_url property).
- The Verifier validates all the inputs
- Verifiers generates a Verification Record and adds it to the registry or sends it directly to a waiting relying party
###
Address-Bound Verification Flow
[](#address-bound-verification-flow)
In this example, a user wants to verify address-bound credentials issued to a blockchain address. Since these credentials simplify the trust model, they only need a fresh, live signature from the [cryptocurrency] wallet's private key to authenticate the session in which those credentials are presented, rather than a signature over the credential themselves. The credentials do not necessarily need to be stored in an identity wallet or even in a crypto wallet, since the crypto wallet will need to be authenticated to present them and they are already tamper-proofed.


-
Crypto Wallet requests transaction or resource requiring verification.

-
Relying Party (e.g. Dapp) calls Verifier with wallet address to be verified.

-
Verifier returns [Presentation Request](https://identity.foundation/presentation-exchange/#presentation-request) (no additional properties required).

-
Verifier prompts user to connect the wallet controlling the blockcain address that the Verification Record will be bound to.

-
Wallet signs an offchain transaction to authenticate wallet and authorize a web3 session. For interoperability and auditing purposes, we recommend a [EIP-4361](https://eips.ethereum.org/EIPS/eip-4361#example-message)-conformant "Sign-In With Ethereum" message or a [CAIP-122](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-122.md)-conformant equivalent.

-
Verifier sends its [Presentation Request](https://identity.foundation/presentation-exchange/#presentation-request) to wallet, or equivalent bespoke RPC calls describing the acceptable credentials.

-
The Wallet checks its storage for one or more matching credentials. If more than one is present, a user selection step should be triggered; if none, a redirect or informative message displayed. If exactly one, consent step may be optional, depending on use-case.

-
Wallet prepares a [Presentation Submission](https://identity.foundation/presentation-exchange/#presentation-submission), or if needed, the Verifier can assemble it on behalf of the wallet; this submission object includes:

- Any Verifiable Credential(s) necessary to complete verification.
- Wallet key--or ephemeral session key--signs presentation submission object (with challenge) to create verifiable and replayable event for logging and audit purposes
- If session key rather than wallet key signed the submission, and/or if any additional (on-chain or off-chain) software was involved in the storing, fetching, and passing of the VC(s), then a [CACAO](https://github.com/ChainAgnostic/CAIPs/blob/c8d8ee203625ea622bd15c42b2493116712dfaf3/CAIPs/caip-74.md) receipt of sign-in message needs to be included for the submission to be functionally equivalent to a submission from a monadic DID wallet. Note: a [CACAO](https://github.com/ChainAgnostic/CAIPs/blob/c8d8ee203625ea622bd15c42b2493116712dfaf3/CAIPs/caip-74.md) receipt contains a replayable authorization of a session key and any other resource expressable as a URI.
-
Wallet or Verifier submits the Presentation Submission to the URL found in the Verification Offer (reply_url property).

-
The Verifier validates all the inputs

-
Verifiers generates a Verification Record and adds it to the registry or sends it directly to the relying party which is awaiting a record for that wallet address (see step 1). Transaction or resource request may be attached as well to process, per use case.

Updated 5 months ago


URL: https://developers.circle.com/verite/docs/tutorial-creating-a-registry-contract-in-solidity
Title: Tutorial: Creating a Registry Contract in Solidity
Description: undefined
Keywords: undefined

When using the [smart contract pattern](/verite/docs/smart-contract-patterns) for verifications, you can use any blockchain you would like. For the sake of this guide, we will focus on [Solidity](https://docs.soliditylang.org/en/v0.8.11/), which is the programming language for the Ethereum Virtual Machine.

##
Setup
[](#setup)
For this tutorial, we will use Hardhat. Hardhat provides tooling to set up your development environment, test contract code, deploy contracts, and more. [Follow this guide to get started with Hardhat](https://hardhat.org/tutorial/setting-up-the-environment.html).

Once your Hardhat project is set up, it's time to create our registry contract. In the contracts folder.

##
Creating The Contract
[](#creating-the-contract)
In the contracts folder, create a new file called VerificationRegistry.sol. In that file, we want to specify the version of Solidity we're using. We're going to be using OpenZeppelin contracts soon, so we need to make sure our contract uses the same Solidity version as OpenZeppelin, which is currently 0.8.0. A the top of your file add:

sol
pragma solidity ^0.8.0;
Now, before we move on, let's install [OpenZeppelin's library of contracts](https://openzeppelin.com/). This will allow us to pull in dependent contracts that are safe and audited without us having to write them ourselves. To install, run the following:

npm install @openzeppelin/contracts

Once the dependencies are installed, we can import the appropriate libraries into our contract. Let's take a second to understand what those libraries are.

The first library we will import into our contract is the Ownable library. This one is designed to make it easier to lock function calls down to the owner of the contract.

The next library we will import is the ECDSA library. This library is used to help us decode signed and hashed data.

Finally, we will import the EIP712 library. This is the most important library in our contract since it is foundational to how we will verify credentials. For more on this, be sure to reference the [Smart Contract Patterns section](/verite/docs/smart-contract-patterns).

To import these libraries, add the following below the Solidity version in our contract:

sol
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
import "@openzeppelin/contracts/utils/cryptography/draft-EIP712.sol";
There's one additional library we want to import. This one is an interface library that allows us to more easily map the types necessary in our contract variables and functions. For brevity, we're not going to write the interface itself. We'll just copy it from the existing example from Circle. You can [grab a copy of the interface library contract here](https://github.com/circlefin/verite/blob/main/packages/contract/contracts/IVerificationRegistry.sol).

Create a new contract file in your contracts folder and call it IVerificationRegistry.sol. Paste the contents of the example file linked above into that contract, then return to your VerificationRegistry.sol file.

Add one more import to the Verification Registry contract:

sol
import "./IVerificationRegistry.sol";
Now, we're ready to write the contract!

#
State Variables
[](#state-variables)
Our Verification Registry requires some contract state. If you remember from the Smart Contract Patterns part of the documentation, this means either a verifier or a subject will have to pay gas to update the state of the contract when a new verification is added to the registry.

Let's go ahead and start creating the contract and add in some variables. First, we need to define the contract itself. We do that like so:

sol
contract VerificationRegistry is Ownable, EIP712("VerificationRegistry", "1.0"), IVerificationRegistry {

}
There are a couple of interesting things going on already. First, we are using the EIP712 library to help us with signature validation and hashing. When we use that library, it requires two parameters: a name and a version.

Next, we are extending our contract to use the types defined in our interface contract.

Ok, now let's define some variables. We'll drop them all below and then we'll talk through each.

sol
// Verifier addresses mapped to metadata (VerifierInfo) about the Verifiers.
mapping(address => VerifierInfo) private _verifiers;

// Verifier signing keys mapped to verifier addresses
mapping(address => address) private _signers;

// Total number of active registered verifiers
uint256 _verifierCount;

// All verification records keyed by their uuids
mapping(bytes32 => VerificationRecord) private _verifications;

// Verifications mapped to subject addresses (those who receive verifications)
mapping(address => bytes32[]) private _verificationsForSubject;

// Verifications issued by a given trusted verifier (those who execute verifications)
mapping(address => bytes32[]) private _verificationsForVerifier;

// Total verifications registered (mapping keys not being enumerable, countable, etc)
uint256 private _verificationRecordCount;
The comments above each variable help explain what their purpose is, but we'll walk through it here as well.

The _verifiers variable is a mapping of information tied to verifiers. If we look at the IVerificationRegistry.sol file, we can see the VerifierInfo type looks like this:

sol
struct VerifierInfo {
    bytes32 name;
    string did;
    string url;
    address signer;
}
We won't copy over every type into this tutorial, but this shows how you can see the shape of the expected data for various variables.

The next variable we have is _signers. This is a mapping of the keys verifiers use to sign the verification records. It's how the Verification Registry contract can validate the verifier is who they say they are.

Next, we have a simple count to help track the number of verifiers: _verifierCount.

sol
uint256 _verifierCount;
We, of course, have to track the actual verifications as well. That is done in the mapping called _verifications.

sol
mapping(bytes32 => VerificationRecord) private _verifications;
In this example, we are mapping the verification identifier (a UUID) to the verification record. You may want to customize this mapping to your specification, but UUIDs are relatively collision resistant and tend to be good unique identifiers for things like this.

Next, we have the mapping of verifications for a specific subject:

sol
mapping(address => bytes32[]) private _verificationsForSubject;
This mapping requires the subject's wallet address and then maps it to an array of UUIDs.

Similarly, we track the verifications for verifiers with:

sol
mapping(address => bytes32[]) private _verificationsForVerifier;
And finally, we have a simple count for total verifications:

sol
uint256 private _verificationRecordCount;
Of course, the contract is nothing if it's just a bunch of state variables. Let's dive into writing the functions that will define the contract.

#
Contract Functions
[](#contract-functions)
The first function we'll define is to add a verifier. Without a verifier, the registry doesn't work. This function should look like this:

sol
function addVerifier(address verifierAddress, VerifierInfo memory verifierInfo) external override onlyOwner {
    require(_verifiers[verifierAddress].name == 0, "VerificationRegistry: Verifier Address Exists");
    _verifiers[verifierAddress] = verifierInfo;
    _signers[verifierInfo.signer] = verifierAddress;
    _verifierCount++;
    emit VerifierAdded(verifierAddress, verifierInfo);
}
Many of the functions on this contract can only be called by the owner of the contract (the address that deployed it). This function is no exception. The onlyOwner modifier uses the OpenZeppelin Ownable library to add a check that ensures the function is being called by the contract owner.

The function takes a verifier's address and the verifier information structured as defined by the VerifierInfo type.

You'll notice the emit at the end of the function. We don't have any events defined on the VerificationRegistry.sol contract, but the example interface contract does. This is completely optional, but emitting events is a nice way to allow others to listen to those events and take some action.

The next function is a simple one that is available to anyone to call. It takes a wallet address and returns a boolean indicating whether the address is a verifier or not.

sol
function isVerifier(address account) external override view returns (bool) {
    return _verifiers[account].name != 0;
}
The next function is equally simple. It is also callable by anyone, not just the contract owner. It returns the total number of registered verifiers:

sol
function getVerifierCount() external override view returns(uint) {
    return _verifierCount;
}
We will also want to get information about specific verifiers—not just their wallet address, but their full VerifierInfo record. To do that, we can create the following function:

sol
function getVerifier(address verifierAddress) external override view returns (VerifierInfo memory) {
    require(_verifiers[verifierAddress].name != 0, "VerificationRegistry: Unknown Verifier Address");
    return _verifiers[verifierAddress];
}
This function is another read-only function, and it can be called by anyone. It takes the verifier's wallet address and returns the full verifier info record.

Verifier info can change, so we need a function to update that information. This function can only be called by the contract owner, but it allows for such updates:

sol
function updateVerifier(address verifierAddress, VerifierInfo memory verifierInfo) external override onlyOwner {
    require(_verifiers[verifierAddress].name != 0, "VerificationRegistry: Unknown Verifier Address");
    _verifiers[verifierAddress] = verifierInfo;
    _signers[verifierInfo.signer] = verifierAddress;
    emit VerifierUpdated(verifierAddress, verifierInfo);
}
This function is a complete replacement, so even if only part of the VerifierInfo record is being updated, a whole new record has to be passed into the function.

This function also emits an event called VerifierUpdated that anyone can listen for.

As you might expect, we also need to be able to remove verifiers. Again, only the contract owner can call this function.

sol
function removeVerifier(address verifierAddress) external override onlyOwner {
    require(_verifiers[verifierAddress].name != 0, "VerificationRegistry: Verifier Address Does Not Exist");
    delete _signers[_verifiers[verifierAddress].signer];
    delete _verifiers[verifierAddress];
    _verifierCount--;
    emit VerifierRemoved(verifierAddress);
}
This function takes the wallet address for the verifier and removes that verifier from the _verifiers mapping. It removes the signing address for the verifier and reduces the total _verifierCount.

We now move into some of the actual verification logic with our functions. The first function is a modifier that acts very much like the onlyOwner modifier mentioned before. This one is a modifier that checks if the function is called by a verifier.

sol
modifier onlyVerifier() {
    require(
        _verifiers[msg.sender].name != 0,
        "VerificationRegistry: Caller is not a Verifier"
    );
    _;
}
Now, we want to allow people to call the smart contract to get the number of verification records created. We can do that with this function:

sol
function getVerificationCount() external override view returns(uint256) {
    return _verificationRecordCount;
}
Next up, we get to a very important function. This function checks if a particular address has a verification record.

sol
function isVerified(address subject) external override view returns (bool) {
    require(subject != address(0), "VerificationRegistry: Invalid address");
    bytes32[] memory subjectRecords = _verificationsForSubject[subject];
    for (uint i=0; i<subjectRecords.length; i++) {
        VerificationRecord memory record = _verifications[subjectRecords[i]];
        if (!record.revoked && record.expirationTime > block.timestamp) {
            return true;
        }
    }
    return false;
}
This is where the Verification Registry contract really shines. The verification is added to the registry, but that only has to happen once. Future checks can simply rely on this function or similar functions customized to your needs.

When a single verification is needed, this next function will support that. It takes the UUID for the verification as an argument and then returns the full verification record:

sol
function getVerification(bytes32 uuid) external override view returns (VerificationRecord memory) {
    return _verifications[uuid];
}
But what about when you need all the verifications for a particular subject? Let's build a function that will return an array of verifications for a particular wallet address.

sol
function getVerificationsForSubject(address subject) external override view returns (VerificationRecord[] memory) {
    require(subject != address(0), "VerificationRegistry: Invalid address");
    bytes32[] memory subjectRecords = _verificationsForSubject[subject];
    VerificationRecord[] memory records = new VerificationRecord[](subjectRecords.length);
    for (uint i=0; i<subjectRecords.length; i++) {
        VerificationRecord memory record = _verifications[subjectRecords[i]];
        records[i] = record;
    }
    return records;
}
This function takes the wallet address for the subject, filters on the existing verifications for just that address, and returns the records.

Similarly, we can get an array of verifications from a single verifier with this function:

sol
function getVerificationsForVerifier(address verifier) external override view returns (VerificationRecord[] memory) {
    require(verifier != address(0), "VerificationRegistry: Invalid address");
    bytes32[] memory verifierRecords = _verificationsForVerifier[verifier];
    VerificationRecord[] memory records = new VerificationRecord[](verifierRecords.length);
    for (uint i=0; i<verifierRecords.length; i++) {
        VerificationRecord memory record = _verifications[verifierRecords[i]];
        records[i] = record;
    }
    return records;
}
The function takes an argument of verifier and will return all of the verifications for that verifier.

As you would imagine, verifications may be issued in mistake or become invalid for some other reason. So we need a way to revoke those in the registry. We can use a function with the onlyVerifier modifier to do so:

sol
function revokeVerification(bytes32 uuid) external override onlyVerifier {
    require(_verifications[uuid].verifier == msg.sender, "VerificationRegistry: Caller is not the original verifier");
    _verifications[uuid].revoked = true;
    emit VerificationRevoked(uuid);
}
This function takes the UUID of the record as an argument, checks to make sure the record was created by the address making the call, and then updates the revoked status to true.

Notice, the record is not removed. It is updated. This is helpful when performing audits and tracking record history.

This function also emits an event so those interested in listening for revocations can be notified.

Of course, there are times where a verification will need to be completely removed. For that, we can create a function to remove them:

sol
function removeVerification(bytes32 uuid) external override onlyVerifier {
    require(_verifications[uuid].verifier == msg.sender,
        "VerificationRegistry: Caller is not the verifier of the referenced record");
    delete _verifications[uuid];
    emit VerificationRemoved(uuid);
}
Like the previous function, this one takes the UUID of the record as an argument. It verifies the record was created by the address making the call, and then it removes the record from the registry entirely.

This function emits a separate event indicating removal of a record.

The next function we will cover is a big one. All of these go hand-in-hand, but without this function, nothing else is possible. This is the function to add verifications to the registry. Let's look at the function and then walk through what's going on.

sol
function registerVerification(
    VerificationResult memory verificationResult,
    bytes memory signature
) external override onlyVerifier returns (VerificationRecord memory) {
    VerificationRecord memory verificationRecord = _validateVerificationResult(verificationResult, signature);
    require(
        verificationRecord.verifier == msg.sender,
        "VerificationRegistry: Caller is not the verifier of the verification"
    );
    _persistVerificationRecord(verificationRecord);
    emit VerificationResultConfirmed(verificationRecord);
    return verificationRecord;
}
First, let's take a look at the arguments. The function takes a verificationResult argument. This is defined in the VerificationResult type. Second, the function takes a signature. Both of these arguments are hard to conceptualize unless you see them in action, so it is recommended that you take a look at the [Verification Registry in Solidity](/verite/docs/tutorial-verification-registry-in-solidity) tutorial.

The tutorial referenced above will walk you through how to create a signed message that can be sent through as an argument.

As you can see, this function uses the onlyVerifier modifier, which makes sense. A verifier has to be the one to add a verification record.

The function then takes the verification result and passes it through another function for validation called _validateVerificationResult. This is a pretty massive function and we'll cover it in a few minutes. But the basics of the function are that it uses the EIP712 standard and verifies both the signature and the format of the record before inserting the record into the registry.

Next, the function persists the record by calling the _persistVerificationRecord function. That's a pretty straightforward function, so we'll document it here. You can add this in your contract anywhere you'd like as it's a helper function more than anything else.

sol
function _persistVerificationRecord(VerificationRecord memory verificationRecord) internal {
        // persist the record count and the record itself, and map the record to verifier and subject
        _verificationRecordCount++;
        _verifications[verificationRecord.uuid] = verificationRecord;
        _verificationsForSubject[verificationRecord.subject].push(verificationRecord.uuid);
        _verificationsForVerifier[verificationRecord.verifier].push(verificationRecord.uuid);
    }
As you can see, this function has an internal modifier, meaning it can only be called by another function on the contract. It takes the verificationRecord and it updates the _verificationRecordCount, sets the record to the _verifications mapping, adds the record as associated to the subject, and adds the record as associated to the verifier.

Finally, our original registerVerification function emits a message indicating that a new verification record was created.

One important note on this function is that it must be called by the verifier. We mentioned this earlier, but you'll see in a second that a subject can also add their own record to the registry.

This next function allows a subject to handle adding their signed verification record to the registry:

sol
function _registerVerificationBySubject(
    VerificationResult memory verificationResult,
    bytes memory signature
) internal returns (VerificationRecord memory) {
    require(
        verificationResult.subject == msg.sender,
        "VerificationRegistry: Caller is not the verified subject"
    );
    VerificationRecord memory verificationRecord = _validateVerificationResult(verificationResult, signature);
    _persistVerificationRecord(verificationRecord);
    emit VerificationResultConfirmed(verificationRecord);
    return verificationRecord;
}
This function operates similarly to the previous one, but it expects the caller to be the subject of the verification record. Outside of that, the actions are the same.

The final note on both of the above two functions is that you may want to implement additional logic based on your specific use cases. Always remember, this contract serves as a guide, but you can extend and modify it however you'd like.

Moving on, let's create a function that allows a subject to remove a verification record about themself. We previously created a function that allowed a verifier to remove a record, so this will be similar but from the subject's perspective.

sol
function _removeVerificationBySubject(bytes32 uuid) internal {
    require(_verifications[uuid].subject == msg.sender,
        "VerificationRegistry: Caller is not the subject of the referenced record");
    delete _verifications[uuid];
    emit VerificationRemoved(uuid);
}
The function takes the record's UUID as an argument, it checks if the caller is the record's subject, and then it deletes the record from the registry, emitting the VerificationRemoved event at the end.

Ok, we covered the _validateVerificationResult function briefly earlier, but we didn't write out the function. Let's do that now. But remember, it's a doozy and it makes use of a lot of the internal workings we imported from the EIP712 library.

sol
function _validateVerificationResult(
    VerificationResult memory verificationResult,
    bytes memory signature
) internal view returns(VerificationRecord memory) {

    bytes32 digest = _hashTypedDataV4(keccak256(abi.encode(
        keccak256("VerificationResult(string schema,address subject,uint256 expiration)"),
        keccak256(bytes(verificationResult.schema)),
        verificationResult.subject,
        verificationResult.expiration
    )));

    // recover the public address corresponding to the signature and regenerated hash
    address signerAddress = ECDSA.recover(digest, signature);

    // retrieve a verifier address for the recovered address
    address verifierAddress = _signers[signerAddress];

    // ensure the verifier is registered and its signer is the recovered address
    require(
        _verifiers[verifierAddress].signer == signerAddress,
        "VerificationRegistry: Signed digest cannot be verified"
    );

    // ensure that the result has not expired
    require(
        verificationResult.expiration > block.timestamp,
        "VerificationRegistry: Verification confirmation expired"
    );

    // create a VerificationRecord
    VerificationRecord memory verificationRecord = VerificationRecord({
        uuid: 0,
        verifier: verifierAddress,
        subject: verificationResult.subject,
        entryTime: block.timestamp,
        expirationTime: verificationResult.expiration,
        revoked: false
    });

    // generate a UUID for the record
    bytes32 uuid = _createVerificationRecordUUID(verificationRecord);
    verificationRecord.uuid = uuid;

    return verificationRecord;
}
The function has some comments to help you understand what's happening, but at the end of the day, think of this function as simply verifying a signature is actually valid before adding a record to the registry. The function updates the verification record passed to it with four new pieces of information:

- Verifier Address
- Entry Time
- Revoked
- UUID
The UUID is created by calling a helper function called _createVerificationRecordUUID. You may implement a different method for identifying records, but if you choose to use UUIDs generated on the contract, this function should help:

sol
function _createVerificationRecordUUID(VerificationRecord memory verificationRecord) private view returns (bytes32) {
    return
        keccak256(
            abi.encodePacked(
                verificationRecord.verifier,
                verificationRecord.subject,
                verificationRecord.entryTime,
                verificationRecord.expirationTime,
                _verificationRecordCount
            )
        );
}
And that's it. That's the last function. Of course, there are many more you can add when building your own contract, but this is more than enough to get you started. In fact, this is based on the demo contract Verite uses.

#
Wrapping Up
[](#wrapping-up)
A verification registry contract is designed to create an easily searchable, easily verifiable central repository for verifications. This makes things reusable and faster.

This implementation is in Solidity and designed for EVM-compatible blockchains, but a verification registry contract can be implemented on any blockchain.

Updated 6 months ago


URL: https://developers.circle.com/verite/docs/identifier-methods
Title: Identifier Methods
Description: undefined
Keywords: undefined

Verifiable Credentials allow flexible methods for identifying the issuer, the subject, and holder in any given situation. The identifier data type is a URI, which can be a web page, Solid profile, a decentralized identifier (DID), etc. Most of the example code in the documentation and sample implementation use DIDs, but this is exemplary and equivalent functionality using other portable and user-centric identifier systems could be entirely conformant.

The Verite sample implementations use [did:key](https://w3c-ccg.github.io/did-method-key/) (a scheme for representing offline/offchain key material in a DID-compatible format based on [the multicodec registry](https://github.com/multiformats/multicodec/blob/master/table.csv)), and [did:web](https://w3c-ccg.github.io/did-method-web/) (a simplifed resolution mechanism for using a well-known DNS domain as root of trust for self-publishing DID documents) for convenience and simplicity. You should use the identifier method that is appropriate for your requirements, however, assessing distinctly the privacy, durability, and interoperability/intelligibility requirements separately for issuers, holders, and verifiers.

On the subject/holder side, we chose to model holder-side cryptography in the sample implementation using did:key because it requires no gas/onchain fees nor assumes any blockchain dependencies. It's a purely generative method, enabling resolution offline, without any registry lookups (i.e., the DID documents can be derived from the DID itself deterministically). This enables identity wallet applications to mimic the behavior of existing crypto wallets.

At the same time, did:key does not support key rotation, which is sometimes a desired DID method characteristic, especially for issuers. A simple option for issuers with long-standing web domains and established processes for pushing updates is to use did:web. For a hands-on tutorial on did:web, see [did:web in minutes](https://www.spruceid.dev/didkit/didkit-examples/did-web-in-minutes) on the SpruceID didkit developer documentation.

##
Evaluating DID Methods
[](#evaluating-did-methods)
There are a large number of DID methods, some of which are based on blockchains, some based on web pages, and some purely generative. Some may support a complete range of DID operations like update, and others (like did:key) may not.

While implementors may choose any method they like, some common factors include:

- technical feasibility
- availability of multiple open source implementations (demonstrating interoperability)
- no/low fee
- the ability for issuers, verifiers, and holder wallets to resolve DIDs without relying on a specific tenant or chain
In general, DID method options will be influenced by a broader set of criteria relevant to all roles in the VC ecosystem, a comprehensive consideration of which is available in the [DID Method Rubric](https://w3c.github.io/did-rubric).

##
Wallet-based versus Address-based Holder Identification Schemes
[](#wallet-based-versus-address-based-holder-identification-schemes)
Depending on the trust model and architecture, and crucially on the liability and control structure around a given wallet, Verite issuers may prefer to issue claims (and downstream dapps interpret those claims) either about:

- a specific blockchain address, or
- a wallet controller (i.e. legal or natural person) via an internal representation of that wallet's control structure identified by a full-featured, fit-for-purpose DID.
For example, an identity-verification process that provides identity assurance over a multi-address or multi-chain wallet is better served by a full-featured user-controlled on-chain DID (e.g. [did:ion](https://identity.foundation/ion/)).

Conversely, in a situation where identity-verification and wallet-binding are better done separately for each address controlled by an entity (or where relying parties prefer to identify counterparties exclusively by on-chain addresses), the blockchain address itself can be the subject of claims, expressed as a generative [did:pkh](https://github.com/w3c-ccg/did-pkh/blob/main/did-pkh-method-draft.md) for maximum compatibility across contexts and interoperability with other Verite implementations.

Note that this crucial distinction has ramifications throughout the implementation for how credentials are presented and verified, and each favors a different trust model for wallets and intermediaries. The [Verification flow](/verite/docs/verifying-credentials) page includes examples of both.

Updated 5 months ago


URL: https://developers.circle.com/verite/docs/quickstart-verify-verite-credentials
Title: Quickstart: Verify Verite Credentials
Description: undefined
Keywords: undefined

Verification is a chain of three client-server requests:

- Initialization call
- Manifest call
- Verification call
A sequential API request is submitted based on the response of the previous request.


Verite verification flow

###
Initialization call
[](#initialization-call)
The verification flow starts with the initialization call.

#####
Endpoint
[](#endpoint)
POST: /verifications

#####
Query
[](#query)
Its query contains the network and the chainId to help the verifier server decide which blockchain to use. Subject is the DeFi application's address on that blockchain.

Initialization Query Payload
{
  "network": "ethereum",
  "chainId": 1337,
  "subject": "0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266"
}
#####
Response
[](#response)
The response contains two fields: challengeTokenUrl and statusUrl. challengeTokenUrl is for the manifest call and statusUrl is for asynchronous status checks.

Initialization Response
{
  'challengeTokenUrl': 'https://verifier-sandbox.circle.com/api/v1/verifications/14b2ade1-389c-4acc-87c6-89d96c95af28',
  'statusUrl': 'https://verifier-sandbox.circle.com/api/v1/verifications/14b2ade1-389c-4acc-87c6-89d96c95af28/status'
}
###
Manifest call
[](#manifest-call)
#####
Endpoint
[](#endpoint-1)
GET: /verifications/{id}

The Manifest step calls the challengeTokenUrl from the initialization call's response. It is a GET call without additional parameters.

#####
Response
[](#response-1)
The response is a manifest list that the server accepts. It contains many meta fields as well as fields that should be carried to the verification call.

Manifest call response
{
  "id": "14b2ade1-389c-4acc-87c6-89d96c95af28",
  "type": "https://circle.com/types/VerificationRequest",
  "from": "did:web:circle.com",
  "created_time": "2022-09-26T18:42:17.687Z",
  "expires_time": "2022-10-26T18:42:17.687Z",
  "reply_url": "https://verifier-sandbox.circle.com/api/v1/verifications/14b2ade1-389c-4acc-87c6-89d96c95af28",
  "body": {
    "status_url": "https://verifier-sandbox.circle.com/api/v1/verifications/14b2ade1-389c-4acc-87c6-89d96c95af28/status",
    "challenge": "126c210d-f458-4ec7-a0d2-0e0a2b8aac42",
    "presentation_definition": {
      "id": "14b2ade1-389c-4acc-87c6-89d96c95af28",
      "format": {
        "jwt": {
          "alg": [
            "EdDSA",
            "ES256K"
          ]
        },
        "jwt_vc": {
          "alg": [
            "EdDSA",
            "ES256K"
          ]
        },
        "jwt_vp": {
          "alg": [
            "EdDSA",
            "ES256K"
          ]
        }
      },
      "input_descriptors": [
        {
          "id": "kybpaml_input",
          "name": "Proof of KYBP",
          "schema": [
            {
              "uri": "https://verite.id/definitions/processes/kycaml/0.0.1/generic--usa-legal_person",
              "required": true
            }
          ],
          "purpose": "Please provide a valid credential from a KYBP/AML issuer",
          "constraints": {
            "fields": [
              {
                "path": [
                  "$.issuer.id",
                  "$.issuer",
                  "$.vc.issuer",
                  "$.iss"
                ],
                "filter": {
                  "type": "string",
                  "pattern": "^did:web:issuer-sandbox.circle.com$|^did:web:assets.circle.com$"
                },
                "purpose": "The issuer of the credential must be trusted",
                "predicate": "required"
              },
              {
                "path": [
                  "$.credentialSubject.KYBPAMLAttestation.process",
                  "$.vc.credentialSubject.KYBPAMLAttestation.process",
                  "$.KYBPAMLAttestation.process"
                ],
                "filter": {
                  "type": "string"
                },
                "purpose": "The process used for KYBP/AML.",
                "predicate": "required"
              },
              {
                "path": [
                  "$.credentialSubject.KYBPAMLAttestation.approvalDate",
                  "$.vc.credentialSubject.KYBPAMLAttestation.approvalDate",
                  "$.KYBPAMLAttestation.approvalDate"
                ],
                "filter": {
                  "type": "string",
                  "pattern": "^[0-9]{4}-[0-9]{2}-[0-9]{2}T[0-9]{2}:[0-9]{2}:[0-9]{2}.[0-9]{3}Z$"
                },
                "purpose": "The date upon which this KYBP/AML Attestation was issued.",
                "predicate": "required"
              }
            ],
            "statuses": {
              "active": {
                "directive": "required"
              },
              "revoked": {
                "directive": "disallowed"
              }
            },
            "is_holder": [
              {
                "field_id": [
                  "subjectId"
                ],
                "directive": "required"
              }
            ]
          }
        }
      ]
    }
  }
}
reply_url field is the URL that the client should call in the verification step.

###
Verification call
[](#verification-call)
#####
Endpoint
[](#endpoint-2)
POST: /verifications/{id}
The URL that the client should call at this step is the reply_url field in the response of the previous call.

#####
Request
[](#request)
JSON
{
  "credential_fulfillment": {
    "descriptor_map": [
      {
        "format": "jwt_vc",
        "id": "proofOfIdentifierControlVP",
        "path": "$.presentation.credential[0]"
      }
    ],
    "id": "e921d5b2-5293-4297-a467-907f9d565e4e",
    "manifest_id": "KYBPAMLAttestation"
  },
  "presentation_submission": {
    "id": "b68fda51-21aa-4cdf-84b7-d452b1c9c3cc",
    "descriptor_map": [
      {
        "format": "jwt_vc",
        "id": "kybpaml_input",
        "path": "$.verifiableCredential[0]"
      }
    ],
    "definition_id": "14b2ade1-389c-4acc-87c6-89d96c95af28"
  },
  "vp": {
    "@context": [
      "https://www.w3.org/2018/credentials/v1"
    ],
    "type": [
      "VerifiablePresentation",
      "CredentialFulfillment"
    ],
    "verifiableCredential": [
      "eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NksifQ.eyJzdWIiOiJkaWQ6a2V5OnpRM3NodjM3OFB2a011UnJZTUdGVjlhM010S3BKa3RlcWIyZFViUU1FTXZ0V2MydEUiLCJuYmYiOjE2NTg5NTM4MjIsImlzcyI6ImRpZDp3ZWI6YXNzZXRzLmNpcmNsZS5jb20iLCJleHAiOjE2NTk1NTg2MjIsInZjIjp7IkBjb250ZXh0IjpbImh0dHBzOi8vd3d3LnczLm9yZy8yMDE4L2NyZWRlbnRpYWxzL3YxIiwiaHR0cHM6Ly92ZXJpdHkuaWQvaWRlbnRpdHkiXSwidHlwZSI6WyJWZXJpZmlhYmxlQ3JlZGVudGlhbCIsIktZQlBBTUxBdHRlc3RhdGlvbiJdLCJjcmVkZW50aWFsU3ViamVjdCI6eyJLWUJQQU1MQXR0ZXN0YXRpb24iOnsiYXBwcm92YWxEYXRlIjoiMjAyMi0wNy0yN1QyMDozMDoyMi4wNThaIiwicHJvY2VzcyI6Imh0dHBzOi8vdmVyaXRlLmlkL2RlZmluaXRpb25zL3Byb2Nlc3Nlcy9reWNhbWwvMC4wLjEvZ2VuZXJpYy0tdXNhLWxlZ2FsX3BlcnNvbiIsInR5cGUiOiJLWUJQQU1MQXR0ZXN0YXRpb24ifSwiaWQiOiJkaWQ6a2V5OnpRM3NodjM3OFB2a011UnJZTUdGVjlhM010S3BKa3RlcWIyZFViUU1FTXZ0V2MydEUifSwiY3JlZGVudGlhbFN0YXR1cyI6eyJpZCI6Imh0dHBzOi8vaXNzdWVyLXNtb2tlYm94LmNpcmNsZS5jb20vYXBpL3YxL2lzc3VhbmNlL3N0YXR1cy8wIzE0NCIsInR5cGUiOiJTdGF0dXNMaXN0MjAyMUVudHJ5Iiwic3RhdHVzTGlzdEluZGV4IjoiMTQ0Iiwic3RhdHVzTGlzdENyZWRlbnRpYWwiOiJodHRwczovL2lzc3Vlci1zbW9rZWJveC5jaXJjbGUuY29tL2FwaS92MS9pc3N1YW5jZS9zdGF0dXMvMCJ9LCJpc3N1YW5jZURhdGUiOiIyMDIyLTA3LTI3VDIwOjMwOjIyLjA1OFoifX0.6ifRvKNZ4xd4VSFYKZ_uf2xSciTacYBz_7ysqyXrfom4non--PQyKfEz8HUO3sYoNIuZgRILZTPOhrj10wPyjw"
    ],
    "holder": "did:key:zQ3shv378PvkMuRrYMGFV9a3MtKpJkteqb2dUbQMEMvtWc2tE"
  },
  "nonce": "126c210d-f458-4ec7-a0d2-0e0a2b8aac42",
  "sub": "0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266",
  "iss": "did:key:zQ3shv378PvkMuRrYMGFV9a3MtKpJkteqb2dUbQMEMvtWc2tE"
}
There are several fields in the query that the clients need to carry from either the response of the previous query or the verifiable credential (VC) fetched from the wallet.

field in the query	field in the response of the previous call
iss	VC's DID
sub	wallet's address
nonce	body.challenge
presentation_submission.definition_id	body.presentation_definition.id
vp.verifiableCredential	VC that is from wallet.
vp.holder	wallet's address
#####
Response
[](#response-2)
A successful verification returns a JSON blob, as illustrated below.

Verification call response
{
    "status": "success",
    "verificationResult": {
        "schema": "verite.id/definitions/processes/kycaml/0.0.1/generic--usa-legal_person",
        "subject": "0xb4d00788f75f27b85285b3af21bdc16b3336d91e",
        "expiration": 1663906402,
        "verifier_verification_id": "2912ef9f-10af-43a8-82a2-b992a8cee111"
    },
    "signature": "0x52e5b656006abdc0b741e1671ab687d71d1369455d4d6ee9ede35262b867dc9e49fc2f59aadbe4829b0ce53c7c52b9a88509e28a888025ec5e503f1cc14da8fb1c"
}
field name	meaning
status	The status of the verification.
verificationResult	The result of the verification.
signature	The signed string, by verifier, of the verificationResult
Updated 6 months ago


URL: https://developers.circle.com/verite/docs/tutorial-verification-registry-in-solidity
Title: Tutorial: Verification Registry in Solidity
Description: undefined
Keywords: undefined


URL: https://developers.circle.com/verite/docs/architecture-overview
Title: Architecture Overview
Description: undefined
Keywords: undefined

Verite’s identity topology is attestation-based. People and institutions make claims about their identities, and others (such as financial institutions and service providers) attest to the validity of those claims by providing cryptographic proofs about them. Once such a cryptographic attestation exists, people can choose to share them with other entities and services that can then verify the attestation as proof of the claim.

This article describes the architectural topology and key components of this model.

##
Identity Hierarchy
[](#identity-hierarchy)
In this architecture, individuals and institutions have one or more identities, and each of those identities in turn may have multiple claims associated with it. This digital hierarchy maps to the physical world in which entities such as individuals and institutions have several identity contexts: A person may have a financial identity, a healthcare identity, a university identity, a social media identity, etc. Each of those unique identity contexts have separate claims associated with them.


Verite focuses on the identity claims and how to prove them to others without sharing more data than is needed (data minimization) and without relying on others to control the claims or control the act of sharing them.

##
Identity Protocol
[](#identity-protocol)
Decentralized identity protocols define the way attestations are issued, stored, requested, verified, and revoked.

The protocols enable individuals and institutions to request and receive cryptographic attestations about identity claims from an "issuer". A financial institution issuing attestations about KYC or accredited investor status to its customers is an an example of an issuer.

Individuals and institutions can custody and manage these attestations in their crypto/identity wallets. The attestations take the form of "Verifiable Credentials".

The individuals and institutions who receive Verifiable Credentials about their identities are often referred to as "subjects" and the identity responsible for custodying the Verifiable Credentials is referred to as a "holder." The subject and holder are the same when self-custodying, but they are not the same if the subject uses a hosted wallet service. One example of a subject is a customer who receives a KYC Verifiable Credential from her financial institution. If she custodies this Verifiable Credential in her own wallet, she is also the holder; if she chooses to custody the credential in a hosted wallet service, then the wallet service is the holder.

Individuals and institutions decide when and with whom they want to share their Verifiable Credentials. Those who request and receive Verifiable Credentials are referred to as "verifiers" or more generally as "relying parties". A smart contract requiring proof of KYC is one example of a relying party.


##
Foundational Standards
[](#foundational-standards)
Verite endorses the W3C Verifiable Credentials (VCs) Data Model. A credential is a claim made by an issuer about an individual (subject), and a Verifiable Credential is a cryptographically secure wrapper around the credential. Verite also leverages Verifiable Presentations to wrap Verifiable Credentials for transmission and prevent replay attacks. Relying parties can verify a credential without contacting its issuer by employing the extensible mechanism defined by the Verifiable Credentials specification. The verification process is designed to be privacy-respecting and cryptographically secure.

Foundational standards that Verite draws upon include:

-
W3C Verifiable Credentials (“VCs”): Verifiable Credentials define a standard flexible, tamper-evident way for an issuer to make a claim about a subject in a way that is independently verifiable and privacy-preserving. VCs are the data model used for Verite claims attestations.

-
Verifiable Presentations ("VPs"): Also defined in the VC spec, Verifiable Presentations provide a way to securely package a set of VCs for transmission to a relying party in a way that allows the subject to prove control over the credentials.

-
Identifiers, Decentralized Identifiers: An "identifier" refers to both a subject and an issuer of a VC. The identifier data type is a URI, and may be a W3C Decentralized Identifier (DID), which is used in Verite reference implementations. Verite does not require the use of DIDs, but they are one standard way to implement identifiers that minimize correlability and enable proof of control over credentials.

-
Verifiable Data Registry: This is a general concept that enables decoupling of verification from the issuer. It's typically used for storing revocation registries, trusted issuer lists, or similar data repositories to be accessed during the verification process. In VC implementations, these are often implemented with a distributed ledger (note that in the Verite approach, the credentials themselves are not stored on-chain).

-
Credential Manifest: This standard from the Decentralized Identity Foundation (DIF) defines the mechanism for requesting and receiving a credential. A Credential Manifest allows an issuer to describe (in a machine-readable way) what types (schemas) of credentials they issue and what their requirements are. It also describes the format for a subject/holder to submit an application for a credential that conforms to those requirements.

-
Presentation Exchange: This DIF standard enables a verifier to describe what types of credentials they require from a subject/holder, and how the subject/holder can send a submission.

-
Wallet and Credential Interactions: Verite's Wallet interaction protocols use a lightweight flow loosely based on the work-in-progress DIF Wallet and Credential Interactions specification.

###
More About Standards
[](#more-about-standards)
A Verite primer on VCs, VPs, and DIDs is available for [reference here](/verite/docs/primer-dids-vcs-vps).

###
Specification References
[](#specification-references)
The complete list of specifications used in Verite's implementation is available for [reference here](/verite/docs/specifications-and-libraries).

Updated 5 months ago


URL: https://developers.circle.com/verite/docs/principles-goals
Title: Principles & Goals
Description: undefined
Keywords: undefined

##
First Principles
[](#first-principles)
Verite's decentralized identity standards aim to satisfy the following design principles:

-
Decentralized: Requires no central issuing agency and functions effectively in DeFi.

-
Persistent and portable: Inherently persistent and long-lived, not requiring the continued operation of any underlying organization.

-
Cryptographically verifiable: Based on cryptographic proofs rather than out-of-band trust.

-
Resolvable and interoperable: Open to any solution that recognizes the common protocols and data model and requires no one specific software vendor implementation, including any that Circle or its members may create.

-
Transparent: Identity holders know when and how their identity data is being requested and used.

-
Private by design: No data correlatable to an identity holder is exposed to a public network, including registry mappings and blockchain persistence.

##
Goals
[](#goals)
Interoperability across the ecosystem is an over-arching Verite goal. There has been no previous industry-wide agreement on how products and services might interoperate in key crypto finance use cases, such as how they represent proof of KYC or accredited investor status. Verite aims to provide clarity by defining these standard connections and providing recipes to illustrate their usage.

Verite's specific tactical goals are to: (a) define data models (schemas) that should be shared and exist as common building blocks for all parties as a public good; and (b) define the protocols for requesting and delivering identity claims in a manner that supports crypto finance use cases.

The intent is to enable any member of the broader crypto ecosystem to develop products and solutions that are inherently compliant and interoperable with each other, and to do so in a manner that is open, transparent, and usable by anyone, whether connected to Circle and the USDC ecosystem or not.

A process goal is to maintain transparency and openness in the iteration of the protocols and data models. Anyone is free to use the source code, modify it, and submit improvements to it.

##
Non-Goals
[](#non-goals)
-
Verite standards do not require use of a specific chain, token, consensus algorithm, wallet, storage system, p2p library, or other fundamental infrastructure.

-
Verite aims to specify definitions for claims that can be supported by any identity solution, and to specify protocols that can be implemented by any service, wallet, or application.

-
Verite does not require use of any specific Decentralized Identifier (DID) method or new attestation format.

-
Verite is not intended as a competing decentralized identity standard; it aims to leverage and contribute to existing (and emerging) decentralized identity standards.

Updated 5 months ago


URL: https://developers.circle.com/verite/docs/introducing-verite
Title: Introducing Verite
Description: undefined
Keywords: undefined

We carry credentials with us everyday. Our drivers license or state ID, our library card, our insurance card, and more are all just a few examples of some of the credentials we carry. We present these credentials when requested, and the credentials are verified by the person or entity we are presenting them to. But as the world shifts to an increasingly digital native format, and as people take more ownership over their identity, how can the issuance of, presentation of, and verification of credentials be managed?

The answer is Verifiable Credentials (VCs). VCs are on a web standards track and provide an open data model for digital credentials. We won't go deep into the details of VCs here because the [Verite documentation](/verite/docs/verite-protocol-introduction) does a great job of that. Instead, we will focus on what Verite is, why it exists, and how it might be used.

##
What Is Verite?
[](#what-is-verite)
Verite is a collaborative and open source initiative spearheaded by the Circle, and its focus is to "provide the governance and standards for the future digital financial ecosystem." Verite is one output of the collaborative effort.

While contributions to the VCs standards themselves is important, and Circle is an active participant alongside the W3C, tools that make it easy to implement and leverage VCs are just as important. As such, Circle began work last year on Verite, an open source library designed to make managing VCs easier.

Currently available in TypeScript and [published through NPM](https://www.npmjs.com/package/verite), the Verite library seeks to make it easier to implement VCs in a variety of forms. The library is early and an additional goal of the library is to collect community feedback.

Verite's helper functions simplify VC flows such as requesting credentials, issuing credentials, presenting credentials, and verifying credentials. For the fastest possible start to using Verite, check out the [Quick Start guide](/verite/docs/verite-protocol-quickstart) in the documentation.

##
Why Does Verite Exist?
[](#why-does-verite-exist)
Despite the standards track for VCs, tooling around the implementation of VCs has historically been limited. There have been reference implementations, but a simple developer experience has largely been missing. This is what Verite sets out to solve.

We believe that adoption of VCs is not only about creating good standards. It's also about making the experience of implementation a great one. To this end, Verite has tried to abstract as much of the complexity away as possible. Our documentation dives deep into what's happening behind the scenes, but the Verite library itself feels simple. And that's the goal.

In a sense, Verite is also the canary in the coal mine. It's a test. An experiment. As mentioned above, the secondary goal of Verite's library is to collect community feedback on its implementation. Since the library's release, we've been fortunate enough to receive a number of issues and pull requests in the [Github repository](https://github.com/circlefin/verite). We encourage the community to continue provide feedback.

##
How Can Verite Be Used?
[](#how-can-verite-be-used)
We have a [number of demos available](https://github.com/circlefin/verite/tree/main/packages/e2e-demo/pages/demos) in the Github repository that can serve both as reference points and as inspiration. Verite can be used for just about any VC need. In our demos, you'll find:

- Basic Credential Issuance
- Basic Credential Verification
- KYC Compliance and Revocation
- Ethereum-based dApp With Credential Requirement
- Lending Market dApp With KYC Requirement
- Centralized App With Travel Rule Requirement
By design, these demos offer both a mobile credential wallet experience or a credential simulation experience. Verite has no plans to dictate the way credentials are stored and presented. The Verite library can be included in existing cryptocurrency wallets, can be used to built a new digitial credentials wallet, can be used to build mobile apps, and more.

We expect to see some interesting use cases come up in the months ahead because Verite is designed with flexibility in mind. Between community collaboration and that flexibility, implementing and experimenting with VCs will become increasingly easier. We're excited for the future, and we hope you'll help us build that future.

Updated 6 months ago


URL: https://developers.circle.com/verite/docs/specifications-and-libraries
Title: Specifications and Libraries
Description: undefined
Keywords: undefined

Decentralized Identity standards are emerging and at varying levels of maturity. Nonetheless, these standards are important to Verite's design principles.

To navigate this, the following specifications and libraries were selected for their promise of broad adoption and interoperability. This list isn’t intended to be exclusive; support for additional standards may be added in the future.

Verite uses additional simplifications that remove the requirement for implementers to fully implement the referenced specifications. The flows and message structures described in our documentation suffice for implementers.

##
Specifications
[](#specifications)
- [W3C Verifiable Credentials Data Model](https://www.w3.org/TR/vc-data-model/)
- [W3C Decentralized Identifiers (DIDs) v1.0](https://www.w3.org/TR/did-core/)
- [DIF Presentation Exchange](https://identity.foundation/presentation-exchange/)
- [DIF Credential Manifest](https://identity.foundation/credential-manifest/)
- [W3C Credential Status 2021](https://w3c-ccg.github.io/vc-status-list-2021/)
- [W3C did:key Method](https://w3c-ccg.github.io/did-method-key/)
- [W3C did:web Method](https://w3c-ccg.github.io/did-method-web/)
- [DIF Wallet and Credential Interactions](https://identity.foundation/wallet-and-credential-interactions/)
- [IETF Multiformats Multibase](https://datatracker.ietf.org/doc/html/draft-multiformats-multibase-03)
- [IETF Multiformats Multicodec](https://datatracker.ietf.org/doc/html/draft-snell-multicodec-00)
##
Libraries
[](#libraries)
###
Verifiable Credentials and DIDs
[](#verifiable-credentials-and-dids)
- [did-jwt-vc](https://github.com/decentralized-identity/did-jwt-vc)
- [did-resolver](https://github.com/decentralized-identity/did-resolver)
- [web-did-resolver](https://github.com/decentralized-identity/web-did-resolver)
- [key-did-resolver](https://github.com/ceramicnetwork/js-ceramic/tree/develop/packages/key-did-resolver)
###
Contracts
[](#contracts)
- [Ethers](https://docs.ethers.io/v5/)
- [Hardhat](https://hardhat.org/)
- [@openzeppelin/contracts](https://github.com/OpenZeppelin/openzeppelin-contracts)
Updated 6 months ago


URL: https://developers.circle.com/verite/docs/revocation
Title: Revocation
Description: undefined
Keywords: undefined

This tutorial demonstrates:

- How an issuer can create a revocable credential
- How an issuer can revoke a credential
- How a verifier can determine whether a given credential is revoked
##
About
[](#about)
Verite's revocation implementation uses the approach described in [W3C Status List 2021](https://w3c-ccg.github.io/vc-status-list-2021/) -- a highly compressible, privacy-promoting approach.

In this method, the revocation status of a given verifiable credential is stored in a specified index of a bitstring. If the value is 0, the credential is not revoked; otherwise it's revoked.

Note: Deployments of this approach vary, and this project uses a simple REST endpoint to serve the bitstring.

Then, at verification time, a verifier obtains the entire bitstring and looks up the value at the index specified in the credential to determine whether it's been revoked.

This has the advantage that, even if the issuer is consulted for the bitstring, the verifier doesn't tell the issuer exactly which index it is checking, enabling what's called "herd privacy".

##
Example of a Revocable Credential
[](#example-of-a-revocable-credential)
Below is an [example from Status List 2021](https://w3c-ccg.github.io/vc-status-list-2021/#revocationlist2021) demonstrating what a Revocable Credential might look like (in JSON-LD format, for clarity). Note the addition of credentialStatus object with several fields:

JSON
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://w3id.org/vc-status-list-2021/v1"
  ],
  "id": "https://example.com/credentials/23894672394",
  "type": ["VerifiableCredential"],
  "issuer": "did:example:12345",
  "issued": "2021-04-05T14:27:42Z",
  "credentialStatus": {
    "id": "https://dmv.example.gov/credentials/status/3#94567",
    "type": "StatusList2021Entry",
    "statusPurpose": "revocation",
    "statusListIndex": "94567",
    "statusListCredential": "https://example.com/credentials/status/3"
  },
  "credentialSubject": {
    "id": "did:example:6789",
    "type": "Person"
  },
  "proof": { ... }
}

The presence of this object tells verifiers that they should check the credential for its current status, and where/how to do it. Specifically:

- statusListCredential is the URI from which the revocation list should be retrieved
- statusListIndex indicates which index in the bitstring this credential corresponds to
- The type StatusList2021Entry informs verifiers the data structure to expect and steps to use to check whether the credential has been revoked
##
How an Issuer Creates a Revocable Credential
[](#how-an-issuer-creates-a-revocable-credential)
The issuer must decide at creation time whether the credential should be revocable, because the credentialStatus property will needed to be added to the issued verifiable credential.

Suppose the following:

- url is the URL where the bitstring revocation list will be accessible
- index is the index within the bitstring storing the specific credential's revocation status
Then credentialStatus can be populated as follows:

JavaScript
const credentialStatus: {
  id: `${url}#${index}`,
  type: "StatusList2021Entry",
  statusPurpose: "revocation",
  statusListIndex: index.toString(),
  statusListCredential: url
}
When issuing the credential, the issuer would pass credentialStatus as follows:

JavaScript
const encoded = await composeVerifiableCredential(
  issuer,
  subject.id,
  attestation,
  { credentialStatus }
)
A complete example follows:

JavaScript
// We will create a random did to represent our own identity wallet
const subject = randomDidKey(randomBytes)

// Stubbed out credential data
const attestation: KYCAMLAttestation = {
  type: "KYCAMLAttestation",
  process: "https://verite.id/definitions/processes/kycaml/0.0.1/usa",
  approvalDate: new Date().toISOString()
}
const credentialType = "KYCAMLCredential"

/**
 * Assume the credential's index within the bitstring will be 0
 */
const credentialStatus = {
  id: `${revocationListUrl}#0`,
  type: "StatusList2021Entry",
  statusPurpose: "revocation",
  statusListIndex: "0",
  statusListCredential: `${revocationListUrl}`
}

// Generate the signed, encoded credential
const encoded = await composeVerifiableCredential(
  issuer,
  subject.id,
  attestation,
  credentialType,
  { credentialStatus }
)
In a real implementation, an issuer may want to maintain multiple revocation status lists. See [revocation best practices](/verite/docs/status-registry-practices) for details.

##
Revoking a Credential
[](#revoking-a-credential)
When an issuer needs to revoke a credential, the bitstring approach requires the credential's index within the bitstring to be flipped to 1. Verite libraries expose a convenience method for issuers, as follows:

JavaScript
statusList = await revokeCredential(credential, statusList, signer)
Verite similarly exposes unrevokeCredential to undo the revocation. Unrevoking doesn't have to be supported by issuers; some issuers choose to issue a new credential instead.

In a deployment, the revoking and unrevoking operations could be exposed by authenticated API calls that would be accessed by an authorized party, such as a compliance agent.

Note that revoking a credential doesn't change the subject's credential or the revocation list itself since both are immutable. A new, modified revocation list is built, which the party hosting the revocation list will need to persist for future lookups.

##
Verification
[](#verification)
The credential itself will inform a verifier whether the credential's status needs to be checked and how. A revocable Verite credential will have a credentialStatus field of type RevocationList2021Status, which informs the verifier how to proceed.

At verification time, the verifier will fetch the content at the revocationListUrl. This content will be a verifiable credential, of type "StatusList2021Credential", which contains the bitstring at the credentialSubject.encodedList property (zlib-compressed, base64-encoded). See [StatusList2021Credential](https://w3c-ccg.github.io/vc-status-list-2021/#statuslist2021credential) for additional details.

JSON
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://w3id.org/vc-status-list-2021/v1"
  ],
  "id": "https://example.com/credentials/status/3",
  "type": ["VerifiableCredential", "StatusList2021Credential"],
  "issuer": "did:example:12345",
  "issued": "2021-04-05T14:27:40Z",
  "credentialSubject": {
    "id": "https://example.com/status/3#list",
    "type": "StatusList2021",
    "statusPurpose": "revocation",
    "encodedList": "H4sIAAAAAAAAA-3BMQEAAADCoPVPbQsvoAAAAAAAAAAAAAAAAP4GcwM92tQwAAA"
  },
  "proof": { ... }
}

The verifier then inspects the credential's index in the bitstring to determine if it's revoked.

Verite exposes a helper function isRevoked:

JavaScript
await isRevoked(credential, statusList)
Updated 6 months ago


URL: https://developers.circle.com/verite/docs/issuer-setup
Title: Issuer Setup
Description: undefined
Keywords: undefined

Before issuing credentials, you will need to following at minimum.

- Deciding what types of credentials to issue, and their schemas
- Verite includes a few representative schemas, such as proof of KYC and chain address ownership.
- See [Recommendations for Credential Schema Design](/verite/docs/schemas).
- Decide what type of issuing identifier method (e.g., DID method) you want to use.
- In order for verifiers to verify credentials, they must be able to determine your authorized signing keys.
- If you are using a DID method, you will need to ensure you are signing credentials with keys resolvable from the DID.
- See [Identifier Methods](/verite/docs/identifier-methods) for factors in this decision.
- Allow users and credential wallets to discover how to interact with you as an issuer
- In the flows described here, this starts with a QR code or deep link that the user opens from their credential wallet
- The flow should enable the wallet to discover metadata about how to request and receive the credential
- Verite uses [DIF Credential Manifest](https://identity.foundation/credential-manifest/) for this purpose. It allows an issuer to describe what sort of credentials they issue, prerequisites for issuance, and output schemas
Updated 6 months ago


URL: https://developers.circle.com/verite/docs/smart-contract-patterns
Title: Smart Contract Patterns
Description: undefined
Keywords: undefined

##
Overview
[](#overview)
A core functionality of Verite is that it enables smart contracts to require successful verification of Verifiable Credentials in order to safely and securely assess proofs of identity, credit and risk scoring, insurance, accredited investor status and other identity-related claims.

Today, smart contracts on most chains (such as ethereum) are not technically capable and/or economically practical at executing the verification operations themselves, and they cannot call upon external verification or other network services beyond the constraints of their own chains. Verification in-contract would also require transmitting credentials on-chain, which may leak sensitive personal information and violate the first principle of 'Privacy by Design.'

Instead, verification can be done by a web-based "front-end", or outsourced to a verifier service that communicates to the dApp directly (via offchain transactions delivered to the smart contract along with the transactions it authorizes) or indirectly (via on-chain registries, oracles, or other mechanisms).

All of these architectures have in common a pattern in which verifications -- including credential exchanges between verifiers and subjects, revocation and validation checks, and verifiable credential signature verifications -- are executed "off-chain" and then cryptographically validated by on-chain smart contracts that associate Verification Records with on-chain addresses. These can live in a Verification Registry on-chain if the registry observes a few lightweight, privacy-preserving best practices. (Note: detailed guidance on this is forthcoming, care of the Verite On-Chain Best Practices Working Group)

This document describes the architecture of this design, illustrates two primary options for implementation and two options for storage, and references example source and tests. The reference implementations are scoped to [Solidity](https://docs.soliditylang.org/en/v0.8.10/), but are intended to illustrate patterns applicable to other chains with similar smart contract programming capability.

##
Registry-Based Architecture
[](#registry-based-architecture)
Smart contracts follow a verification pattern in which verifications are performed off-chain and then confirmed on-chain. An off-chain verifier handles Verifiable Credential exchange in the usual manner and, upon successful verification, the verifier creates a lightweight privacy-preserving Verification Result object (represented in JSON).

The verifier then hashes and signs the Verification Result. The signature, along with the result itself, enables subsequent validation by smart contracts known as Verification Registries.

The verifier either registers the result directly with a Verification Registry contract as part of the verification process (the “Verifier Submission” pattern), or else returns it to subjects for them to use in smart contract transactions (the “Subject Submission” pattern). Both sequences are discussed below.

The registry smart contract validates the Verification Result and the verifier’s signature, and creates a privacy-preserving Verification Record that enables subsequent apps, contracts, and tokens to query whether a particular verification is associated with a particular address.

###
Verification Results
[](#verification-results)

Upon completing successful verification of Verifiable Credentials, verifiers create Verification Results and sign them. Verification Results contain minimal information:

- A schema identifier identifying the type of credential that was verified
- The public on-chain address associated with the verified subject, and
- The expiration time of the result (which may occur sooner than the expiration of the credential)
A Verification Result created by a Verifier is never directly persisted to a blockchain, but it may be (if on-chain persistence is needed by the use case) persisted as a minimized Verification Record. Best practices guidance for minimizing privacy risks when persisting these records are forthcoming.

A Verification Result and the verifier’s signature are passed as parameters to a Verification Registry contract function for validation. The registry uses the signature and the Verification Result to determine whether or not the result corresponds to the public address of a known verifier configured in the registry contract.

A Verification Registry executes this confirmation by accepting the Verification Result and signature as input parameters, checking any relevant contract logic against data in the Verification Result, and then re-hashing the Verification Result and using the hash and the submitted signature to confirm that the signature was generated by the private signing key of a corresponding public address of a known verifier configured in the contract. To prevent replay attacks, the Verification Registry also ensures that the calling address is the verifier (for verifier submissions), or that the calling address is the same as the subject of the VerificationResult (for subject submissions).

In order to safeguard against potential issues with hashing, signatures, and key recovery in Ethereum, the reference implementations utilize [EIP-712](https://eips.ethereum.org/EIPS/eip-712) as the preferred hashing and signing approach when evaluating Verification Results. Specifically, the examples employ the [OpenZeppelin EIP-712 implementation](https://docs.openzeppelin.com/contracts/3.x/api/drafts#EIP712). The reference implementations also leverage OpenZeppelin’s [Ownable](https://docs.openzeppelin.com/contracts/2.x/api/ownership#Ownable) and [ERC20](https://docs.openzeppelin.com/contracts/2.x/api/token/erc20) implementations.

###
Verification Records
[](#verification-records)
Upon successful confirmation, the Verification Registry smart contract generates a Verification Record with basic information about the verification. This is the data model that is emitted as an event by the contract, and the data model that is optionally persisted on-chain.


A Verification Record contains:

- A UUID associated with the verification qua event (Note: we also recommend logging events by the same UUID for cleaner auditing and forensics of verification functions)
- Verifier's and subject's public chain addresses
- The time of verification confirmation in the contract
- The expiration time of the verification (which is not necessarily the same as that of the credential), and
- A boolean flag to denote whether the record is revoked (verifiers can revoke records they create).
If the registry is persistent, then this is information that is recorded on-chain about a successful verification. If the registry is not persistent, then the data in a Verification Record is still emitted as an event upon successful validation. No additional information about the underlying credential, issuer, subject, or verifier is contained in the Verification Record.

Verification Records allow counterparties, DeFi pools, Dapps, and tokens to query whether a particular address has had a given claim -- KYC/IDV, accredited investor status, etc. -- verified before or during a transaction, without leaking Personally Identifiable Information (PII) about the queried address. Verification Records also enable audit and forensics sequences, discussed below.


###
Expiration and Revocation
[](#expiration-and-revocation)
Verification Records have expiration times which may occur sooner than the expiration of the original Verifiable Credential. In other words, verifiers may enforce their own expiration logic in order to force re-verification, or to create more conservative time windows for automatic credential expiration. However, the Verification Record must never have an expiration time that exceeds that of the original Verifiable Credential.

Verification Records may also be revoked by the verifier address that submitted the original Verification Result that led to a Verification Record’s creation. This is appropriate for verifier-submitted results, but may not be effective for subject-submitted results, as subjects may submit to registries unknown to verifiers.

A subject may also remove any persistent Verification Records that have been associated with its own subject address. That is, an individual credential holder who has been verified has the ability to execute the removal of a Verification Record about his/her own verifications. Note that data is never truly removed from on-chain storage, but the act of executing a removal transaction will invalidate and zero-out the record from subsequent blocks in the chain. A specific registry implementation may remove this capability based on the contract logic; for example, a specific KYC registry may prevent removal of verifier-revoked credentials, or resubmission of any existing equivalent credential. These decisions are implementation-specific.

###
Verifier Management
[](#verifier-management)
VerificationRegistry contracts manage trusted verifiers. Specifically, registries map public verifier chain addresses to VerifierInfo objects that contain the following on-chain information:

- A Decentralized Identifier (DID) for the verifier
- A human-readable name for the verifier
- A URL pointing to more information about a verifier
- A public on-chain signer address which corresponds to a private key that the verifier uses to sign its VerificationResults
This information about a verifier is persisted on-chain. The registry contract's owner (by default its deployer) has the ability to add, update, and remove verifiers by supplying new VerifierInfo objects.

The registry contract’s owner is therefore critically important -- the owner must vet and trust verifiers to execute proper verifications, and update or remove them if necessary. The owner is also capable of replacing itself with a new owner address.

The registry provides external-scoped accessor methods to obtain VerifierInfo for any configured verifier given a verifier's public chain address. The issuer associated with a particular credential and verification is not persisted or included in either the result or the record, but the verifier associated with a verification is persisted and is publicly visible. Verifiers may be contacted out of band by authorities in order to provide further data associated with a particular verification which they may persist privately.

##
Verifier Submission Patterns: Live or On-Chain
[](#verifier-submission-patterns-live-or-on-chain)
A verifier backend may submit a Verification Result or subset of its contents directly to a dapp or other relying smart contract, expressed as a transaction in the appropriate VM in principle, which the relying smart contract could verify without executing. This transaction could even be custodied and submitted by the wallet itself, if there were reason to justify the added UX complexity.

Another verifier-submission pattern involves an additional actor, an onchain-registry, which one or more smart contracts read asynchronously. In this version, a verifier executes the off-chain verification of a credential and then registers a risk-minimized subset of the Verification Result object called a Verification Record in some form of on-chain registry. A relying decentralized application or other client never accesses the credential or the Verification Result directly, instead relying on a trusted intermediary to translate verbose results to opaque Verification Records and maintain them on-chain. While this adds another indirection and on-chain risks and costs, its persistence on-chain simplifies on-going or multi-audience querying, as one or more smart contracts can execute a view (no cost) function on the registry contract over time (e.g. at each additional transaction, to check non-revocation).

This latter pattern supports granular "whitelisted account" and “addresses with attestations” approaches. It is available to web apps, dapps, and other smart contracts. It also integrates seamlessly with ERC-20 and similar standards since no custom functions are necessary.


###
Example Permissioned Pool or Token
[](#example-permissioned-pool-or-token)
The following sequence illustrates an example of the Verifier Submission Pattern in a hypothetical permissioned pool example:


###
Example Verification within an IDV and KYC Process
[](#example-verification-within-an-idv-and-kyc-process)
The permissioned example could also be embedded within an IDV/KYC sequence. In this example, an identity verifier might not only generate a Verifiable Credential attesting proof of KYC, but also register verification of the attestation with a registry contract.

For example:


##
Subject Submission Pattern
[](#subject-submission-pattern)
In the subject submission pattern, a dapp still utilizes an off-chain verifier for verification, but the verifier does not directly register the result with a smart contract. Instead, the verifier returns the Verification Result and its signature to the subject, and the subject passes the Verification Result and verifier’s signature to a smart contract's custom function, which can validate the result in addition to other logic in the same transaction.

This approach enables a dapp to execute verification and other contract logic in a single transaction and requires no fees from a verifier. However, trade-offs exist: In ethereum, it requires custom function usage so does not integrate seamlessly with existing transfer standards (such as ERC20), requires contract inheritance in order to ensure that the subject is the caller of the result validation function rather than the contract being the caller (to prevent replay attacks).


###
Example Risk Score Subject Submission
[](#example-risk-score-subject-submission)
The following example illustrates subject-submitted verification results for the sake of providing hypothetical credit/risk scoring to a lending pool smart contract:


In this hypothetical example, a contract in the lending pool could inherit the registry contract (or implement the registry interface) to invoke internal verification registration methods in the same transaction as the borrow logic. This ensures that the caller is the subject (represented by the dapp) as opposed to the inheriting contract becoming the caller. There is no persistence of credit verifications in this example.

###
Subject Submission Pattern Risks and Mitigations
[](#subject-submission-pattern-risks-and-mitigations)
Verification registries that support both subject-managed verification results and verifier revocation must ensure they don't inadvertently allow subjects to override a verifier revocation through resubmission, as demonstrated in the following flow:

- Subject registers verification result
- Verifier revokes
- Subject deletes, then resubmits original verification result (causing the record to appear unrevoked)
This risk can be avoided through policies enforced in the implementation of a registry that supports verifier revocation:

- Disallow subject submission
- Allow subject submission, but disallow subject deletion
- Disallow deletion of a revoked record
This can also be mitigated by the verifier applying an aggressive expiration for subject-held verification results, narrowing the window of effectiveness of such a threat.

##
Storage Patterns
[](#storage-patterns)
Upon generating a Verification Record, a contract may follow one of two storage patterns: (a) in-contract storage, and (b) off-chain storage with optional oracle integration.

In-contract storage involves persisting Verification Records in a manner that associates verifier addresses with all of the Verification Records that a verifier's address has approved, and maps subject addresses to all Verification Records associated with that subject -- exposing no PII or verification result payloads, but providing proof of verification types, timestamps, and expirations that the subject has successfully passed.

An example that executes this pattern is the [VerificationRegistry.sol](https://github.com/circlefin/verite/blob/main/packages/contract/contracts/VerificationRegistry.sol) contract.

The off-chain storage pattern involves no such on-chain storage. External oracles and chain scanners can observe the Verification Record events emitted by a contract, but there is no persistence of records on-chain.

This storage option may be appropriate for transient verifications with short-lived credentials (such as the risk score example above). For off-chain storage to support the Verifier Submission sequence, an oracle could be implemented to access accurate off-chain verification information.

##
Auditing and Forensics Sequences
[](#auditing-and-forensics-sequences)
No personal data or credentials are stored on-chain. Instead, Verification Records are used as references to a verifier where additional information about a verification may be found. The verifier’s private off-chain information includes, among other data, a record of the issuer of a subject's credential.

So while basic Verification Record information associated with addresses is public and can be correlated to transaction activity, and callers can verify that an address has a particular claim, an enforcement authority interested in obtaining PII about a subject would need to take the additional steps of gaining the issuer identifier from the verifier and then contacting that issuer directly.

A provable yet privacy-preserving audit path is simple. However, a deeper forensics path that accesses personal data is intended to be possible only for law enforcement with appropriate authority, and not possible for others to execute.

##
Privacy Considerations
[](#privacy-considerations)
While verification records may be extended to store additional properties, implementors should ensure data stored on-chain does not contribute to the re-identification of the individual to which it applies.

In general, this requires registry design to target clear use cases determined by stakeholders, without reliance of on-chain data for additional optionality (relying instead on offchain protocols when this is needed).

##
Alternative Patterns and Future Work
[](#alternative-patterns-and-future-work)
These patterns are not definitive, they serve only as initial examples to illustrate integrating decentralized identity standards -- Verifiable Credentials and Presentation Exchange usage -- into smart contracts and DeFi programs.

Future directions to explore include verification in-contract on chains that are capable of executing verifications efficiently and economically, deeper filtering of credentials rules in-contract instead of solely in verifier logic, alternative patterns and registry designs for specific schemas and credential types, and usage of technologies such as zero-knowledge proofs to encapsulate credential data on-chain for selective disclosure scenarios.

The source code examples referenced here are also not intended for production use. The implementations exist to illustrate the design patterns, not to support commercial production deployment.

##
Reference Implementations and Test Suite
[](#reference-implementations-and-test-suite)
A reference implementation smart contract that executes Verifier Management, Verification Result validation, and Verification Record persistence is the IVerificationRegistry interface and its VerificationRegistry.sol implementation:

[https://github.com/circlefin/verite/tree/main/packages/contract/contracts](https://github.com/circlefin/verite/tree/main/packages/contract/contracts)

This registry implementation is used by two examples, PermissionedToken.sol (a verifier submission example) and ThresholdToken.sol (a subject submission example).

The test suite leverages hardhat and waffle, and is located in the ‘test’ subdirectory of the above repo:

[https://github.com/circlefin/verite/blob/main/packages/contract/test/VerificationRegistryTests.ts](https://github.com/circlefin/verite/blob/main/packages/contract/test/VerificationRegistryTests.ts)

An example Dapp with MetaMask integration that uses the ThresholdToken is available here:

[https://github.com/circlefin/verite/tree/main/packages/e2e-demo/components/demos/dapp](https://github.com/circlefin/verite/tree/main/packages/e2e-demo/components/demos/dapp)

Updated 6 months ago


URL: https://developers.circle.com/verite/docs/credential-state
Title: Credential State
Description: undefined
Keywords: undefined


URL: https://developers.circle.com/verite/docs/consumer-overview
Title: Overview
Description: undefined
Keywords: undefined

##
Landscape
[](#landscape)
"Knowing your customers" in the traditional banking sense is a tall order, and a one-size-fits-all regulatory approach that can be a huge burden on crypto businesses, that often need 1/10 of the certainty afforded by KYC'ing all their customers or counterparties and are loathe to assume huge costs and liabilities just to do it. Add to that rising regulatory risks and liabilities... and you've got every reason to let a trusted intermediary KYC your customers for you. Verite lets you get just the bare minimum you need from a more dedicated identity verifier, with a verifiable, trustless passing of basic facts. If you ever need to prove exactly when and how you approved a blockchain address or a wallet to interact with you... you've got timestamped, tamperproof, receipts. As ironclad as on-chain data!

Some key questions to ask yourself:

- What use-cases do you want to start with? [Overview of use-cases](#use-cases)
- What is the bare minimum you need to know to allowlist/grant access to an address or a wallet? [Data minimization](#data-minimization)
- What kind of wallets is it strategic for you to support? [Wallet Overview](#wallet-overview)
- What does "reliance" mean, given the above? [Liability and Auditing Considerations](#liability-and-auditing-considerations)
- Is the issuance/wallet-interaction something you want to "build or buy"? [Architectural options](#architectural-options)
##
Use-Cases
[](#use-cases)
In the short-term, the following use-cases are going live in at least one product offering in 2022:

- VC-based gating of KYB status and domicile (US y/n) for a "company wallet" (i.e., Compliant and Auditable Institutional DeFi)
- VC-based gating of Investor Accreditation for a "company wallet" (US-domiciled wallets)
The following are in research and design phase, being co-developed by participants and adopters:

- KYC'd individual wallet (custodial and self-custodial)
- Non-US KYB status (including interoperability with GLEIF and FincID credentialing)
- FATF-compliant reporting for custodial-to-custodial transactions (incl custodial-to-noncustodial transactions)
- Currency controls/FX reporting
- Verifiable credit-assessment and forensics-sourcing
##
Data Minimization
[](#data-minimization)
In many use-cases, the least you can get away with knowing about an address is its:

- functional jurisdiction (tax residence, domicile, etc is often used as a proxy for this)
- liability (personal wallet or "company wallet", i.e. funds and liability belonging to a legal person)
- control model (including identity assurance, possibility of recovery, hardware/software security, and other wallet capabilities)
None of these are PII, and if sufficiently coarse-grained, none of these compromise on the anonymity central to the ethos of cryptocurrency and web3. Verite exists to make these kinds of facts verifiable, yet still under direct user control with a minimum of issuer involvement or surveillance risk.

If you need anything finer-grained than the above, you inch towards the risk of deanonymizing the wallet/user, leaking personal information to be snatched up by prying scammers or spammers, etc. To get into that more dangerous territory, you will have to proceed to request further information from the wallet and/or issuer; this can entail a more niche zero-knowledge protocol or circuit/setup, or a direct channel to the issuer and authorization from the user to inquire about sensitive data they retain.

##
Liability and Auditing Considerations
[](#liability-and-auditing-considerations)
The best way to think through reliance from a legal and compliance point of view is this: what are you inferring or taken as evidenced, once you've verified a valid (non-revoked, non-suspended) Verite VC from a trusted issuer? You are responsible for all that you inferred or assumed, and the issuer is responsible for only what is in the credential. For this reason, it is highly recommended that you log the verification interactions carefully, retaining at least the issuer's identifier and whatever the issuer might need to produce the VC itself, for forensic purposes.

A note on "Uptime": if you are consuming credentials that may need to be revoked quickly, such as credentials reflecting the current status of real-time monitoring of data sources like OFAC and PEP lists, you may need to check the "status" of a credential far more frequently than you verify the credential itself. Luckily, this status can be expressed as a web URL, an on-chain registry, and/or as a value queried via on-chain oracle; unlike the credential itself, no fancy cryptography is needed for these more frequent checks.

##
Wallet Overview
[](#wallet-overview)
Take a minute to ask yourself some difficult strategic questions:

- Depending on your business model, you may be more interested in supporting "identity wallets" (applications for signing contracts, handling sensitive identity documents, providing verifiable consent, etc) or in supporting "cryptocurrency wallets" (that authorize transactions on cryptocurrency blockchains and "web3" applications).
- You might be interested in supporting only cryptocurrency wallets with full "identity wallet" functionality, or interested in separating the two concerns in two distinct pieces of software. (Our sample implementation may provide a useful starting point for this latter approach! A white-label browser-extension for identity functionality to complement a cryptocurrency wallet is coming soon).
- Retail wallets tend to have long, slow upgrade cycles and governance processes. Conversely, many companies contract out to wallet firms to provide highly-customized "provisioned wallets" to their employees for managing company funds. As Verite capabilities are standardized and rolled out as common APIs, these may be a better match for "testing the waters"
- Depending on which exact credentials you issue and your risk tolerance, you might have different requirements for identity-assurance, sybil-resistance/uniqueness, deduplication, or liveness/biometric binding. I.e., if your use-case requires you to be certain that the authorized employee is authorizing each transaction of a company wallet, you may want to limit your support to wallets with built-in per-transaction or per-session biometrics, etc.
##
Architectural options
[](#architectural-options)
At present, the two main options to consider are whether you want to attest to the controller of an blockchain address or to the controller of a specific wallet, which may control multiple addresses in addition to a DID (wallet identifier). For more information, read the [identifier scheme considerations](/verite/docs/identifier-methods#wallet-based-versus-address-based-holder-identification-schemes) and compare the [address-bound credential exchange flow](/verite/docs/verifying-credentials#address-bound-verification-flow) and the [wallet-bound credential exchange flow](/verite/docs/verifying-credentials#wallet-bound-verification-flow).

For address-bound flows, please see [Wallet Connect request presentation](/verite/docs/integrating-via-walletconnect#presentation) for implementation guidance and code examples, and [Wallet Connect issuance](/verite/docs/integrating-via-walletconnect#issuance) for reference.

For wallet-bound flows, please see the pages on [wallet-bound credential exchange flow](/verite/docs/verifying-credentials#wallet-bound-verification-flow) for implementation guidance and code examples, and [wallet-bound issuance mechanics](/verite/docs/issuance) and [wallet-bound issuance service setup](/verite/docs/issuer-setup) for reference.

Once you have clear your use-cases and your high-level architecture, you arrive at the build-or-buy decision. Verifying Verite credentials at scale and in production can be a massive undertaking if you're not familiar with OIDC token, authorization issues, applied cryptography, or other related problems in web engineering. That said, they are just signed JSON! If you're considering building a verifier for your environment, start with the tutorials and documents that guide you through the process in the ["For Developers" section of this site](/verite/docs/getting-started).

If outsourcing this layer is more appealing, there are already a number of verifiers that offer the verification and validation/initial-processing of Verite credentials as a service, which can deliver definitive answers about which wallets/address to sort into which buckets or trust-levels over APIs, oracles, and/or on-chain registries. Some of these are even free to use in production, at scale!

Updated 6 months ago


URL: https://developers.circle.com/verite/docs/integrating-via-walletconnect
Title: Integrating Via WalletConnect
Description: undefined
Keywords: undefined

##
Introduction
[](#introduction)
One of the core difficulties in the web3 stack is that signing mechanisms and wallet-dapp interfaces are notoriously understandardized, and in many case numerous competing standards and interfaces persist for years, leading to a
splintered user experience and a piecemeal security model. On top of adding friction and heightened risk of end-user error, requiring users to identify their wallets and click different buttons to go through different flows
per-wallet is also of an anti-pattern in privacy terms, since it makes"fingerprinting" accounts substantially easier with just a few data points or analytics primitives.

WalletConnect, a relay network built on open standards incubated and specified at the [Chain Agnostic Standards
Alliance](https://github.com/chainagnostic/CASA/), is often thought of as a convenience for developers of dapps who don't want to integrate wallet
interfaces one-by-one, or a way for wallets to pick up "back-end" capabilities without having to stand up their own end-to-end web2 routing and security infrastructure. It establishes a websocket interface mutually between wallets
and dapps, which both abstracts out some of the HTTPS complexity, but also creates herd privacy in making a large portion of today's web3 traffic over HTTPS wires uniform and more opaque. Better living through standardization!

One of the most vexing problems in the adoption of verifiable credentials in the web3 space has been the chicken-and-egg problem of VC exchange protocols-- few wallets have been willing to stick their neck to dogfood a new protocol with new attack vectors and liabilities. Add to this the lack of demand-- where are the applications demanding a specific form of verifiable credentials or a specific protocol for issuing and presenting them?

Verite is happy to sidestep both of these traditional "cold-start" problems by introducing a "minimum viable protocol" through WalletConnect, inheriting all of its privacy and security features so that all wallets and dapps supporting WalletConnect V2 can add on support for verifiable credentials with two tiny code-changes.

##
Feature Discovery
[](#feature-discovery)
WalletConnect V2 introduces a lot of scaffolding for multi-chain and multi-Virtual-Machine features on both wallets and decentralized applications. This presents a similar cold-start problem-- how to let dapps and wallets communicate to each other their features and interfaces efficiently in-band? The solution lies in adding a rich and expressive "feature discovery" step to the wallet authorization flow, known to end-user as "the connect wallet button".

At a high level, a dapp finds out at time of connection which features (expressed as RPC methods and RPC notifications) the wallet supports and grants the dapp access to, by requesting authorization and receiving back granular permissions. This feature-discovery method is idempotent, and can be called again and again in the background of a session to enable progressive authorization flows, which are increasingly being promoted as best practice by browser security experts and protocol designers.

Simply put, a dapp asks a wallet: "Can we exchange VCs? Are you willing to store
VCs I give you?" A wallet can answer, "No", "Yes and yes", or "Yes and maybe later".
The dapps can always ask again later after establishing trust in other ways.

Feature discovery basically occurs as a back-and-forth negotiation of scope objects expressed as JSON arrays, since the Ethereum ecosystem and most other major blockchains make extensive structural use of JSON-RPC for packets between dapps, wallets, and nodes. These scope objects bear some similarity to the JSON scope objects used by the [Rich Authorization Request](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-rar) (RAR) system in some contemporary Web2 protocols; RAR is also a building-block of the [GNAP](https://datatracker.ietf.org/wg/gnap/about/) protocol. That said, they are also very much tailored to the cross-chain community research at [CASA](https://github.com/chainagnostic/CASA/), which maintains a registry of [namespaces](https://namespaces.chainagnostic.org) to make cross-chain engineering adoptable and ergonomic.

The protocol and the syntax for this object-based negotiation are specified in one of the Chain-Agnostic Improvement Proposals, [CAIP-25](https://chainagnostic.org/CAIPs/caip-25); optional extensions to the protocol are specified as independent CAIPs like [CAIP-171](https://chainagnostic.org/CAIPs/caip-171) and [CAIP-211](https://chainagnostic.org/CAIPs/caip-211). This lightweight JSON-RPC-based protocol outlines a flow where
a dapp can prompt a wallet to factor in policy and/or user input in a granular authorization of wallet features and networks in the browser. This protocol is specified more abstractly than how it is implemented in the WalletConnect relay network, since parity is maintained with a relay-free implementation used by browser-embedded (i.e. "extension") wallets, including the MetaMask Snaps program.

###
Examples
[](#examples)
See [https://chainagnostic.org/CAIPs/caip-25#request](https://chainagnostic.org/CAIPs/caip-25#request)

TODO - Verite-specific verbose example with wallet scope object and current WalletConnect syntax

##
Issuance
[](#issuance)
The complex part of issuing a verifiable credential is negotiating between issuer and end-user (via their wallet and/or dapp interfaces) what the contents and preconditions/inputs to a credential are. In the case of Verite, these are
relatively simple, and the negotation can be as simple as a wallet downloading the issuer's [Credential Manifest](/verite/docs/message-formats-samples#credential-manifest) and reducing it down to a [Credential Application](/verite/docs/message-formats-samples#credential-application) with any inputs provided. In a Verite use-case, this can be simplified even more-- a wallet can simply hard-code the [Credential Application](/verite/docs/message-formats-samples#credential-application) if no inputs are required, and dereference the [Credential Manifest](/verite/docs/message-formats-samples#credential-manifest) from a well-known endpoint to confirm it will still be enough to get the credential it is expecting in return.

The request for a credential, then, actually comes from the wallet to the dapp, accompanied by a [Credential Application](/verite/docs/message-formats-samples#credential-application) object and, optionally, an ordered array of signature formats the wallet supports from most to least preferred. This latter array is optional, and is largely supported for interoperability purposes in web2/web3 hybrid applications; most dapps and wallets will default to VC-JWTs for the foreseeable future, with the possible exception of Zero-Knowledge wallets and dapps which might prefer more complex signature formats where mutually supported.

Once the issuer has parsed the application and found it matches their expectations/needs, they simply return a VC as a JSON object.

###
Examples
[](#examples-1)
See [https://chainagnostic.org/CAIPs/caip-169#issue](https://chainagnostic.org/CAIPs/caip-169#issue)

TODO - Verite-specific verbose example with expanded [Presentation Application][]

##
Presentation
[](#presentation)
As above, the complexity in presentations is abstracted by the JSON objects exchanged; in the Verite use-case, dapps can essentially hard-code the [Presentation Definition][] object that they use to request a verite credential,
since the schemata are versioned and business logic is simple. The optional additional parameters for "holder-binding" do not apply to address-bound credentials, since Wallet Connect has already authenticated the user's wallet and the credentials are already bound to that wallet; in cases where the credential is issued to a DID or other identifiers, these options may be necessary to test liveness, prove continuity of wallet control, etc. These
extensions are beyond the current scope of Verite but may be central to future use-cases.

###
Examples
[](#examples-2)
See [https://chainagnostic.org/CAIPs/caip-169#present](https://chainagnostic.org/CAIPs/caip-169#present)

TODO - Verite-specific verbose example with expanded [Presentation Submission][]

##
Extensibility: Storage
[](#extensibility-storage)
In the authorization example above, you may have noticed the slippage between"wallet has the capability to store a VC" and "wallet authorizes this dapp to issue it VCs it will store". Dapps are encouraged to request authorization
progressively after explaining to the end-user the contents and utility of a given VC, after escalating from "connect wallet" to "create account", etc.

The actual storage of a VC is meant to be abstracted away from the dapp issuing or requesting the credential for both privacy and security reasons. The actual storage mechanism used can be encrypted or plaintext, local or remote, centralized or distributed, whole or sharded. Since wallets and architectures vary widely, this very broad and complex topic is entirely out of scope of Verite.

##
Extensibility: OIDC
[](#extensibility-oidc)
Support for OIDC servers, particularly as OIDC evolves beyond its third-party cookies and postMessage dependences, is a complex topic well beyond the current scope of Verite. That said, Verite credentials might have a lot of utility beyond cryptocurrency, such as in Web2 applications, particularly for eCommerce and reputation systems looking for more progressive trust establishment and more secure pseudonymity. As OIDC systems incrementally support the presentation of verifiable credentials, the [wallet_creds_metadata](https://chainagnostic.org/CAIPs/caip-169#wallet-metadata) method may become a useful mechanism for legacy compatibility, presenting Verite tokens or other verifiable credentials in places where previously you could only share verified claims by "Logging in with Hooli."

Updated 6 months ago


URL: https://developers.circle.com/verite/docs/governance-overview
Title: Governance Overview
Description: undefined
Keywords: undefined

The design of Verite was optimized for the privacy at various levels, but it was also optimized for flexibility for each actor in the system, to support many different kinds of business arrangements and infrastructural investments. Fundamentally, Verite only thrives (and justifies its complexity) if an organic and resilient ecosystem arises where actors can specialize, discriminate, and profit. Like all ecosystems, we need governance to balance flexibility against liability and reach against fairness. This overviews components of the governance we see as necessary.

##
Why do we need governance?
[](#why-do-we-need-governance)
In a network with multiple issuers, every VC issued should be on equal footing. This means a VC can be presented across the network with the same level of confidence and veracity, regardless of who issued it and who verifies it

Without common ground about the meaning and value of each VC, the Verite ecosystem will only be as strong as its weakest Issuer or Verifier:

- The network will fragment, i.e., verifiers will only accept VCs from certain issuers
- Fragmentation of reliance means VCs become less portable – which is one of the most important factors aspects of their value proposition
- This will also be a huge barrier to adoption, as widespread acceptance of VCs will be difficult to achieve (or the network will centralize around a single Issuer)
##
Current State: Boot-strapping an Ecosystem
[](#current-state-boot-strapping-an-ecosystem)
Verite is currently orchestrated by Circle in close collaboration with its initial implementers, and grounded in feedback from early evaluators who commit seriously to considering our work to date. This is “strategically centralized,” in the sense that Circle is currently the hub at the center of major technical and business planning with implementers, but the multi-lateral collaboration has already started in Working Groups, bound by multi-lateral IP/NDA agreements. One such working group is actually focused on roadmapping and elaborating everything that follows; to get involved, reach out to Circle.

##
Target-State: Three Interlocking Governance Mechanisms
[](#target-state-three-interlocking-governance-mechanisms)
In a scaled ecosystem with multiple issuers and verifiers of differing scales and foci, Verite needs an explicit, detailed governance framework and rulebook that defines how to create, update, and enforce rules and standards that bind the network together legally and technically. These can be thought of as “by-laws” that structure how Verite expands, as community-driven governance gets formalized.

This rule-based governance will be key to making Verifiable Credentials, and the business relationships they represent, portable and interoperable, and thus to achieving mass adoption.

Verite governance will have three primary components: (1) the Governance Framework, (2) the Rulebook, and (3) the Network Utilities.


##
The Governance Framework
[](#the-governance-framework)
The Governance Framework refers to a standing body of humans representing organizations (companies, non-profits, and/or DAOs), who together make decisions about Verite. This includes an authoritative Verite Governance Board and the more dynamic Verite Working Groups that spin up and wind down on a “project basis” to add technical, business, product, and/or legal features to Verite as a whole.

The Verite Governance Board should authorize new Working Groups, which produce standards and technical artefact. One such working group is already working on a Rulebook (detailed below) that governs and binds key ecosystem participants. When Working Groups present upgrades to an already-functioning Verite system in production, this can create breakage (technical and economic) for participants; for this reason, the Governance Board’s primary duty is to make decisions about these changes. Specifically, the Governance Board votes to accept changes to standards and rules that protect the technical and regulatory integrity of the Verite ecosystem.


####
Detailed Remit
[](#detailed-remit)
The governance framework will define (not exhaustive):

- The legal structure of the Verite consortium
- The eligibility criteria for participating in the Verite consortium
- How IP / CLAs / open source is managed
- How working groups are structured, who can participate, and what do they do
- How standards are created and agreed on
- How updates and extensions to the standards are agreed on
- How the rulebook is created and agreed on
- How the rulebook is updated and agreed on over time
- How decisions are made, what decisions are made, who can make decisions
- Onboarding and offboarding policies and procedures
- The consortium’s business model – pricing, fees, expenses etc
##
Network Utilities
[](#network-utilities)
The “Network Utilities” that power Verite provide common services to all participants and end-users in the Verite ecosystem.

The first and most structural network utility is the Trusted Identity Registry. The Trusted Identity Registry defines which Issuers and Verifiers can be trusted to adhere to Circle Verite Standards. It functions as an “key directory” providing authoritative key material to prevent phishing or impersonation within the system. As Verite scales up, it will also include up-to-date information on conformance testing, to facilitate real-time decision making about the trustworthiness and roadworthiness of each actor’s implementation and credentials. Circle reserves the right to remove actors from the Registry if they have not signed the Rulebook or have been judged by the community not to be honoring it.

####
Detailed Remit: Trusted Identity Registry
[](#detailed-remit-trusted-identity-registry)
Circle will build, maintain, and publish the Trusted Identity Registry that includes Issuers and Verifiers which have applied for and been approved by the Verite Governance Board to join the registry.
Entities in the registry adhere to technical, operational, legal, regulatory, and compliance standards defined by Verite Governance Board.
Verifiers and Issuers in the registry must sign a Verite Rulebook which defines the rights, obligations, standards, reps & warranties, etc that they must adhere to.
The rulebook dictates the conditions under which a VC can be issued, verified, and or/revoked
The Verite Governance Board can elect to remove entities from the registry if they fall out of compliance with the rulebook.
Each one is identified by a Decentralized Identity (DID) from a supported DID method, that is listed in a machine-readable registry published on Github and in an Ethereum smart-contract for on-chain use-cases.

##
Verite Rulebook
[](#verite-rulebook)
As the network expands and hits the market “in production”, all of this will have to be formalized: not just the governance between issuers and verifiers, but also the liabilities and obligations from issuer to end-user to consumer/relying party. These various kinds of relationships will be circumscribed and specified by rules, the sum of which can be periodically published as a versioned Verite Rulebook.

This rulebook structures the relationships, expectations, and limits of all the actors in the system. Most crucially, however, it governs and binds Issuers and Verifiers, who must sign a contract to uphold it and stay within its limits to stay in the Trusted Identity Registry over time.

####
Detailed Remit
[](#detailed-remit-1)
The Verite Rulebook will cover a number of different topics, including but not limited to:

- Definitions of who can and cannot be issued a verifiable credential
- Regulatory standards that must be adhered to for issuing different types of VCs
- Standards/rules for the processes an Issuer must follow to perform KYC/B and Accredited Investor checks
- Standards/rules for the processes an Issuer must follow to perform to issue other VC types
- Standard/rules for the processes a Verifier must follow to verify VCs
- Rules for how long should VCs be valid for and for how often should KYC/B be redone on an existing holder.
- Standards/rules for when a VC should be revoked, the process and SLA for revoking it, and how others are notified
- Defines manager(s) of the Trusted Identity Registry, the eligibility criteria for inclusion, criteria and processes for removal for the registry, etc.
- Defines how liability is allocated across various participants and network utilities
- Define who has recourse and whom do they have recourse against and the terms of that recourse
- Representations and warranties of participants that signers commit to uphold
- Technical standards which, in being adhered to, sustain and protect the integrity of the ecosystem
- Financial requirements of the issuers/verifiers
- SLAs for the network utilities (e.g. uptime, resiliency, latency, etc)
Updated 5 months ago


URL: https://developers.circle.com/verite/docs/issuing-credentials
Title: Issuing Credentials
Description: undefined
Keywords: undefined

Identity providers can issue portable and decentralized Verifiable Credentials to their users. Verifiable Credentials represent provable claims associated with an identity. They can represent any number of claims -- proof of KYC, credit score, etc.

Verifiable Credentials are issued to a recipient-controlled identifier (such as a DID), which increases the ability for the recipient to use the credential in different contexts through [holder/subject binding techniques](https://identity.foundation/presentation-exchange/#holder-and-subject-binding) that prove control over an identifier.

Collecting that identifier in a secure and convenient way for the user requires different interaction flows; the following pattern is one way an issuer can enable this process for an authenticated user.

##
Issuance Flow
[](#issuance-flow)
In this example, an individual's financial institution offers the ability to request a KYC or credit score credential to be held in the individual's mobile identity wallet. The flow consists of the exchange of three distinct JSON objects:

- A Credential Offer object published by the Issuer that describes the credential and how to apply for it
- A Credential Application object submitted by the wallet, containing information that, when appropriately validated and/or verified, will form the contents of those credentials, and
- A Credential Fulfillment envelope returned by the Issuer that contains the final credential(s) to be stored by the wallet and presented later to unknown future verifiers.
###
Sample JSON objects
[](#sample-json-objects)
- [Credential Offer](/verite/docs/message-formats-samples#credential-offer)
- [Credential Manifest](/verite/docs/message-formats-samples#credential-manifest)
- [Credential Application](/verite/docs/message-formats-samples#credential-application)
- [Credential Fulfillment](/verite/docs/message-formats-samples#credential-fulfillment)
###
Wallet-Bound Credential Issuance
[](#wallet-bound-credential-issuance)
In more detail, here is a flow for issuing a DID-bound (i.e., identity wallet-bound) credential to an identity wallet:


- A User navigates to the Issuer site
- The Issuer presents a QR code.
- User scans the QR code with their wallet.
- Wallet parses the QR code, which encodes a JSON object with a challengeTokenUrl property.
- Wallet performs a GET request at that URL to return a Credential Offer, a wrapper around a [Credential Manifest](https://identity.foundation/credential-manifest/#credential-manifest-2), with three supplementary properties:
- The issuer DID.
- A URL for the wallet to submit a Credential Application.
- A challenge to sign.
- The wallet prompts the user to proceed. The Credential Manifest includes descriptive properties, e.g. in the Verite demo app a title and description of the credential are shown.
- Once the recipient proceeds, the wallet prepares a signed [Credential Application](https://identity.foundation/credential-manifest/#credential-application),
- If the wallet doesn't have a DID, it generates one.
- The wallet creates a Credential Application for the DID.
- The application is signed along with the challenge in the Credential Offer
- The Verite library exposes a convenience method createCredentialApplication for this purpose.
- Wallet submits the Credential Application to the URL found in the Credential Offer.
- The Issuer creates a Verifiable Credential and returns it to the wallet as a [Credential Fulfillment](https://identity.foundation/credential-manifest/#credential-fulfillment).
- Wallet persists the credential.
###
Address-Bound Credential Issuance
[](#address-bound-credential-issuance)
In the case of credentials bound to a blockchain address, issuance is necessarily bespoke to a given wallet implementation, as few cryptocurrency wallets have standardized verification credential capabilities or even standardized secure storage. In many use-cases, address-bound credentials are only recommended for addresses that are securely limited to a trusted wallet (whether hardware-secured, multi-signature, or EOA) whose signing control cannot be transfered or exfiltrated.

As wallet standards emerge, guidance and potentially even full interface specifications will be forthcoming for how wallets can receive VCs, store them, and sign over them in live presentations.

For now, Verite recommends creating verifiable and replayable receipts of all off-chain wallet interactions (including issuance and delegated signing/presentation operations). The Sign-In With Ethereum specification ([EIP-4361](https://eips.ethereum.org/EIPS/eip-4361#example-message)) is one such way of harmonizing on off-chain behavior, an approach being extended to other VMs in the [CASA](https://github.com/chainAgnostic/CASA) by [CAIP-122](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-122.md). Receipts of these events can be logged as [CACAO](https://github.com/ChainAgnostic/CAIPs/blob/c8d8ee203625ea622bd15c42b2493116712dfaf3/CAIPs/caip-74.md)s, for uniform verifiability.

Updated 5 months ago


URL: https://developers.circle.com/verite/docs/getting-started
Title: Getting Started
Description: undefined
Keywords: undefined

Verite has provided several interactive demos as well as sample projects to showcase some use-cases of the Verite standards. The code is freely available on [Github](https://github.com/circlefin/verite). To run all of the demos, you will need code found in two repositories: verite and demo-wallet.

- Setup and run Verite's [verite](https://github.com/circlefin/verite/blob/main/README.md)
- Setup and run Verite's [demo-wallet](https://github.com/circlefin/verite/blob/main/packages/wallet/README.md)
- Select a demo in verite to be guided through the steps
The verite repository also hosts several standalone code samples to assist in developing your own solution. Those samples are located at:

- packages/demo-issuer
- packages/demo-verifier
- packages/demo-revocation
Updated 6 months ago


URL: https://developers.circle.com/verite/docs/issuer-overview
Title: Overview
Description: undefined
Keywords: undefined

##
Landscape
[](#landscape)
Today's identity providers, identity verification services, banks, and other
core services that perform some degree of Know-Your-Customer (KYC) and/or
Know-Your-Business-Partner (KYBP) services on their customers operate in a
predomintrustantly issuer-centric, phone-home world: today's web [2.0] and
mobile-app ecosystems are almost 100% powered by API phone-homes and delegation
tokens. This is gradually changing, though: third party cookies are being
replaced by more nebulous (and platform-centric) tracking models, reducing the
centrality of identity providers in the web advertising ecosystem. In parallel,
user-centric approaches are increasingly putting cryptocurrency private keys and
sensitive identity data directly in user-controlled wallets. The role of an
identity provider is shifting, along with their phone-home mechanisms, user
experience expectations, and business models. The next big thing is portable
identity, user-controlled sharing, and trustless, universally-verifiable tokens.
Verite provides a standards-driven approach for empowering your end-users
without assuming new risks and liabilities.

If you want your end-users to be able to present a [revocable,
tightly-controlled] "badge" that proves them to be your customers anywhere such
a badge grants them access to better products and validated-customer prices, you
want to be issuing them Verite credentials. Which exact form that takes depends
on the answers to a few key questions:

- What use-cases do you want to start with? [Overview of use-cases](#use-cases)
- What liability are you comfortable holding for the credentials you issue?
[Liability and Auditing
Considerations](#liability-and-auditing-considerations)
- What kind of wallets is it strategic for you to support? [Wallet Overview](#wallet-overview)
- Is the issuance/wallet-interaction something you want to "build or buy"?
[Architectural options](#architectural-options)
##
Use-Cases
[](#use-cases)
In the short-term, the following use-cases are going live in at least one product offering in 2022:

- VC-based gating of KYB status and domicile (US y/n) for a "company wallet"
(i.e., Compliant and Auditable Institutional DeFi)
- VC-based gating of Investor Accreditation for a "company wallet" (US-domiciled wallets)
The following are in research and design phase, being co-developed by participants and adopters:

- KYC'd individual wallet (custodial and self-custodial)
- Non-US KYB status (including interoperability with GLEIF and FincID credentialing)
- FATF-compliant reporting for custodial-to-custodial transactions (incl
custodial-to-noncustodial transactions)
- Currency controls/FX reporting
- Verifiable credit-assessment and forensics-sourcing
##
Liability and Auditing Considerations
[](#liability-and-auditing-considerations)
Each Verite credential type represents a different liability surface and
lifecycle. When designing your Verite engagement, consider the following
variables:

- What are the "semantics" (functional content) of each credential you are considering issuing?
- KYB credentials basically say "I know this wallet to be controlled by a
company that I have KYB'd according to the linked standard", no more and
no less
- Accredited Investor credentials are essentially the same, linking to a
different process definition standard
- FATF reporting requires legal entity information to be verifiable and
anchored in auditing and/or registration authorities; the liability
considerations are more complex for relying on these credentials than
issuing self-attested (non-repudiable) ones
- Which customers will you issue these portable credentials to?
- What can you safely assume about the wallets you are issuing to?
- How do you want to link your logging and record-keeping for these credentials
to your core identity systems and business model? We recommend storing a copy
of every VC you ever issue, in a way that can be easily queried at scale at
least by UUID or other unique per-credential key.
A note on "Uptime": particularly if you are issuing credentials that may need
to be revoked quickly, you should consider whether you are operationally
equipped to maintain (24/7, 365) monitoring of real-world data sources like OFAC
and PEP lists. Most IDV companies have some kind of realtime monitoring that
triggers "push" notifications to clients when statuses change, but with
portable, self-certifying credentials like Verite, you don't know whom to push
that notification to-- instead, you have to comply with the low
latency-tolerance of publishing credential status updates to "revocation lists".

##
Wallet Overview
[](#wallet-overview)
Take a minute to ask yourself some difficult strategic questions:

- Depending on your business model, you may be more interested in supporting
"identity wallets" (applications for signing contracts, handling sensitive
identity documents, providing verifiable consent, etc) or in supporting
"cryptocurrency wallets" (that authorize transactions on cryptocurrency
blockchains and "web3" applications).
- You might be interested in supporting only cryptocurrency wallets with full
"identity wallet" functionality, or interested in separating the two concerns
in two distinct pieces of software. (Our sample implementation may provide a
useful starting point for this latter approach! A browser-extension for
identity functionality to complement a cryptocurrency wallet is coming soon).
- Retail wallets tend to have long, slow upgrade cycles and governance
processes. Conversely, many companies contract out to wallet firms to provide
highly-customized "provisioned wallets" to their employees for managing
company funds. As Verite capabilities are standardized and rolled out as
common APIs, these may be a better match for "testing the waters"
- Depending on which exact credentials you issue and your risk tolerance, you
might have different requirements for identity-assurance,
sybil-resistance/uniqueness, deduplication, or liveness/biometric binding.
I.e., if your use-case requires you to be certain that the authorized employee
is authorizing each transaction of a company wallet, you may want to limit
your support to wallets with built-in per-transaction or per-session
biometrics, etc.
##
Architectural options
[](#architectural-options)
At present, the two main options to consider are whether you want to attest to the controller of an blockchain address or to the controller of a specificwallet, which may control multiple addresses in addition to a DID (wallet identifier). For more information, read the [identifier scheme considerations](https://verite.id/verite/patterns/identifier#wallet-based-versus-address-based-holder-identification-schemes) and compare the [address-bound credential exchange flow](https://verite.id/verite/patterns/verification-flow#address-bound-verification-flow) and the [wallet-bound credential exchange flow](https://verite.id/verite/patterns/verification-flow#wallet-bound-verification-flow).

For address-bound flows, please see [Wallet Connect issuance](https://verite.id/verite/developers/supporting-wallet-connect#issuance)for implementation guidance and code examples, and [Wallet Connect request presentation](https://verite.id/verite/developers/supporting-wallet-connect#presentation) for reference.

For wallet-bound flows, please see the pages on [wallet-bound issuance mechanics](https://verite.id/verite/developers/issue-a-verifiable-credential) and [wallet-bound issuance service setup](https://verite.id/verite/developers/issuer-setup) for implementation guidance and code examples, and [wallet-bound credential exchange flow](https://verite.id/verite/patterns/verification-flow#wallet-bound-verification-flow) for reference.

Once you have clear your use-cases and your high-level architecture, you arrive at the build-or-buy decision. If you want to issue the credentials you will be responsible for yourself, there are tutorials and documents to guide you through the process in the ["For Developers" section of thissite](https://verite.id/verite/developers/getting-started).

Updated 6 months ago


URL: https://developers.circle.com/verite/docs/how-to-use-verifiable-credentials-and-verite-to-build-an-off-chain-nft-allowlist
Title: How To Use Verifiable Credentials And Verite To Build An Off-Chain NFT Allowlist
Description: undefined
Keywords: undefined

While Verite can help solve significantly more complex challenges than managing an early access list (i.e., an "allowlist") for NFT projects, it seemed like a fun experiment to see how well Verite could handle moving the costly process off-chain while still maintaining data integrity. Before we dive in, let's talk about what an NFT allowlist is, how it is normally managed, and what the problems are.

###
What is an Allowlist?
[](#what-is-an-allowlist)
For many NFT projects, rewarding early supporters is crucial to the business model and community health. An allowlist helps give early supporters a distinct experience with unique terms and timing. By putting supporters' wallet address on a list that grants them early access to mint NFTs from the new collection, these supporters can avoid what's commonly referred to as "[gaswars](https://www.coindesk.com/learn/what-are-crypto-gas-wars/)". Gas wars happen when a popular NFT project drops and people (and bots) spend exorbitant amounts of gas on the Ethereum network to ensure their minting transactions go
through before anyone else's to avoid missing the narrow window of availability in an oversubscribed market. Needless to say, this negatively impacts all participants because it can price people out of the collection and force them to buy the NFTs on secondary market at a higher premium. On the Ethereum mainnet, gas fees have even spiked to higher than the mint prices of the NFTs! That's a hefty surcharge.

The allowlist concept lets people on the list mint for a period of time (normally 24 hours) before the public mint. This helps keep bots out of the mint, guarantees adequate supply to everyone on that list (or at least, guarantees each member on the list access to supply while it is adequate!), and keeps gas prices relatively low. NFT projects generally frame allowlist spots as a "reward" for community participation or various kinds of non-monetary contributions.

###
How Are Allowlists Normally Managed?
[](#how-are-allowlists-normally-managed)
Historically, Ethereum-based NFT allowlists have been managed on-chain. This means a mapping of wallets addresses must be added to on-chain storage, usually via a function on the NFT project's smart contract that populates that persistent mapping in storage accessible to other functions. The transaction to add these wallet addresses can be incredibly expensive, since it is literally buying and taking up additional blockspace with each additional address. It has historically ranged from a few hundred dollars to a few thousand dollars depending on the price of ETH at the time, even if the allowlist is bundled into a single transaction. This number goes much higher broken out into multiple gas-inflicting updates.

Because of the cost, projects are incentivized to set the allowlist once and never update it. Every update costs money. This can lead to errors in the list, inequity, justifiably grouchy late-comers to the community, and other problems. Additionally, allowlists can often become "static requirements": rigid patterns that get over-applied by a one-size-fits-all approach. Services like [Premint](https://www.premint.xyz/) have begun to change this, which introduces an economy of scale to save on gas and other features. But further improvements are possible! Projects should have the flexibility to implement dynamic requirements on whom gets added to an allowlist and how.

That's where Verifiable Credentials come in.

###
How To Use Verite and Verifiable Credentials
[](#how-to-use-verite-and-verifiable-credentials)
We're going to be working through an Ethereum ERC-721 NFT contract alongside a mechanism that allows us to issue verifiable credentials to participants that we want to allow on the allowlist. We'll use Hardhat to help us generate the skeleton for our smart contract code and to make it easier to test and deploy.

We'll also use [Sign In With Ethereum](https://login.xyz/) (SIWE) to handle our user sessions. We're using SIWE because [it provides more protections and assurances](https://blog.spruceid.com/sign-in-with-ethereum-is-a-game-changer-part-1/) than the normal "Connect Wallet" flow does.

On the front-end side of the house, we'll build a simple page that allows potential allowlist recipients to request their verifiable credential, and we'll build the actual minting functionality.

Let's get started. You'll need Node.js version 12 or above for this. You'll also need a good text editor and some knowledge of the command line.

From your command line, change into the directory where you keep all your fancy NFT projects. Then, let's clone the example app I built ahead of this tutorial to make our lives easier.

git clone https://github.com/circlefin/verite-minter-allowlist
This is a repository that uses SIWE's base example app and extends it. So what you'll have is a folder for your frontend application, a folder for your backend express server, and a folder for your smart contract-related goodies.

Let's start by looking at the backend server. Open the backend/src/index.js file. There's a lot going on in here, but half of it is related to SIWE, which is very well documented. So, we're going to gloss over those routes and just trust that they work (they do).

###
Request Allowlist Endpoint
[](#request-allowlist-endpoint)
Scroll down in the file until you see the route for requestAllowlist. Now, before we go any further, let me walk through a quick explanation of how this entire flow will work.

- Project runs a web app and a server
- Web app handles both requesting/issuing verifiable credentials associated with the allowlist and minting
- During the minting process, a verifiable credential must be sent back to the project's server.
- Backend handles checking to see if a wallet should receive a credential, then generating the credential.
- Backend handles verifying that a credential sent as part of the minting process is valid.
- If credential is valid, backend signs an EIP712 message with a private key owned by the project.
- Signature is returned to the frontend and includes it as part of the mint function on the smart contract.
We'll dive into details on the smart contract in a moment, but that's the basic flow for the front and backends. For those who love a good diagram, we've got you covered:



Now, if we look at the route called requestAllowlist, we'll see:

JavaScript
if (!req.session.siwe) {
  res.status(401).json({ message: "You have to first sign_in" })
  return
}
const address = req.session.siwe.address
if (!validateAllowlistAccess(address)) {
  res.status(401).json({ message: "You are not eligible for the allowlist" })
  return
}

const { subject } = await getOrCreateDidKey(address)

const issuerDidKey = await getIssuerKey()
const application = await createApplication(issuerDidKey, subject)
const presentation = await getPresentation(issuerDidKey, application)

res.setHeader("Content-Type", "application/json")
res.json(presentation)
We are using the SIWE library to make sure the user is signed in and has a valid session. This also gives us the user's wallet address. Remember, we're trusting that all the SIWE code above this route works (it does).

Next, we are checking to see if the project has determined that wallet to be eligible for the allowlist. This is a very low-tech process. Most projects ask participants to do something in order to get on the list and then they manage a spreadsheet of addresses. In this code example, we have a function called validateAllowlistAccess() that checks a hardcoded array of addresses from a config file:

JavaScript
const config = JSON.parse(fs.readFileSync("../config.json"))

const validateAllowlistAccess = (address) => {
  return config.addressesForAllowlist.includes(address)
}
Next, we need to create a DID (decentralized identifier) key for the associated wallet (or we need to look up an existing DID key). In a perfect world, we'd be using a built-in credential wallet integration with the user's Ethereum wallet, but since we don't have that, we're going to manage a delegated key system. The system works like this:

- Project checks to see if there is a DID key for the wallet in question in the database (note: the database here is just disk storage, but can be anything you'd like).
- If there is a DID key, project uses that key for Verite functions.
- If there is no DID key, project generates one and adds the mapping to the database.
That's happening here:

JavaScript
const { subject } = await getOrCreateDidKey(address)
And the getOrCreateDidKey() function looks like this:

JavaScript
const getOrCreateDidKey = async (address) => {
  const db = JSON.parse(fs.readFileSync("db.json"))
  let keyInfo = db.find((entry) => entry.address === address)
  if (!keyInfo) {
    const subject = randomDidKey(randomBytes)
    subject.privateKey = toHexString(subject.privateKey)
    subject.publicKey = toHexString(subject.publicKey)
    keyInfo = {
      address,
      subject
    }
    db.push(keyInfo)
    fs.writeFileSync("db.json", JSON.stringify(db))
  }

  return keyInfo
}
As you can see, our database is making use of the always fashionable file system. We look up the key or we generate a new one using Verite's randomDidKey function. We then convert the public and private key portion of the payload to hex strings for easier storage.

Ok, moving on. Next, we grab the issuer key. This is a DID key that is associated with the project.

JavaScript
const issuerDidKey = await getIssuerKey()
Much like the function to get the user's DID key, the getIssuerKey function just does a look up in the DB and returns the key. Remember to always protect your keys, kids. Even though these keys are exclusively for signing and issuing credentials, you should protect them as if they could spend your ETH.

JavaScript
const getIssuerKey = async () => {
  let issuer = JSON.parse(fs.readFileSync("issuer.json"))
  if (!issuer.controller) {
    issuer = randomDidKey(randomBytes)
    issuer.privateKey = toHexString(issuer.privateKey)
    issuer.publicKey = toHexString(issuer.publicKey)
    if (!issuerDidKey.signingKey) {
      const randomWallet = ethers.Wallet.createRandom()
      const privateKey = randomWallet._signingKey().privateKey
      issuerDidKey.signingKey = privateKey
    }
    fs.writeFileSync("issuer.json", JSON.stringify(issuer))
  }

  return issuer
}
As you can see, in addition to creating a DID key or fetching a DID key with this function, we are creating a signing key using an ETH wallet. This will be the same key we use to deploy the smart contract and sign a message later. Stand by for disclaimers!

Next, we call a function called createApplication.

JavaScript
const createApplication = async (issuerDidKey, subject) => {
  subject.privateKey = fromHexString(subject.privateKey)
  subject.publicKey = fromHexString(subject.publicKey)
  const manifest = buildKycAmlManifest({ id: issuerDidKey.controller })
  const application = await composeCredentialApplication(subject, manifest)
  return application
}
This function includes some helpers to convert the DID key private and public keys back from hex strings to buffers. The function then uses the buildKycAmlManifest function from the Verite library to build a manifest that will be used in the credential application. It should be noted that I'm using the KycAmlManifest but you could create your own manifest that more closely mirrors adding someone to an allowlist. The KycAmlManifest fit closely enough for me, though.

Finally, the manifest is used and passed into the Verite library function composeCredentialApplication and the application is returned.

When the application is built, we now call a function called getPresentation:

JavaScript
const getPresentation = async (issuerDidKey, application) => {
  issuerDidKey.privateKey = fromHexString(issuerDidKey.privateKey)
  issuerDidKey.publicKey = fromHexString(issuerDidKey.publicKey)

  const decodedApplication = await validateCredentialApplication(application)

  const attestation = {
    type: "KYCAMLAttestation",
    process: "https://verite.id/definitions/processes/kycaml/0.0.1/usa",
    approvalDate: new Date().toISOString()
  }

  const credentialType = "KYCAMLCredential"

  const issuer = buildIssuer(issuerDidKey.subject, issuerDidKey.privateKey)
  const presentation = await composeFulfillmentFromAttestation(
    issuer,
    decodedApplication,
    attestation,
    credentialType
  )

  return presentation
}
We're using the project's issuer DID key here. We validate and decode the application using Verite's validateCredentialApplication function. Then, we have to attest to the credential presentation.

Using the issuer private key and public key, we call the Verite library buildIssuer function. With the result, we can then create the verifiable presentation that will ultimately be passed back to the user by calling Verite's composeFulfillmentFromAttestation function.

It is that presentation that is sent back to the user. We'll take a look at the frontend shortly, but just know that the presentation comes in the form of a JWT.

###
Verify Mint Access Endpoint
[](#verify-mint-access-endpoint)
Next, we'll take a look at the verifyMintAccess route. This route includes significantly more functionality. Let's dive in!

JavaScript
try {
  const { jwt } = req.body

  if (!req.session || !req.session.siwe) {
    return res.status(403).send("Unauthorized, please sign in")
  }
  const address = req.session.siwe.address

  const decoded = await verifyVerifiablePresentation(jwt)

  const vc = decoded.verifiableCredential[0]

  const decodedVc = await verifyVerifiableCredential(vc.proof.jwt)

  const issuerDidKey = await getIssuerKey()

  const { subject } = await getOrCreateDidKey(address)

  const offer = buildKycVerificationOffer(
    uuidv4(),
    issuerDidKey.subject,
    "https://test.host/verify"
  )
  const submission = await composePresentationSubmission(
    subject,
    offer.body.presentation_definition,
    decodedVc
  )

  //  The verifier will take the submission and verify its authenticity. There is no response
  //  from this function, but if it throws, then the credential is invalid.
  try {
    await validateVerificationSubmission(
      submission,
      offer.body.presentation_definition
    )
  } catch (error) {
    console.log(error)
    return res.status(401).json({ message: "Could not verify credential" })
  }

  let privateKey = ""

  if (!issuerDidKey.signingKey) {
    throw new Error("No signing key found")
  } else {
    privateKey = issuerDidKey.signingKey
  }

  let wallet = new ethers.Wallet(privateKey)

  const domain = {
    name: "AllowList",
    version: "1.0",
    chainId: config.chainId,
    verifyingContract: config.contractAddress
  }
  const types = {
    AllowList: [{ name: "allow", type: "address" }]
  }
  const allowList = {
    allow: address
  }

  const signature = await wallet._signTypedData(domain, types, allowList)

  return res.send(signature)
} catch (error) {
  console.log(error)
  res.status(500).send(error.message)
}
Once again, the first thing we check is that the user has a valid SIWE session. This route takes a body that includes the verifiable presentation we had sent to the user previously. So, the next step is to call the Verite function verifyVerifiablePresentation to then be able to extract the verifiable credential and call the verifyVerifiableCredential function.

As with our requestAllowlist route, we now need to get the issuer DID key and look up the user's delegated DID key. From there, we can use the issuer key to call the Verite library function buildKycVerificationOffer. We use the results of that call and the user's DID key to call the Verite library function composePresentationSubmission.

Now, we get on to the good stuff. We're going to make sure a valid credential was sent to us. We call the Verite library function validateVerificationSubmission. This function will throw if the credential is invalid. Otherwise, it does nothing. We're rooting for nothing!

Next, the code might get a little confusing, so I want to spend some time walking through this implementation and highlighting how you'd probably do this differently in production. Once the credential is verified, we need to sign a message with a private key owned by the project. For simplicity, I chose to use the same private key that would deploy the smart contract. This is not secure. Don't do this. Hopefully, this is enough to illustrate how to execute the next few steps, though.

We have the issuer DID key written to our database already (file system). We also included a signing key. We need that signing key to sign the message that will be sent back to the user. We use that key to build an Ethereum wallet that can be used for signing.

JavaScript
let privateKey = ""

if (!issuerDidKey.signingKey) {
  throw new Error("No signing key found")
} else {
  privateKey = issuerDidKey.signingKey
}

let wallet = new ethers.Wallet(privateKey)
Finally, we build out the EIP-712 message and sign it. The resulting signature hash is what we send back to the browser so the user can use it in the smart contract's minting function.

That was a lot, but guess what? The frontend and the smart contract should be a lot quicker and easier to follow.

###
Frontend
[](#frontend)
If we back out of the backend folder in our project, we can then switch into the frontend folder. Take a look at frontend/src/index.js. The requestAllowlist function is the one the user will call to hit the project's server's endpoint to see if the user is even allowed to get an allowlist credential. If so, the credential is returned and stored in localstorage:

JavaScript
async function requestAllowlistAccess() {
  try {
    const res = await fetch(`${BACKEND_ADDR}/requestAllowlist`, {
      credentials: "include"
    })
    const message = await res.json()

    if (res.status === 401) {
      alert(message.message)
      return
    }
    localStorage.setItem("nft-vc", message)
    alert("Credential received and stored in browser")
  } catch (error) {
    console.log(error)
    alert(error.message)
  }
}
Again, this would look a lot nicer if there was a built-in credential wallet integration with Ethereum wallets, but for simplicity, the credential is being stored in localstorage. Safe, safe localstorage.

(narrator: localstorage is not safe).

When it's time to mint during the presale, the user clicks on the mint button and the mintPresale function is called:

JavaScript
async function mintPresale() {
  const jwt = localStorage.getItem("nft-vc")
  if (!jwt) {
    alert("No early access credential found")
    return
  }

  const res = await fetch(`${BACKEND_ADDR}/verifyMintAccess`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ jwt }),
    credentials: "include"
  })

  if (res.status === 401 || res.status === 403) {
    alert(
      "You're not authorized to mint or not signed in with the right wallet"
    )
    return
  }

  const sig = await res.text()

  const contract = new ethers.Contract(address, json(), signer)
  const allowList = {
    allow: address
  }
  let overrides = {
    value: ethers.utils.parseEther((0.06).toString())
  }
  const mint = await contract.mintAllowList(
    1,
    allowList,
    sig,
    address,
    overrides
  )
  console.log(mint)
  alert("Minted successfully")
}
This function grabs the credential from localstorage and sends it along to the project's backend server. Assuming the signature from the project is returned, the user is now able to mint. That signature is sent to the smart contract as well as how many tokens should be minted and the amount of ETH necessary to mint. Note, the allowlist object that we send as well. This helps the smart contract verify the signature. Simple!

But how does that work with the smart contract exactly?

###
Smart Contract
[](#smart-contract)
If you open up the contract folder, you'll see a sub-folder called contracts. In there, you'll see the smart contract we're using in this example, called Base_ERC721.sol.

This is a pretty standard NFT minting contract. It's not a full implementation. There would be project-specific functions and requirements to make it complete, but it highlights the allowlist minting functionality.

The first thing to note is we're using the EIP-712 standard via a contract imported from OpenZeppelin. You can see that with this line:

import "@openzeppelin/contracts/utils/cryptography/draft-EIP712.sol";
Next, we are extending the ERC-721 contract and specifying use of EIP-712 here:

contract BASEERC721 is ERC721Enumerable, Ownable, EIP712("AllowList", "1.0") {
  ...
A little further down in the contract, we create a struct that defines the allowlist data model. It's simple because we are only looking at the wallet address that should be on the allowlist:

struct AllowList {
  address allow;
}
We're going to focus in now on the mintAllowList function and the _verifySignature function. Our mintAllowList function starts off similar to a normal NFT minting function except it includes the required signature argument and dataToVerify argument. We do a couple of normal checks before we get to a check that verifies the signature itself. This is where the magic happens.

The _verifySignature function is called. It takes in the data model and the signature.

function _verifySignature(
  AllowList memory dataToVerify,
  bytes memory signature
) internal view returns (bool) {
  bytes32 digest = _hashTypedDataV4(
      keccak256(
          abi.encode(
              keccak256("AllowList(address allow)"),
              dataToVerify.allow
          )
      )
  );

  require(keccak256(bytes(signature)) != keccak256(bytes(PREVIOUS_SIGNATURE)), "Invalid nonce");
  require(msg.sender == dataToVerify.allow, "Not on allow list");

  address signerAddress = ECDSA.recover(digest, signature);

  require(CONTRACT_OWNER == signerAddress, "Invalid signature");

  return true;
}
Using the EIP-712 contract imported through the OpenZeppelin library, we're able to create a digest representing the data that was originally signed. We can then recover the signing address and compare it to the expected address. In our simplified example, we expect the signer to be the same address as the contract deployer, but you can, of course, extend this much further.

To help avoid replay attacks, we also compare the current signature to a variable called PREVIOUS_SIGNATURE. If the signature is the same, we reject the entire call because a signature can only be used once.

Back to our mintAllowList function, if the signature is verified, we allow the minting to happen. When that's complete, we update the PREVIOUS_SIGNATURE variable. This is, as with many things in this demo, a simplified replay attack prevention model. This can and probably should be extended to support your own use cases.

###
Caveats and Conclusion
[](#caveats-and-conclusion)
In a perfect world, we would not be issuing credentials to a delegated subject DID. In our example, we could have just as easily have issued to the user's wallet address, but we wanted to highlight the DID functionality as best as possible.

It is possible today for the user to manage their own DID and keys, but the tricky part comes, as mentioned earlier in this post, when interacting with crypto wallets. Signing a transaction is not the same as signing a JWT. The keys used are different, the signatures are different, and the flow is different. Until these things become unified and more seamless, this demo helps illustrate how Verite can be used today to enforce allowlist restrictions for an NFT minting project.

Hopefully, this sparks some creativity. Hopefully, it inspires some people to go and build even more creative solutions that leverage verifiable credentials and Verite.

Updated 6 months ago


URL: https://developers.circle.com/verite/docs/verite-issuer-registry-pvg
Title: Verite Issuer Registry PVG
Description: undefined
Keywords: undefined


URL: https://developers.circle.com/verite/docs/schema-design
Title: Schema Design
Description: undefined
Keywords: undefined


URL: https://developers.circle.com/verite/docs/schemas
Title: Schemas
Description: undefined
Keywords: undefined


URL: https://developers.circle.com/verite/docs/travel-rule-research
Title: Travel Rule Research
Description: undefined
Keywords: undefined

The Verite open source repo includes some code that provides a cursory demonstration of how exchanging verifiable credentials could be a core component of scalable and open ecosystems for financial institutions to comply with Travel Rule reporting obligations with minimal privacy, data leakage, and security risks.

In addition to this high-level "proof of concept," long-tail research in ongoing into FATF use cases forming a cornerstone of Verite in the latter part of 2023.

Please note: TRUST, TRISA, and other protocols that currently do not use Verifiable Credentials as an exchange format for FATF counterparty reporting are in no way incompatible with this group's approach; our goal is to focus on the privacy risks and protocols for mutual discovery and interactions, which can bootstrap any protocol between financial institutions once validated.

##
The core problems of our FATF research group, aka "The Travel Rule"
[](#the-core-problems-of-our-fatf-research-group-aka-the-travel-rule)
The "Travel Rule" refers to the [U.S. Bank Secrecy Act rule](https://www.sec.gov/about/offices/ocie/aml2007/fincen-advissu7.pdf) as well as guidance defined by the international [Financial Action Task Force (FATF) Recommendation 16](https://www.fatf-gafi.org/publications/fatfrecommendations/documents/guidance-rba-virtual-assets-2021.html). The Travel Rule requires Financial Institutions (FIs) and what FATF refers to as Virtual Asset Service Providers (VASPs) to exchange and analyze specific PII data about the originator and beneficiary of a given transaction when such a transaction exceeds a threshold of notional value (the threshold is 3000 USD value for the US BSA version and 1000 USD/EUR value for FATF version). The purpose of these regulations is to police financial crime such as money laundering, block terrorist financing, stop payments to sanctioned entities and countries by requiring intermediaries to report threshold-exceeding transaction and combine those reports with other monitoring to detect and investigate suspicious activities. Since most prosecution and tort discovery of these crimes leans heavily on forensics, this reporting is often archival in nature and format.

Mapping Travel Rule requirements to blockchain transactions poses several challenges. One such problem is "discoverablility" -- knowing when a beneficiary/recipient address is managed by a financial institution and therefore when Travel Rule obligations exist, and furthermore how to contact the recipient institution to execute secure counterparty data exchange. At broadcast time, the originator of a blockchain transaction has no means of knowing whether a recipient address is custodied by a financial institution or VASP, much less which one, and furthermore no means of directly contacting the owner or custodian of that address.

The crypto ecosystem has proposed many potential solutions to this problem over time:

- maintaining registries of known blockchain addresses mapped to VASP network endpoints,
- maintaining a network of VASP nodes which FIs can query/poll prior to transaction broadcasts,
- leveraging analytics services and tools that glean metadata about addresses and make inferences about their level of safety,
- transacting only on permissioned networks or specific chains designed with different identity capabilities,
- introducing new identifier schemes for transacting customers
- many hybrids and combinations of these approaches
Many of these approaches are either greenfield approaches requiring vast, new platforms to be established quickly, or empower dangerously omniscient data barons to be the only possible middlemen regulators and end-users alike can trust to enforce reporting and monitoring. The governance of those platforms and/or middlemen poses, for us, a real challenge to the "opt-in", pseudonymous-by-default ethos of web3, but proposing a cogent, researched, prototyped alternative is worth a thousand eloquent critiques so we have been working on the former rather than stacking up the latter.

##
The approach
[](#the-approach)
Our design goal in our research and design has been to focus on the privacy problems of discoverability and trust establishment between VASPs and FIs (and perhaps even self-custodied wallets!), imagining an open platform that does not compromise the anonymity guarantees of today's blockchains and marketplaces.

The Verite research group has sketched out a "Verite-maximalist," privacy-preserving architecture for solving these problems by:
1.) using a decentralized bulletin board discovery mechanism for wallet custodians (or, theoretically, a sufficiently sophisticated VC-enabled self-custody wallet!) to discover one another, then
2.) querying public, verifiable information about custodians and/or exchanging private verifiable credentials about self-custody counterparties (if accepted by the former) to mutually authenticate a secure channel, and only then
3.) bootstrapping a mutually-secure connection verifiable credentials exchange about the controllers of both wallets.

Note: The "travel rule" demo in the Verite codebase on github demonstrates one implementation of 1, which we call the "mempool" implementation of a "bulletin board" messaging-based discovery architecture; 2 is still in research phase; and 3 can be achieved today by protocols like TRUST or TRISA, albeit not yet in verifiable credential form.

###
Discovery and Credentials Exchange Sequence
[](#discovery-and-credentials-exchange-sequence)
Note, the following diagram reflects the prototype on github, which predates much of the exploratory/theoretical design work of the FATF working group within Verite's open-source development initiative. The text below presents an expanded, more detailed view; for an understanding of the prototype, refer to the diagram wherever the two differ.


###
Part 1: Discovery and Proving Control of Blockchain Addresses
[](#part-1-discovery-and-proving-control-of-blockchain-addresses)
Crucial to the design of our system is some kind of bulletin board system which can be conceived of as a miniature "mempool", i.e. a smart contract that hosts ephemeral messages posted publically for the express purpose of being responded to privately by self-authenticating parties.

Before an intended/proposed transaction is broadcast, the sending and receiving FIs use this chain-agnostic "bulletin board" to publish and observe messages establishing and requesting proof of address control. These messages contain no customer data and no specific transaction data other than the recipient address (hashed) and the identity of the sender's custodian.

Since the transaction amount is not included, observers are unable to determine information about the planned transaction other than its intended origin and destination (this is less information than is available in the EVM-wide mempool, for example, which broadcasts entire transaction ready to be added to blocks). This allows counterparty endpoints to find and connect to one another privately before exchanging counterparty information, and the mutual exchange of proof-of-control credentials for both addresses prevent any other party from spoofing the originator or beneficiary.

- Alice, the originator, initiates a transfer with her financial institution, which operates a hosted crypto wallet on her behalf.
- Alice's FI forms a message (referred to here as "M1"), containing:
- Request for proof of control of the beneficiary address (e.g. formatted as a Presentation Request or as a [CACAO receipt](https://chainagnostic.org/CAIPs/caip-74) of a [SIWX message](https://chainagnostic.org/CAIPs/caip-122))
- One or more identifiers for querying and verifying the identity of the custodian initiating the message (i.e., Alice's)
- A secure callback URL endpoint for replies (e.g. a Presentation Exchange endpoint for receiving Verifiable Presentations)
- Optionally, a "Fast mode" variant of this "M1" message could also skip a step by also including:
- A Proof-of-control of the originating address (formatted as a Verifiable Credential, for example, or even as a ["sign-in with X" off-chain transaction message](https://chainagnostic.org/CAIPs/caip-122))
- Optionally, Alice's FI could elect to support a "Suspicious mode" (i.e. supporting an optional authentication step) by additionally including in M1:
- A secure callback URL endpoint for requesting proof-of-control credentials by M1 message identifier after establishing trust (see below)
- Alice's FI publishes M1 to the decentralized bulletin board.
- Bob's financial institution observes M1 being emitted in the bulletin board, verifies that the recipient address is in its custody, and responds acordingly.
###
Part 2: Trust Establishment: i.e. Exchange of Public Credentials
[](#part-2-trust-establishment-ie-exchange-of-public-credentials)
The trust establishment step (which some might think of as an "anti-spam"/"anti-DoS" step to avoid spam or data-collecting crawlers) is not included in the simplified prototype in our repo. There are various prototyping efforts happening in the DIF and elsewhere that may prove adequate for this step, so we are deferring research on it in case open-source specifications get hardened in the meantime for this kind of authentication and public trust registry building.

- If in "Suspicious Mode", Bob's financial institution would attempt to authenticate Alice's FI, the author of M1 before responding. If in "Fast Mode, it could skip to step 3.
- Bob's financial institution would authenticate Alice's FI by the identifier(s) provided in M1.
- In a greenfield world, the simplest identifiers to mandate would be a short list of DID methods that support a given key/value pair in the document, such as a given service type. One simple DID method for this would be did:web, conducive to manual/human-readable fall-backs and auditability without reliance on blockchains or other novel architectures.
- That said, there are many brownfield registries and identifier networks that could easily be bootstrapped at this step. "Identifier" here refers to any key that can be used to query public registries of regulated financial institutions maintained by trusted intermediaries, competent authorities, or your friendly local trade association and open source standards organization (including a DID registered on-chain or off- with Verite!). Crucially, however, these registries should be multiple and open, to maximize spread and bootstrap an open ecosystem that is inclusive of multiple closed ones and proprietary authorities.
- This step of authenticating counterparties via registry lookups could be thought of as equivalent to fetching public credentials from authorities; direct exchange of verifiable credentials between FIs could be explored as a more disintermediated/P2P equivalent long-term.
- Bob's financial institution verifies the originator's proof-of-control.
- Bob's financial institution constructs message "M2" which contains:
- a VC proving control over the beneficiary address or equivalent [off-chain] proof.
- Bob's FI transmits M2 privately to Alice's FI using the callback URL specified in M1.
- Alice's FI receives and verifies the VC in M2, leaving both Alice's FI and Bob's FI (a) certain that each controls the designated addresses and (b) with a private secure network connection established through the specified callback URL.
###
Part 3: Exchange of Counterparty Credentials
[](#part-3-exchange-of-counterparty-credentials)
If Alice's FI determines the transaction does not meet the threshold required for the Travel Rule after establishing both Alice's and Bob's requirements by jurisdiction, then Part 3 is not necessary and does not apply, as Alice's FI can simply proceed to broadcasting the blockchain transaction.

If the Travel Rule does apply, however, both financial institutions are able to exchange counterparty verifiable credentials over callback URLs that allow for private secure communication. This can happen over any secure channel, and today is mostly done in consortium/network-like platforms that bundle the authentication of step 2 and the secure channel for step 3 (as well as data dictionaries or translation enginges for exchanging the actual information required by applicable reporting requirements, itself a massively complex value-add and rules engine). This research could be called long-tail, as the rapid pace of regulatory change here means the existing protocols will probably be distracted updating their rules engines for some time and not have bandwidth to consider harmonizing on UX and consent flows!

As a prototype of user-centric UX and an architecture that layers consent receipts on top of reporting artefacts, the Verite prototype models how identity-enabled and verifiable-credential-enabled wallets could surface meaningful and direct consent into such a flow. While traditional financial institutions typically require their users to consent to terms and conditions that grant the rights to transfer this kind of private information, this prototype of more proactive and per-interaction consent could pave the way to better support for non-custodial wallet compliance, or conformance to more aggressive regulation about meaningful consent. To address this latter hypothetical requirement, the flow presents notifications as alerts to customers so that they have visibility into when their data is requested, and to provide them the choice to cancel sending or receiving transactions that require such counterparty credentials exchange.

- If the transaction's value triggers the Travel Rule threshold, Alice's FI responds to Bob's FI by composing a private message "M3" containing:
- Transaction amount
- Alice's originating counterparty verifiable credential
- Presentation Request for beneficiary counterparty credentials
- Bob's FI receives M3 and verifies the originator counterparty verifiable credential, and then executes its internal compliance procedures.
- Once Bob's FI clears the information, it generates message "M4" acknowledging receipt.
- Bob transmits M4 to the secure callback URL endpoint.
- Alice's FI receives M4 and verifies Bob's beneficiary counterparty verifiable credential, and then has enough information to decide whether and how to proceed. [Note: this step may include multiple steps, business logic depending on jurisdiction of both parties, bootstrapping into existing travel rule PII exchange protocols, etc.]
- Once Alice's FI clears the information, it broadcasts the planned transaction to the appropriate blockchain's [actual, public transaction] mempool to execute the exchange of value.
- [Optional] Depending on the design of the bulletin board, M1 may need to be manually withdrawn to spare resources.
##
Future Development
[](#future-development)
Note that this sequence is intended only as a demonstration of how exchanging verifiable credentials may be helpful with Travel Rule requirements. We lean heavily on the expertise of companies already working in this space and protocols already in place to exchange counterparty information securely between Financial Institutions.

Production-level topics such as spam prevention in the bulletin board, cross-chain and multi-swap messaging complications, smart-contract-based wallets and other multisignature counterparties, and private key security protections are beyond the current scope and are among several subjects to address in future development and exploration.

Updated 5 months ago


URL: https://developers.circle.com/verite/docs/verite-protocol-introduction
Title: Introduction
Description: undefined
Keywords: undefined


URL: https://developers.circle.com/verite/docs/verite-protocol-quickstart
Title: Quickstart
Description: undefined
Keywords: undefined

If you'd like to jump right into writing code, we have a quick start guide below. You should still read through the foundations of Verite to learn more about the overall architecture and how verifiable credentials can be applied in the real-world, but it's understandable to get excited and want to dive head-first into code.

This quick start is representative of the end-to-end flow of a person (subject) requesting a credential, an issuer issuing that credential, and a verifier verifying that credential. It is all encapsulated in a single function for readability and for quickly testing the referenced functions from the Verite library. However, it's important to understand that each step in the process will happen on different computers and in different environments.

For more detailed guides, please see the [For Developers](/verite/docs/getting-started) section.

##
Installing Verite
[](#installing-verite)
The entire Verite library is housed in a single monorepo called verite. To install the library, simply run either:

npm i verite

or

yarn add verite

##
Example Code
[](#example-code)
ts
import {
  randomDidKey,
  buildIssuer,
  composeFulfillmentFromAttestation,
  buildKycAmlManifest,
  validateCredentialApplication,
  composeCredentialApplication,
  buildKycVerificationOffer,
  composePresentationSubmission,
  validateVerificationSubmission,
  verifyVerifiableCredential,
  verifyVerifiablePresentation
} from "verite"
import { randomBytes } from "crypto"
import { v4 as uuidv4 } from "uuid"

//  Simulation of key generation and storage
//  Each party will have their own keys and will need to maintain these
const subject = randomDidKey(randomBytes)
const issuerDidKey = randomDidKey(randomBytes)
const verifier = randomDidKey(randomBytes)

//  Issuer builds a manifest representing the type of credential (in this case a KYCAML credential)
const manifest = buildKycAmlManifest({ id: issuerDidKey.controller })
//  The credential application is created and returned as a JWT
const application = await composeCredentialApplication(subject, manifest)
//  The decoded JWT is necessary when it comes time to issue the verifiable presentation which will include this credential
const decodedApplication = await validateCredentialApplication(application)
//  The attestation is a standardized representation of the issuer
const attestation = {
  type: "KYCAMLAttestation",
  process: "https://verite.id/definitions/processes/kycaml/0.0.1/usa",
  approvalDate: new Date().toISOString()
}
const credentialType = "KYCAMLCredential"

//  The issuer is created from the issuer key, and the credential is issued
const issuer = buildIssuer(issuerDidKey.subject, issuerDidKey.privateKey)
const presentation = await composeFulfillmentFromAttestation(
  issuer,
  decodedApplication,
  attestation,
  credentialType
)

//  As with the application, the verifiable presentation (which contains the credential)
//  is in JWT form and must be decoded by the subject. This can be done in a mobile app
//  client or a web browser.
const decoded = await verifyVerifiablePresentation(presentation)
//  The verifiable credential is another JWT within the verifiable presentation and
//  can be extracted like this:
const vc = decoded.verifiableCredential[0]
//  The verifiable credential must then be decoded so that the subject can request
//  verification
const decodedVc = await verifyVerifiableCredential(vc.proof.jwt)

//  The subject would make a request to the verifier's server to obtain the verification
//  offer. The code below must be executed by the verifier, using the verifier's key.
const offer = buildKycVerificationOffer(
  uuidv4(),
  verifier.subject,
  "https://test.host/verify"
)

//  The subject can then create a submission is the full verification request which would
//  be sent to the verifier that uses the offer created and supplied by the verifier
const submission = await composePresentationSubmission(
  subject,
  offer.body.presentation_definition,
  decodedVc
)

//  The verifier will take the submission and verify its authenticity. There is no response
//  from this function, but if it throws, then the credential is invalid.
await validateVerificationSubmission(
  submission,
  offer.body.presentation_definition
)
console.log("Credential verified!")
Updated 5 months ago

