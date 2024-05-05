
URL: https://developers.circle.com/stablecoins/docs/transfer-usdc-on-testnet-from-ethereum-to-avalanche
Title: Transfer USDC on testnet from Ethereum to Avalanche
Description: undefined
Keywords: undefined

To get started with CCTP, follow the example script provided [here](https://github.com/circlefin/evm-cctp-contracts/blob/d1c24577fb627b08483dc42e4d8a37a810b369f7/docs/index.js). The example uses [web3.js ](https://web3js.readthedocs.io/en/v1.8.1/getting-started.html)to transfer USDC from a wallet address on Ethereum Sepolia testnet to another wallet address on Avalanche Fuji testnet.

##📘
Interactive Tutorial with Web3 Services

If you are new to building smart contracts, check out our [interactive tutorial](https://learn.circle.com/quickstarts/smart-contract-platform-cctp) where you can transfer USDC using Circle's Web3 Services: Smart Contract Platform and Programmable Wallets.

The script has 5 steps:

- In this first step, you initiate a transfer of USDC from one blockchain to another, and specify the recipient wallet address on the destination chain. This step approves the Ethereum Sepolia TokenMessenger contract to withdraw USDC from the provided Ethereum Sepolia wallet address.
JavaScript

const approveTx = await usdcEthContract.methods.approve(ETH_TOKEN_MESSENGER_CONTRACT_ADDRESS, amount).send({gas: approveTxGas})
- In this second step, you facilitate a burn of the specified amount of USDC on the source chain. This step executes depositForBurn function on the Ethereum Sepolia TokenMessenger contract deployed on [Sepolia testnet](https://sepolia.etherscan.io/address/0x9f3B8679c73C2Fef8b59B4f3444d4e156fb70AA5).
JavaScript

const burnTx = await ethTokenMessengerContract.methods.depositForBurn(amount, AVAX_DESTINATION_DOMAIN, destinationAddressInBytes32, USDC_ETH_CONTRACT_ADDRESS).send();
- In this third step, you make sure you have the correct message and hash it. This step extracts messageBytes emitted by MessageSent event from depositForBurn transaction logs and hashes the retrieved messageBytes using the keccak256 hashing algorithm.
JavaScript

const transactionReceipt = await web3.eth.getTransactionReceipt(burnTx.transactionHash);
const eventTopic = web3.utils.keccak256('MessageSent(bytes)')
const log = transactionReceipt.logs.find((l) => l.topics[0] === eventTopic)
const messageBytes = web3.eth.abi.decodeParameters(['bytes'], log.data)[0]
const messageHash = web3.utils.keccak256(messageBytes);
- In this fourth step, you request the attestation from Circle, which provides authorization to mint the specified amount of USDC on the destination chain. This step polls the attestation service to acquire the signature using the messageHash from the previous step.
##📘
Rate Limit

The attestation service rate limit is 10 requests per second. If you exceed 10 requests per second, the service blocks all API requests for the next 5 minutes and returns an HTTP 429 response.

JavaScript

let attestationResponse = {status: 'pending'};
while(attestationResponse.status != 'complete') {
    const response = await fetch(`https://iris-api-sandbox.circle.com/attestations/${messageHash}`);
    attestationResponse = await response.json()
    await new Promise(r => setTimeout(r, 2000));
}
- In this final step, you enable USDC to be minted on the destination chain. This step calls the receiveMessage function on the Avalanche Fuji MessageTransmitter contract to receive USDC at the Avalanche Fuji wallet address.
JavaScript

const receiveTx = await avaxMessageTransmitterContract.receiveMessage(receivingMessageBytes, signature);
Updated about 1 month ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/limits
Title: Limits
Description: undefined
Keywords: undefined

##
Minter Allowance
[](#minter-allowance)
The USDC smart contract (or module) on each blockchain specifies a limit for how much USDC can be minted before the limit needs to be increased by the master minter, Circle. This limit is called the "minter allowance" and it is individually set for each authorized minter, such as CCTP.

Minter allowance is decremented each time the authorized minter mints, by the amount of USDC that is minted. A transaction attempting to mint in excess of the minter allowance will fail, but may succeed on a subsequent retry after the minter allowance is reset. Minter allowance can be queried from the USDC contract on EVM-compatible chains using the public [minterAllowance](https://github.com/centrehq/centre-tokens/blob/0d3cab14ebd133a83fc834dbd48d0468bdf0b391/contracts/v1/FiatTokenV1.sol#L153) function. On Noble, minter allowance can be queried via the [fiattokenfactory module minters API](https://github.com/circlefin/noble-fiattokenfactory/blob/33b30a6cf87eba20874df84fa93dd100f71ed512/proto/fiattokenfactory/query.proto#L49-L52).

##
Per-Message Burn Limit
[](#per-message-burn-limit)
CCTP defines per-message burn limits. This value is configurable by the CCTP administrator (Circle). This limit prevents the situation where a user burns an amount of USDC on a source chain that could never be minted on a destination chain without increasing minter allowance thresholds. Per-message burn limits can be queried on the TokenMinter contract on EVM-compatible chains, using the public [burnLimitsPerMessage](https://github.com/circlefin/evm-cctp-contracts/blob/master/src/roles/TokenController.sol#L69) mapping. On Noble, per message burn limit can be queried via the [cctp module per_message_burn_limits API](https://github.com/circlefin/noble-fiattokenfactory/blob/master/proto/fiattokenfactory/query.proto#L49-L52).

Updated 5 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/supported-domains
Title: Supported Domains
Description: undefined
Keywords: undefined

A domain is a Circle-issued identifier for a blockchain where CCTP contracts are deployed. Domains do not map to any existing public chain id.

Domain	Name
0	Ethereum
1	Avalanche
2	OP (Optimism)
3	Arbitrum
4	Noble
5	Solana
6	Base
7	Polygon PoS
Updated 2 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/cctp-faq
Title: FAQ
Description: undefined
Keywords: undefined

###
What is Cross-Chain Transfer Protocol (CCTP)?
[](#what-is-cross-chain-transfer-protocol-cctp)
CCTP is a permissionless on-chain utility that facilitates USDC transfers securely between blockchains via native burning and minting. With CCTP, USDC is effectively "teleported" from one blockchain to another.

###
How does CCTP differ from lock-and-mint bridging?
[](#how-does-cctp-differ-from-lock-and-mint-bridging)
Lock-and-mint bridges are applications that lock a user's native USDC within a smart contract on a source chain and then mint a synthetic or bridged form of USDC on a destination chain. This process incurs additional trust assumptions and can result in poor UX due to the fragmentation of liquidity.

In contrast, CCTP enables USDC to move securely 1:1 between blockchains via a native burn-and-mint process. The result is greater capital efficiency and unified liquidity with no creation of bridged forms of USDC.

As a low-level primitive, CCTP can be embedded within existing bridge apps to replace their lock-and-mint functionality.

###
How does CCTP differ from liquidity pool bridging?
[](#how-does-cctp-differ-from-liquidity-pool-bridging)
Liquidity pool bridges are applications that hold large pools of USDC on a source chain and a destination chain in order to facilitate cross-chain swaps for end users. This process incurs additional trust assumptions and fees associated with the liquidity tied up on each chain.

In contrast, CCTP enables USDC to move securely 1:1 between blockchains via a native burn-and-mint process. The result is greater capital efficiency and unified liquidity with no creation of large pools of USDC.

As a low-level primitive, CCTP can be embedded within existing bridge apps to replace their liquidity pool functionality. Alternatively, CCTP could be used by the bridge provider to programmatically rebalance their liquidity pools behind the scenes and reduce operational costs.

###
Who is CCTP designed for?
[](#who-is-cctp-designed-for)
CCTP serves as permissionless infrastructure for developers to build on top of or integrate into their existing apps, wallets, and bridges.

###
Does CCTP require signing up with Circle?
[](#does-cctp-require-signing-up-with-circle)
No. CCTP is a permissionless on-chain utility for third-party developers.

###
What are the fees associated with cross-chain transfers via CCTP?
[](#what-are-the-fees-associated-with-cross-chain-transfers-via-cctp)
There would be a gas fee on the source chain and a gas fee on the destination chain. The app that integrates CCTP would be responsible for determining how gas fees are handled and/or passed on to the end user. There are no additional fees from CCTP.

###
When will CCTP be available on additional chains?
[](#when-will-cctp-be-available-on-additional-chains)
CCTP is available now on mainnet for Arbitrum, Avalanche, Base, Ethereum, Noble, OP Mainnet, Polygon PoS, and Solana. Expansion to additional blockchains is expected throughout 2024.

###
Can’t I use a Circle Mint account or Core API to move USDC across chains? What about a centralized exchange?
[](#cant-i-use-a-circle-mint-account-or-core-api-to-move-usdc-across-chains-what-about-a-centralized-exchange)
Yes, Circle Mint and Core API are capable of moving USDC natively across chains. However, those commercial products are only available to qualified businesses approved by Circle.

Centralized exchanges typically hold various native forms of USDC liquidity on their platforms. Users with an account at a centralized exchange can deposit USDC (native to a given chain) into their exchange wallet, and then withdraw USDC (native to a different chain) to their external wallet. This process incurs additional trust assumptions and possible delays or limitations to the amount allowed for withdrawals.

In contrast, CCTP is permissionless and runs on public blockchains. This means it is accessible to any third-party developer to integrate into their apps and and does not require signing up for an account. Users can move USDC through a CCTP-enabled app to any supported blockchain at any time. Developers can also compose new on-chain experiences on top of CCTP within their apps.

###
How does a given quantity of USDC burned on the source chain become successfully minted on the destination chain?
[](#how-does-a-given-quantity-of-usdc-burned-on-the-source-chain-become-successfully-minted-on-the-destination-chain)
When USDC is burned on the source chain, the event is automatically observed by Circle’s attestation service. The app facilitating the burn of USDC is responsible for fetching the signed attestation from Circle, which then enables CCTP to mint USDC on the destination chain.

###
Have the CCTP smart contracts undergone security audits?
[](#have-the-cctp-smart-contracts-undergone-security-audits)
Yes. Please see our third-party audit documentation conducted by ChainSecurity ([view PDF](https://6778953.fs1.hubspotusercontent-na1.net/hubfs/6778953/CCTP/ChainSecurity_Circle_Circle_EVM_Bridge_audit.pdf)) and Halborn ([view PDF](https://6778953.fs1.hubspotusercontent-na1.net/hubfs/6778953/CCTP/Circle_Internet_Financial_EVM_Bridge_Smart_Contract_Security_Audit_Report_Halborn_Final.pdf)) for more details.

###
What happens if Circle’s attestation service is unresponsive?
[](#what-happens-if-circles-attestation-service-is-unresponsive)
While its unavailability would temporarily preclude new burn messages from being signed, we anticipate robust uptime and availability similar to how our existing minting services operate today.

###
Besides public wallet addresses on-chain, does Circle have access to any personally identifiable information (PII) when a user sends or receives USDC via CCTP?
[](#besides-public-wallet-addresses-on-chain-does-circle-have-access-to-any-personally-identifiable-information-pii-when-a-user-sends-or-receives-usdc-via-cctp)
No, we do not have access to, collect, or store any such data.

###
How does CCTP affect existing bridged forms of USDC?
[](#how-does-cctp-affect-existing-bridged-forms-of-usdc)
CCTP has no direct impact upon existing bridged forms of USDC.

###
How does CCTP affect Circle’s plans to launch USDC on more blockchains?
[](#how-does-cctp-affect-circles-plans-to-launch-usdc-on-more-blockchains)
Circle’s plans to bring USDC natively to more blockchain networks remain the same and will continue to grow. We envision CCTP establishing USDC as a universal liquidity layer for the Internet that is accessible to all.

Updated about 1 month ago

-
[Cross-Chain Transfer Protocol](/stablecoins/docs/cctp-getting-started)
Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/eurc-on-test-networks
Title: EURC on Test Networks
Description: undefined
Keywords: undefined

##🚧
Testnet Tokens have no Financial Value

Because the testnet networks listed below are used only for testing, the EURC testnet tokens in circulation on these networks are programmatically minted, have no financial value, and are not backed by real euro.

Similarly, ETH testnet tokens on Ethereum Sepolia have no financial value.

##
EURC token addresses for blockchain test networks
[](#eurc-token-addresses-for-blockchain-test-networks)
Blockchain Network	Token Address
Avalanche Fuji	[0x5e44db7996c682e92a960b65ac713a54ad815c6b](https://testnet.snowtrace.io/token/0x5e44db7996c682e92a960b65ac713a54ad815c6b)
Ethereum Sepolia	[0x08210F9170F89Ab7658F0B5E3fF39b0E03C594D4](https://sepolia.etherscan.io/address/0x08210F9170F89Ab7658F0B5E3fF39b0E03C594D4)
Solana Devnet	[HzwqbKZw8HxMN6bF2yFZNrht3c2iXXzpKcFu7uBEDKtr](https://explorer.solana.com/address/HzwqbKZw8HxMN6bF2yFZNrht3c2iXXzpKcFu7uBEDKtr?cluster=devnet)
Stellar Testnet	[EURC-GB3Q6QDZYTHWT7E5PVS3W7FUT5GVAFC5KSZFFLPU25GO7VTC3NM2ZTVO](https://stellar.expert/explorer/testnet/asset/EURC-GB3Q6QDZYTHWT7E5PVS3W7FUT5GVAFC5KSZFFLPU25GO7VTC3NM2ZTVO?asset%5B%5D=EURC-GB3Q6QDZYTHWT7E5PVS3W7FUT5GVAFC5KSZFFLPU25GO7VTC3NM2ZTVO-1)
Updated about 2 months ago


URL: https://developers.circle.com/stablecoins/docs/migrate-from-mumbai-to-amoy-testnet
Title: Migrate from Mumbai to Amoy
Description: undefined
Keywords: undefined

We're expand ing support for Amoy to better serve developers building with Circle’s stablecoin protocols and platform services. We encourage all developers to migrate to Mumbai before April 11, 2024. To learn more, see the [Migrate from Mumbai to Amoy blog](https://www.circle.com/blog/upcoming-support-for-polygon-pos-amoy-testnet).

##📘
Please note that Polygon PoS Mumbai testnet is expected to no longer be actively maintained by the community moving forward.

##
USDC
[](#usdc)
Circle will no longer maintain the USDC token contract on the Polygon PoS Mumbai testnet, effective March 27, 2024. To ensure uninterrupted service, please update the contract addresses from Mumbai to Amoy using the following smart contract addresses.

USDC planned launch dates:

- March 27, 2024: Polygon PoS Amoy
Note: Launch dates are tentative and subject to change. If you have questions or want to learn more about USDC, join the conversation in our [Discord](https://discord.com/invite/buildoncircle).

Stablecoin	Blockchain	Available	Amoy Testnet Address
USDC	Polygon PoS	No	[0x41e94eb019c0762f9bfcf9fb1e58725bfb0e7582](https://www.oklink.com/amoy/address/0x41e94eb019c0762f9bfcf9fb1e58725bfb0e7582)
##
CCTP
[](#cctp)
CCTP support will be discontinued on the Polygon PoS Mumbai testnet effective March 27, 2024. While the CCTP smart contracts will still be available on Mumbai, the attestation API will cease to function, rendering CCTP unusable on Mumbai.

Please update your project to utilize the following Polygon PoS Amoy contracts to ensure uninterrupted functionality. It is important to note that apart from the lack of Mumbai support, there are no changes to the attestation API, meaning that no attestation API implementation changes are required on your end.

CCTP planned launched dates:

- March 27, 2024: Polygon PoS Amoy
Note: Launch dates are tentative and subject to change. If you have questions or want to learn more about CCTP, join the conversation in our [Discord](https://discord.com/invite/buildoncircle).

TokenMessenger

Blockchain	Available	Domain	Amoy Testnet Address
Polygon PoS	No	7	[0x9f3b8679c73c2fef8b59b4f3444d4e156fb70aa5](https://www.oklink.com/amoy/address/0x9f3b8679c73c2fef8b59b4f3444d4e156fb70aa5)
MessageTransmitter

Blockchain	Available	Domain	Amoy Testnet Address
Polygon PoS	No	7	[0x7865fafc2db2093669d92c0f33aeef291086befd](https://www.oklink.com/amoy/address/0x7865fafc2db2093669d92c0f33aeef291086befd)
TokenMinter

Blockchain	Available	Domain	Amoy Testnet Address
Polygon PoS	No	7	[0xe997d7d2f6e065a9a93fa2175e878fb9081f1f0a](https://www.oklink.com/amoy/address/0xe997d7d2f6e065a9a93fa2175e878fb9081f1f0a)
Updated about 1 month ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/what-is-usdc
Title: What is USDC?
Description: undefined
Keywords: undefined

USDC is a digital dollar issued by Circle, also known as a stablecoin, running on many of the world’s leading blockchains. Designed to represent US dollars on the internet, USDC is backed 100% by highly liquid cash and cash-equivalent assets so that it's always redeemable 1:1 for USD.

Circle publishes monthly attestation reports for USDC reserve holdings on [our Transparency page](http://circle.com/en/usdc#transparency).

##📘
You may be interested in Cross-Chain Transfer Protocol []()

Cross-Chain Transfer Protocol (CCTP) is a permissionless on-chain utility that enables USDC to move securely between supported blockchain networks. Just like USDC, CCTP is open and free for [anyone to build upon](/stablecoins/docs/cctp-getting-started).

##
USDC Revolutionizes Commerce and Financial Services
[](#usdc-revolutionizes-commerce-and-financial-services)
Circle developed USDC to usher in a world where the US dollar is accessible to businesses and people around the globe without needing a traditional bank account. Digital currencies like USDC inherit the speed, efficiency, and security of public blockchain networks to bring about transformative benefits:

- Open and programmable
- Globally accessible
- Available 24/7/365
- Instant settlement at near-zero cost
Before USDC, businesses needed traditional banking solutions to accept payments, send remittances to vendors and suppliers, and manage their corporate treasuries. In contrast to USDC, legacy payment methods are slow, expensive, and limited by their lack of interoperability with other systems.

USDC makes money programmable, providing businesses and developers the ability to build innovative applications and services on an open platform that anyone can connect to -- globally, quickly, and cost-effectively.

##
How to Access USDC
[](#how-to-access-usdc)
Qualified businesses can apply for a [Circle Mint](https://developers.circle.com/circle-mint/docs) account to readily convert USD to and from USDC at no additional cost.

Developers can add support for USDC in their applications on a variety of public blockchain networks:

- Crypto-native developers often choose to integrate directly with the [USDC smart contract](https://developers.circle.com/stablecoins/edit/quickstart-transfer-10-usdc-on-chain) to design customizable funds flows for their applications.
- Developers new to the blockchain industry can benefit from [Circle's Web3 Services](https://developers.circle.com/w3s/docs) product suite, designed to make it easy to build applications that leverage the power of USDC.
- Developers can use [Cross-Chain Transfer Protocol](/stablecoins/docs/cctp-getting-started) to build novel cross-chain apps that stack together the various functionalities of trading, lending, payments, NFTs, gaming, and more, all while keeping things simple for users.
Updated 5 months ago


URL: https://developers.circle.com/stablecoins/docs/transfer-usdc-on-testnet-from-noble-to-ethereum
Title: Transfer USDC on testnet between Noble and Ethereum
Description: undefined
Keywords: undefined


URL: https://developers.circle.com/stablecoins/docs/transfer-testnet-usdc-between-solana-devnet
Title: Transfer USDC on devnet between Solana and other chains
Description: undefined
Keywords: undefined

To get started with CCTP on Solana devnet, follow the example scripts provided [here](https://github.com/circlefin/solana-cctp-contracts/tree/master/examples). The examples use [Solana web3.js ](https://solana-labs.github.io/solana-web3.js/), [Anchor](https://www.npmjs.com/package/@project-serum/anchor), to transfer USDC to and from an account on Solana devnet and an address on an external blockchain.

As a security measure, these scripts should only be used for devnet testing. It is not recommended to reuse private keys across mainnet and devnet.

Updated about 2 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/noble-cosmos-module
Title: Noble Cosmos Module
Description: undefined
Keywords: undefined

##
Overview
[](#overview)
Noble is a Cosmos application-specific blockchain (or "appchain") that provides native asset issuance for the Cosmos ecosystem. USDC is natively issued on Noble and can be transferred via the Inter-Blockchain Communication Protocol (IBC) to other supported appchains in Cosmos, or via CCTP to any supported domain (e.g. Ethereum).

Note that there are key differences between Cosmos appchains like Noble and EVM-compatible blockchains. Unlike on EVM chains where CCTP is a set of smart contracts, CCTP on Noble is a Cosmos SDK module, which is deployed by Noble governance and built into the Noble blockchain. Cosmos appchains can use IBC to build composable flows with CCTP on Noble.

###
Testnet and Mainnet Module Address
[](#testnet-and-mainnet-module-address)
Chain	[Domain](/stablecoins/docs/supported-domains)	Address
Noble	4	noble12l2w4ugfz4m6dd73yysz477jszqnfughxvkss5
CCTP on Noble source code is available at [https://github.com/circlefin/noble-cctp](https://github.com/circlefin/noble-cctp). The full message spec is defined at [noble-cctp/x/cctp/spec/02_messages.md](https://github.com/circlefin/noble-cctp/blob/dc81b3e0d566d195c869a213519fcecd38b020a5/x/cctp/spec/02_messages.md). The interface below serves as a reference for permissionless messaging functions exposed by the module.

###
Module Interface
[](#module-interface)
####
depositForBurn
[](#depositforburn)
Message: MsgDepositForBurn

Broadcast a transaction that deposits for burn to a provided domain.

Arguments:

- Amount - The burn amount
- DestinationDomain - Domain of destination chain
- MintRecipient - address receiving minted tokens on destination chain as a 32 length byte array
- BurnToken - The burn token address on source domain
####
depositForBurnWithCaller
[](#depositforburnwithcaller)
Message:MsgDepositForBurnWithCaller

Broadcast a transaction that deposits for burn with caller to a provided domain.

This message wraps MsgDepositForBurn. It adds one extra argument, destinationCaller.

Arguments:

- Amount - The burn amount
- DestinationDomain - Domain of destination chain
- MintRecipient - address receiving minted tokens on destination chain as a 32 length byte array
- BurnToken - The burn token address on source domain
- DestinationCaller - authorized caller as 32 length byte array of receiveMessage() on destination domain
####
replaceDepositForBurn
[](#replacedepositforburn)
Message: MsgReplaceDepositForBurn

Broadcast a transaction that replaces a deposit for burn message. Replace the mint recipient and/or
destination caller.

Allows the sender of a previous BurnMessage (created by depositForBurn or depositForBurnWithCaller)
to send a new BurnMessage to replace the original. The new BurnMessage will reuse the amount and
burn token of the original without requiring a new deposit.

Arguments:

- OriginalMessage- original message bytes to replace
- OriginalAttestation- attestation bytes of OriginalMessage
- NewDestinationCaller - the new destination caller, which may be the
same as the original destination caller, a new destination caller, or an empty
destination caller, indicating that any destination caller is valid.
- NewMintRecipient - the new mint recipient. May be the same as the
original mint recipient, or different.
####
receiveMessage
[](#receivemessage)
Message: MsgReceiveMessage

Broadcast a transaction that receives a provided message from another domain. After validation, it performs a mint.

Arguments:

- message [Message Format](/stablecoins/docs/message-format)
- attestation - Concatenated 65-byte signature(s) of message, in increasing order
of the attester address recovered from signatures.
####
sendMessage
[](#sendmessage)
Message:MsgSendMessage

Broadcast a transaction that sends a message to a provided domain.

Arguments:

- DestinationDomain - Domain of destination chain
- Recipient - Address of message recipient on destination chain
- MessageBody - Raw bytes content of message
####
sendMessageWithCaller
[](#sendmessagewithcaller)
Message:MsgSendMessageWithCaller

Broadcast a transaction that sends a message with a caller to a provided domain.

Specifying a Destination caller requires that only the specified caller can call receiveMessage() on destination domain.

This message wraps SendMessage It adds one extra argument, DestinationCaller.

Arguments:

- DestinationDomain - Domain of destination chain
- Recipient - Address of message recipient on destination chain
- MessageBody - Raw bytes content of message
- DestinationCaller - caller on the destination domain, as 32 length byte array
####
replaceMessage
[](#replacemessage)
Message: MsgReplaceMessage

Broadcast a transaction that replaces a provided message. Replace the message body and/or destination caller.

Arguments:

- OriginalMessage - original message bytes to replace
- OriginalAttestation - attestation bytes of OriginalMessage
- NewMessageBody - new message body of replaced message
- NewDestinationCaller - the new destination caller, which may be the same as the original destination caller, a new destination caller, or an empty destination caller, indicating that any destination caller is valid.
Updated 5 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/cctp-getting-started
Title: Cross-Chain Transfer Protocol
Description: undefined
Keywords: undefined

##
Overview
[](#overview)
[Cross-Chain Transfer Protocol (CCTP)](https://circle.com/cctp) is a permissionless on-chain utility that facilitates USDC transfers securely between blockchains networks via native burning and minting. Circle created it to improve capital efficiency and minimize trust requirements when using USDC across blockchain networks. CCTP enables developers to build multi-chain applications that provide secure, 1:1 transfers of USDC across blockchains for their users.

###
Understanding the Problem
[](#understanding-the-problem)
Disparate blockchain networks operate in siloed environments and are unable to communicate with one another. While some networks have built-in protocols to transmit data across their constituent blockchains (e.g. Cosmos uses the Inter-Blockchain Communication (IBC) protocol to send information between its appchains), it is not possible for isolated networks, such as Ethereum and Avalanche, to communicate directly.

Bridges exist to address this limitation, providing the ability to transfer digital assets like USDC between blockchains, but they also present drawbacks. Lock-and-mint bridging and liquidity pool bridging are two common methods that require tying up USDC liquidity in third-party smart contracts, resulting in limited capital efficiency and introducing additional trust assumptions.

###
Design Approach
[](#design-approach)
As a low-level primitive, CCTP can be embedded within any app or wallet—even existing bridges—to enhance and simplify the user experience for cross-chain use cases. With USDC circulating on a large number of blockchain networks, CCTP can connect and unify liquidity across disparate ecosystems where it's supported.

CCTP is based on generalized message passing and built for composability to open up a wide design space. Developers can readily extend its functionality beyond just moving USDC from one blockchain to another. For example, it's possible to create a flow that sends USDC across chains to be automatically deposited into a DeFi lending pool to begin generating yield. This experience can even be designed to feel like one seamless transaction to the end user.

##
How it works
[](#how-it-works)
- USDC is burned on the source chain: Using an app, a user initiates a transfer of USDC from one blockchain to another, and specifies the recipient wallet address on the destination chain. The app facilitates a burn of the specified amount of USDC on the source chain.
- A signed attestation is fetched from Circle: Circle observes and attests to the burn event on the source chain. The app requests the attestation from Circle, which provides authorization to mint the specified amount of USDC on the destination chain.
- USDC is minted on the destination chain: The app uses the attestation to trigger the minting of USDC. The specified amount of USDC is minted on the destination chain and sent to the recipient wallet address.
##
Example Use Cases
[](#example-use-cases)
Developers can tap into CCTP to build novel cross-chain apps that stack together the various functionalities of trading, lending, payments, NFTs, gaming, and more, all while keeping things simple for users.

###
Cross-chain swaps
[](#cross-chain-swaps)
Users can perform cross-chain swaps with digital assets that live on disparate chains, even across L2 rollups (e.g. swapping ARB on Arbitrum for OP on OP Mainnet). In a completely automated way, ARB can be swapped for USDC on Arbitrum, routed by CCTP to OP Mainnet, and swapped for OP. Routing and execution are hidden from the user to deliver a seamless user experience.

###
Cross-chain deposits
[](#cross-chain-deposits)
Users can utilize USDC on Ethereum to open a trading position on a decentralized exchange on Avalanche. Because CCTP can route USDC between chains behind the scenes, the user never needs to switch wallets or even think about which chain they’re holding USDC.

###
Cross-chain purchases
[](#cross-chain-purchases)
With one click, a user with USDC on Avalanche can buy an Arbitrum-based NFT on Uniswap and list it for sale on OpenSea. When the user initiates the transaction, CCTP routes their USDC from Avalanche to Arbitrum to purchase the NFT from Uniswap, then opens the listing on OpenSea. Once again, the user is shielded from complexity for a fast and easy experience.

##
Supported Blockchains
[](#supported-blockchains)
CCTP is available on 8 blockchains where USDC is natively issued, resulting in 56 unique transfer routes between networks.

Mainnet:

- Arbitrum, Avalanche, Base, Ethereum, Noble, OP Mainnet, Polygon PoS, Solana
Testnet:

- Arbitrum Sepolia, Avalanche Fuji, Base Sepolia, Ethereum Sepolia, Noble Testnet, OP Sepolia, Polygon PoS Amoy, Solana Devnet
###
Attestation Service API
[](#attestation-service-api)
This public API provides signed attestations used to transmit cross-chain messages. For more information, see the [API reference](https://developers.circle.com/stablecoin/reference).

##📘
Rate Limit

The attestation service rate limit is 10 requests per second. If you exceed 10 requests per second, the service blocks all API requests for the next 5 minutes and returns an HTTP 429 response.

Environment	URL
Testnet	https://iris-api-sandbox.circle.com
Mainnet	https://iris-api.circle.com
###
Sample App
[](#sample-app)
Explore our open-source [Sample App](https://github.com/circlefin/cctp-sample-app) powered by CCTP, which demonstrates how a user can transfer USDC across supported blockchains from a simple interface. The app uses development frameworks and libraries such as React and Material-UI for the front end, web3-react for wallet integration, ethers.js for interacting with EVM blockchains, and our public API for fetching attestations that validate USDC burn events.

Updated about 1 month ago

-
[Concepts](/stablecoins/docs/concepts)

URL: https://developers.circle.com/stablecoins/docs/evm-smart-contracts
Title: EVM Smart Contracts
Description: undefined
Keywords: undefined

##
Contract Responsibilities
[](#contract-responsibilities)
- TokenMessenger: Entrypoint for cross-chain USDC transfer. Routes messages to burn USDC on a source chain, and mint USDC on a destination chain.
- MessageTransmitter: Generic message passing. Sends all messages on the source chain, and receives all messages on the destination chain.
- TokenMinter: Responsible for minting and burning USDC. Contains chain-specific settings used by burners and minters.
*Full contract source code is available at [https://github.com/circlefin/evm-cctp-contracts](https://github.com/circlefin/evm-cctp-contracts).

##
Mainnet Contract Addresses
[](#mainnet-contract-addresses)
###
TokenMessenger: Mainnet
[](#tokenmessenger-mainnet)
Chain	[Domain](/stablecoins/docs/supported-domains)	Address
Ethereum	0	[0xbd3fa81b58ba92a82136038b25adec7066af3155](https://etherscan.io/address/0xbd3fa81b58ba92a82136038b25adec7066af3155)
Avalanche	1	[0x6b25532e1060ce10cc3b0a99e5683b91bfde6982](https://snowtrace.io/address/0x6b25532e1060ce10cc3b0a99e5683b91bfde6982)
OP Mainnet	2	[0x2B4069517957735bE00ceE0fadAE88a26365528f](https://optimistic.etherscan.io/address/0x2B4069517957735bE00ceE0fadAE88a26365528f)
Arbitrum	3	[0x19330d10D9Cc8751218eaf51E8885D058642E08A](https://arbiscan.io/address/0x19330d10D9Cc8751218eaf51E8885D058642E08A)
Base	6	[0x1682Ae6375C4E4A97e4B583BC394c861A46D8962](https://basescan.org/address/0x1682Ae6375C4E4A97e4B583BC394c861A46D8962)
Polygon PoS	7	[0x9daF8c91AEFAE50b9c0E69629D3F6Ca40cA3B3FE](https://polygonscan.com/address/0x9daf8c91aefae50b9c0e69629d3f6ca40ca3b3fe)
###
MessageTransmitter: Mainnet
[](#messagetransmitter-mainnet)
Chain	[Domain](/stablecoins/docs/supported-domains)	Address
Ethereum	0	[0x0a992d191deec32afe36203ad87d7d289a738f81](https://etherscan.io/address/0x0a992d191deec32afe36203ad87d7d289a738f81)
Avalanche	1	[0x8186359af5f57fbb40c6b14a588d2a59c0c29880](https://snowtrace.io/address/0x8186359af5f57fbb40c6b14a588d2a59c0c29880)
OP Mainnet	2	[0x4d41f22c5a0e5c74090899e5a8fb597a8842b3e8](https://optimistic.etherscan.io/address/0x4d41f22c5a0e5c74090899e5a8fb597a8842b3e8)
Arbitrum	3	[0xC30362313FBBA5cf9163F0bb16a0e01f01A896ca](https://arbiscan.io/address/0xC30362313FBBA5cf9163F0bb16a0e01f01A896ca)
Base	6	[0xAD09780d193884d503182aD4588450C416D6F9D4](https://basescan.org/address/0xAD09780d193884d503182aD4588450C416D6F9D4)
Polygon PoS	7	[0xF3be9355363857F3e001be68856A2f96b4C39Ba9](https://polygonscan.com/address/0xF3be9355363857F3e001be68856A2f96b4C39Ba9)
###
TokenMinter: Mainnet
[](#tokenminter-mainnet)
Chain	[Domain](/stablecoins/docs/supported-domains)	Address
Ethereum	0	[0xc4922d64a24675e16e1586e3e3aa56c06fabe907](https://etherscan.io/address/0xc4922d64a24675e16e1586e3e3aa56c06fabe907)
Avalanche	1	[0x420f5035fd5dc62a167e7e7f08b604335ae272b8](https://snowtrace.io/address/0x420f5035fd5dc62a167e7e7f08b604335ae272b8)
OP Mainnet	2	[0x33E76C5C31cb928dc6FE6487AB3b2C0769B1A1e3](https://optimistic.etherscan.io/address/0x33E76C5C31cb928dc6FE6487AB3b2C0769B1A1e3)
Arbitrum	3	[0xE7Ed1fa7f45D05C508232aa32649D89b73b8bA48](https://arbiscan.io/address/0xE7Ed1fa7f45D05C508232aa32649D89b73b8bA48)
Base	6	[0xe45B133ddc64bE80252b0e9c75A8E74EF280eEd6](https://basescan.org/address/0xe45B133ddc64bE80252b0e9c75A8E74EF280eEd6)
Polygon PoS	7	[0x10f7835F827D6Cf035115E10c50A853d7FB2D2EC](https://polygonscan.com/address/0x10f7835f827d6cf035115e10c50a853d7fb2d2ec)
##
Testnet Contract Addresses
[](#testnet-contract-addresses)
###
TokenMessenger: Testnet
[](#tokenmessenger-testnet)
Chain	[Domain](/stablecoins/docs/supported-domains)	Address
Ethereum Sepolia	0	[0x9f3B8679c73C2Fef8b59B4f3444d4e156fb70AA5](https://sepolia.etherscan.io/address/0x9f3B8679c73C2Fef8b59B4f3444d4e156fb70AA5)
Avalanche Fuji	1	[0xeb08f243e5d3fcff26a9e38ae5520a669f4019d0](https://testnet.snowtrace.io/address/0xeb08f243e5d3fcff26a9e38ae5520a669f4019d0)
OP Sepolia	2	[0x9f3B8679c73C2Fef8b59B4f3444d4e156fb70AA5](https://sepolia-optimism.etherscan.io/address/0x9f3b8679c73c2fef8b59b4f3444d4e156fb70aa5)
Arbitrum Sepolia	3	[0x9f3B8679c73C2Fef8b59B4f3444d4e156fb70AA5](https://sepolia.arbiscan.io/address/0x9f3b8679c73c2fef8b59b4f3444d4e156fb70aa5)
Base Sepolia	6	[0x9f3B8679c73C2Fef8b59B4f3444d4e156fb70AA5](https://base-sepolia.blockscout.com/address/0x9f3B8679c73C2Fef8b59B4f3444d4e156fb70AA5)
Polygon PoS Amoy	7	[0x9f3B8679c73C2Fef8b59B4f3444d4e156fb70AA5](https://www.oklink.com/amoy/address/0x9f3b8679c73c2fef8b59b4f3444d4e156fb70aa5)
###
MessageTransmitter: Testnet
[](#messagetransmitter-testnet)
Chain	[Domain](/stablecoins/docs/supported-domains)	Address
Ethereum Sepolia	0	[0x7865fAfC2db2093669d92c0F33AeEF291086BEFD](https://sepolia.etherscan.io/address/0x7865fAfC2db2093669d92c0F33AeEF291086BEFD)
Avalanche Fuji	1	[0xa9fb1b3009dcb79e2fe346c16a604b8fa8ae0a79](https://testnet.snowtrace.io/address/0xa9fb1b3009dcb79e2fe346c16a604b8fa8ae0a79)
OP Sepolia	2	[0x7865fAfC2db2093669d92c0F33AeEF291086BEFD](https://sepolia-optimism.etherscan.io/address/0x7865fAfC2db2093669d92c0F33AeEF291086BEFD)
Arbitrum Sepolia	3	[0xaCF1ceeF35caAc005e15888dDb8A3515C41B4872](https://sepolia.arbiscan.io/address/0xacf1ceef35caac005e15888ddb8a3515c41b4872)
Base Sepolia	6	[0x7865fAfC2db2093669d92c0F33AeEF291086BEFD](https://base-sepolia.blockscout.com/address/0x7865fAfC2db2093669d92c0F33AeEF291086BEFD)
Polygon PoS Amoy	7	[0x7865fAfC2db2093669d92c0F33AeEF291086BEFD](https://www.oklink.com/amoy/address/0x7865fafc2db2093669d92c0f33aeef291086befd)
###
TokenMinter: Testnet
[](#tokenminter-testnet)
Chain	[Domain](/stablecoins/docs/supported-domains)	Address
Ethereum Sepolia	0	[0xE997d7d2F6E065a9A93Fa2175E878Fb9081F1f0A](https://sepolia.etherscan.io/address/0xe997d7d2f6e065a9a93fa2175e878fb9081f1f0a)
Avalanche Fuji	1	[0x4ed8867f9947a5fe140c9dc1c6f207f3489f501e](https://testnet.snowtrace.io/address/0x4ed8867f9947a5fe140c9dc1c6f207f3489f501e)
OP Sepolia	2	[0xE997d7d2F6E065a9A93Fa2175E878Fb9081F1f0A](https://sepolia-optimism.etherscan.io/address/0xe997d7d2f6e065a9a93fa2175e878fb9081f1f0a)
Arbitrum Sepolia	3	[0xE997d7d2F6E065a9A93Fa2175E878Fb9081F1f0A](https://sepolia.arbiscan.io/address/0xe997d7d2f6e065a9a93fa2175e878fb9081f1f0a)
Base Sepolia	6	[0xE997d7d2F6E065a9A93Fa2175E878Fb9081F1f0A](https://base-sepolia.blockscout.com/address/0xE997d7d2F6E065a9A93Fa2175E878Fb9081F1f0A)
Polygon PoS Amoy	7	[0xE997d7d2F6E065a9A93Fa2175E878Fb9081F1f0A](https://www.oklink.com/amoy/address/0xe997d7d2f6e065a9a93fa2175e878fb9081f1f0a)
##
Interface
[](#interface)
The interface below serves as a reference for permissionless messaging functions exposed by the TokenMessenger and MessageTransmitter functions. The full ABIs are listed [here](https://github.com/circlefin/evm-cctp-contracts/tree/adb2a382b09ea574f4d18d8af5b6706e8ed9b8f2/docs/abis/cctp).

###
TokenMessenger
[](#tokenmessenger)
####
depositForBurn
[](#depositforburn)
Deposits and burns tokens from sender to be minted on destination domain. Minted tokens will be transferred to mintRecipient.

Parameters

Field	Type	Description
amount	uint256	Amount of tokens to deposit and burn.
destinationDomain	uint32	Destination domain identifier.
mintRecipient	bytes32	Address of mint recipient on destination domain.
burnToken	address	Address of contract to burn deposited tokens on local domain.
####
depositForBurnWithCaller
[](#depositforburnwithcaller)
Same as depositForBurn but with an additional parameter, destinationCaller. This parameter specifies which address has permission to call receiveMessage on the destination domain for the message.

Parameters

Field	Type	Description
amount	uint256	Amount of tokens to deposit and burn.
destinationDomain	uint32	Destination domain identifier.
mintRecipient	bytes32	Address of mint recipient on destination domain.
burnToken	address	Address of contract to burn deposited tokens on local domain.
destinationCaller	bytes32	Address of caller on the destination domain.
####
replaceDepositForBurn
[](#replacedepositforburn)
Replace a BurnMessage to change the mint recipient and/or destination caller. Allows the sender of a previous BurnMessage (created by depositForBurn or depositForBurnWithCaller) to send a new BurnMessage to replace the original. The new BurnMessage will reuse the amount and burn token of the original, without requiring a new deposit.

This is useful in situations where the user specified an incorrect address and has no way to safely mint the previously burned USDC.

##📘
The sender of the original depositForBurn has access to call replaceDepositForBurn. The resulting mint will supersede the original mint, as long as the original mint has not confirmed yet on-chain. When using a third-party app/bridge that integrates with CCTP to burn and mint USDC, it is the choice of the app/bridge if and when to replace messages on behalf of users. When sending USDC to smart contracts, be aware of the functionality that those contracts have and their respective trust model.

Parameters

Field	Type	Description
originalMessage	bytes calldata	Original message bytes (to replace).
originalAttestation	bytes calldata	Original attestation bytes.
newDestinationCaller	bytes32	The new destination caller, which may be the same as the original destination caller, a new destination caller, or an empty destination caller, indicating that any destination caller is valid.
newMintRecipient	bytes32	The new mint recipient, which may be the same as the original mint recipient, or different.
###
MessageTransmitter
[](#messagetransmitter)
####
receiveMessage
[](#receivemessage)
Messages with a given nonce can only be broadcast successfully once for a pair of domains. The message body of a valid message is passed to the specified recipient for further processing.

Parameters

Field	Type	Description
message	bytes calldata	Message bytes.
attestation	bytes calldata	Signed attestation of message.
####
sendMessage
[](#sendmessage)
Sends a message to the destination domain and recipient. Emits a MessageSent event which will be attested by Circle’s attestation service.

Parameters

Field	Type	Description
destinationDomain	uint32	Destination domain identifier.
recipient	bytes32	Address to handle message body on destination domain.
messageBody	bytes calldata	Application-specific message to be handled by recipient.
####
sendMessageWithCaller
[](#sendmessagewithcaller)
Same as sendMessage but with an additional parameter, destinationCaller. This parameter specifies which address has permission to call receiveMessage on the destination domain for the message.

Parameters

Field	Type	Description
destinationDomain	uint32	Destination domain identifier.
recipient	bytes32	Address of message recipient on destination domain.
destinationCaller	bytes32	Address of caller on the destination domain.
messageBody	bytes calldata	Application-specific message to be handled by recipient.
####
replaceMessage
[](#replacemessage)
Replace a message with a new message body and/or destination caller. The originalAttestation must be a valid attestation of originalMessage, produced by Circle’s attestation service.

Parameters

Field	Type	Description
originalMessage	bytes calldata	Original message to replace.
originalAttestation	bytes calldata	Attestation of originalMessage.
newMessageBody	bytes calldata	New message body of replaced message.
newDestinationCaller	bytes32	The new destination caller, which may be the same as the original destination caller, a new destination caller, or an empty destination caller (bytes32(0), indicating that any destination caller is valid).
Updated 17 days ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/quickstart-transfer-10-usdc-on-chain?_gl=1*2vmi5w*_ga*MjA0Mjg4MDAxMC4xNjk1MzMxMjk4*_ga_GJDVPCQNRV*MTY5OTI5MzMxNi4yNC4xLjE2OTkyOTMzMzAuNDguMC4w
Title: Quickstart: Transfer 10 USDC On-Chain
Description: undefined
Keywords: undefined

You can utilize USDC to move money near-instantly around the globe using decentralized protocols. This guide describes the steps to send 10 USDC between two wallets using the Ethereum Sepolia testnet network. To complete the steps in this guide, you use third-party tooling, such as Javascript libraries, node services, and wallets.

This guide provides the foundational knowledge you can build on to integrate USDC payment flows into your app or platform. You can adapt the sample code in this guide to serve as a framework for your development. Note that all API calls and transactions in this guide occur on a testnet environment. The tokens you use have no financial value.

After you validate and optimize your app in a testnet environment, you can migrate to a production or mainnet environment and create transactions that do transfer financial value using USDC.

##
Overview of USDC and its token contract
[](#overview-of-usdc-and-its-token-contract)
USDC is a stablecoin backed 1:1 to the U.S dollar and operates natively on many public blockchains. Like many digital currencies, USDC is powered by a token contract, which is a programmable piece of code that manages user balances autonomously across a decentralized network. As transactions occur, the token contract automatically updates the digital ledger, ensuring real-time tracking of funds. This mechanism allows individuals and businesses to send and receive dollars seamlessly, capitalizing on the transparency, security, and efficiency of blockchain technology. 

##
Before you get started
[](#before-you-get-started)
For this tutorial, you need the following apps, tools, and accounts:

-
[Circle Mint Sandbox Account](https://app-sandbox.circle.com/signup/sandbox): Get an account that provides access to testnet USDC. 

-
[MetaMask](https://metamask.io/): A resource for creating wallets, which are devices for storing USDC on chain:

- Create two wallets, one for sending and one for receiving testnet USDC transactions on the Ethereum “Sepolia” testnet network. These will serve as the origin wallet (the one you’re sending USDC from) and the destination wallet (the one you’re sending to). Note the addresses for both. 
- Note the private key for the origin wallet. 
- Import the Ethereum Sepolia testnet [USDC token contract](https://sepolia.etherscan.io/address/0x1c7D4B196Cb0C7B01d743Fbc6116a902379C7238) into your origin wallet to enable it to read the token balance. The contract’s address is 0x1c7D4B196Cb0C7B01d743Fbc6116a902379C7238. 
To view the testnet USDC smart contracts for supported blockchains, see [USDC on Test Networks](https://developers.circle.com/developer/docs/usdc-on-testnet).

-
[Infura](https://www.infura.io/): A node service for connecting to the blockchain and submitting your USDC transaction: 

- Create an API key for the Web3 API offering. 
- Note the Ethereum “Sepolia” testnet endpoint provided. 
-
[Node.js](https://nodejs.org/en): Use the JavaScript runtime environment to run the code locally on your machine.

-
[Ethers.js](https://ethers.org/): JavaScript library containing functions to call the USDC token contract and initiate transactions.

-
[Visual Studio](https://code.visualstudio.com/): a lightweight, but powerful, source code editor available as a desktop app.

##
1. Set up a Circle Mint sandbox account
[](#1-set-up-a-circle-mint-sandbox-account)
Set up a sandbox account so you can get testnet USDC tokens to use for testing.

- Visit [Circle's sandbox signup page](https://app-sandbox.circle.com/signup/sandbox) and click Sign Up.
- Log in to the sandbox account you create.
- Click APIs in the left pane, then click CREATE A KEY in the API Keys section.
- Provide a name for the key, then click CREATE KEY.
- Copy the key value and store it a location you can easily access. You use the key for steps later in this topic.
##
2. Obtain testnet USDC and native gas token
[](#2-obtain-testnet-usdc-and-native-gas-token)
Use the Circle sample app to get testnet USDC to transfer, and testnet ETH to pay the gas (transaction fee) for the transaction.

- Configure the sample app to use you API key: 
- Navigate to [sample-sandbox.circle.com](http://sample-sandbox.circle.com/) 
- Click on the Settings cog wheel in the top-right corner.
- In the Your API Key field, paste the API key you created.
- Click on the menu (three horizontal bars) in the top-left corner, to display available API endpoints.
- Mint testnet USDC via simulated bank transfer in the Core Functionality set:
- Click POST /businessAccount/banks/wires. 
- Click PREFILL FORM to add a simulated bank account for testing.
- Click MAKE API CALL to simulate a request.
- Scroll down to display the response to the request, then copy the value for the id in the response. It should look similar to the following:
"id": "d783e25e-e8b3-42eb-90b4-626f7a9580ce"
This is the Account ID to use for the wire transfer.
- Click GET /businessAccount/banks/wires/{id}/instructions.
- In the Account ID field, paste the id value from the previous step.
- Click MAKE API CALL.
- From the Response section, copy and save the values for trackingRef and accountNumber. 
- Click POST /mocks/payments/wire.
- In the Tracking Ref field, paste the value for trackingRef from the previous step.
- In the Account Number field, paste the value for accountNumber from the previous step. 
- In the Amount field, enter 10.00.
- Click MAKE API CALL.
- Add recipient address from the Payouts API set: 
- Click POST /addressBook/recipients.
- In the Address field, enter your origin wallet address.
- In the Blockchain field, select ETH.
- In the Email field, enter your email address (as recipient).
- In the bns field, enter the nickname for your destination wallet.
- In the Nickname field, enter your user nickname.
- Click MAKE API CALL.
Copy and store the ID from the response.
- Click GET addressBook/recipients/{id}.
- In the Recipient Id field, enter the ID from the previous step.
- Click MAKE API CALL.
In the Response, confirm that the request successfully added the address.
- Perform a payout in USDC and native token (ETH) for gas fees from the Payouts API set:
- Click POST /payouts.
- In the Amount field, enter 10.00.
- Set the To Currency to USD.
- In the Destination field, enter the should be the recipient ID that corresponds to the destination wallet address.
- For Destination Type, select address_book.
- In the Beneficiary Email field, enter the email address of the recipient.
- Click MAKE API CALL.
- From the Response, copy the ID for the payout.
Repeat these steps, but choose ETH for the To Currency field, to provide native tokens to cover gas fees for the transaction.
- Click GET /payouts/{id}.
- In the Payout Id field, enter the payout ID from the previous step.
- Click MAKE API CALL.
View the Response to monitor the payout status. 
##
3. Install Ethers.js library to enable API calls
[](#3-install-ethersjs-library-to-enable-api-calls)
Run the following command to install [Ethers.js](https://ethers.org/):


npm install ethers
##
4. Customize the sample JavaScript code with input variables
[](#4-customize-the-sample-javascript-code-with-input-variables)
In Visual Studio Code, create a new JavaScript file named send10.js. Copy the following code sample and paste it into the send10.js file:

JavaScript

const { ethers } = require('ethers');

// 1. Input the Sepolia RPC endpoint
const rpcEndpoint = '<RPC_ENDPOINT_URL>'; // Insert the INFURA Network Endpoint

// 2. Initialize the Ethers.js provider
const provider = new ethers.JsonRpcProvider(rpcEndpoint);

// 3. Input USDC token contract address for Ethereum Sepolia
const tokenAddress = '<USDC_TOKEN_CONTRACT>'; // USDC TokenAddress

// 4. Define the USDC token contract ABI
const minTokenAbi = [
  {
    inputs: [
      { internalType: 'address', name: 'to', type: 'address' },
      { internalType: 'uint256', name: 'value', type: 'uint256' },
    ],
    name: 'transfer',
    outputs: [{ internalType: 'bool', name: '', type: 'bool' }],
    stateMutability: 'nonpayable',
    type: 'function',
  },
  {
    inputs: [{ internalType: 'address', name: 'account', type: 'address' }],
    name: 'balanceOf',
    outputs: [{ internalType: 'uint256', name: '', type: 'uint256' }],
    stateMutability: 'view',
    type: 'function',
  },
  {
    inputs: [],
    name: 'decimals',
    outputs: [{ internalType: 'uint8', name: '', type: 'uint8' }],
    stateMutability: 'view',
    type: 'function',
  },
];

// 5. Create a new contract instance
const contract = new ethers.Contract(tokenAddress, minTokenAbi, provider);

// 6. Input the addresses and the private key; specify number of tokens to send
const senderAddress = '<SENDER_WALLET_ADDRESS>';
const recipientAddress = '<RECIPIENT_WALLET_ADDRESS>';
const senderPrivateKey = '<SENDER_WALLET_PRIVATE_KEY>';

const usdcAmount = 1.0;

async function main() {
  // 7. Check the number of decimals for the USDC token
  const decimals = await contract.decimals();

  // 8. Check the balance of the sender's address
  const balance = await contract.balanceOf(senderAddress);
  console.log('Sender USDC balance:', ethers.formatUnits(balance, decimals));

  // 9. Calculate the actual amount in the smallest unit
  const value = ethers.parseUnits(usdcAmount.toString(), decimals);

  // 10. Create the transaction
  const tx = await contract.connect(new ethers.Wallet(senderPrivateKey, provider)).transfer(recipientAddress, value);

  // 11. Wait for the transaction to be mined and log the transaction hash
  const receipt = await tx.wait();
  console.log('Tx Hash:', receipt);
}

main().catch((error) => {
  console.error('Error:', error);
  process.exit(1);
});
###
Customize Code
[](#customize-code)
To customize the code for this guide, replace the following variable values: 

- Under comment // 2 replace <RPC_ENDPOINT_URL> with the INFURA endpoint.
- Under comment // 4, replace <USDC_TOKEN_CONTRACT> with the token contract address: 0x07865c6E87B9F70255377e024ace6630C1Eaa37F.
- Under comment // 6:
- Replace <SENDER_WALLET_ADDRESS> with the origin wallet address.
- Replace <RECIPIENT_WALLET_ADDRESS> with the destination wallet address.
- Replace <SENDER_WALLET_PRIVATE_KEY> with the origin wallet’s private key.
- Update the value for usdcAmount to the number of USDC to send. For this example, 10.
Save the Javascript file.

##
5. Execute JavaScript code to initiate USDC transfer
[](#5-execute-javascript-code-to-initiate-usdc-transfer)
In Terminal, enter the following command to execute the code and transfer USDC. 


// Type node <FILENAME>.js to run code
node send10.js
Check the status of your USDC transaction by visiting the [Sepolia testnet block explorer](https://sepolia.etherscan.io/):

Paste the transactionHash value from your terminal log into the search bar. Under Status, “Success” means the transaction successfully settled. 

Congratulations! Completing this quickstart guide for USDC has equipped you with the essential skills of programmable money.

You can adapt the script provided in this guide to enable real-world transactions and empower your own app development. Now, you can apply this newfound knowledge to create frictionless financial experiences, empowering users with seamless, secure global transactions.

Updated 17 days ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/usdc-on-main-networks
Title: USDC on Main Networks
Description: undefined
Keywords: undefined

##
What is a Mainnet?
[](#what-is-a-mainnet)
USDC is a dollar-backed stablecoin that runs on multiple blockchains including the Ethereum blockchain, the Arbitrum blockchain, the Solana blockchain, and more.

Each of these blockchains have both main and test networks, or mainnets and testnets. Mainnets are the production blockchains that move and store digital assets, tokens, NFTs, smart contracts, and messages, while testnets enable developers to test their applications before deploying code on the mainnet.

##🚧
Mainnet Tokens have Financial Value

Because the networks below are used to transfer financial value, take precautions to ensure you make any transfer. Precautions include testing your code, verifying addresses, ensuring your private key/s are safely used and stored.

##❗️
Do not Test on Mainnets

Because mainnet blockchains move and store tokens that have financial value, do not do any testing on the mainnet blockchains.

##
USDC Mainnet Addresses
[](#usdc-mainnet-addresses)
Blockchain	USDC Mainnet Address
Algorand	[31566704](https://algoexplorer.io/asset/31566704)
Arbitrum	[0xaf88d065e77c8cC2239327C5EDb3A432268e5831](https://arbiscan.io/token/0xaf88d065e77c8cC2239327C5EDb3A432268e5831)
Avalanche C-Chain	[0xB97EF9Ef8734C71904D8002F8b6Bc66Dd9c48a6E](https://snowtrace.io/token/0xB97EF9Ef8734C71904D8002F8b6Bc66Dd9c48a6E)
Base	[0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913](https://basescan.org/token/0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913)
Celo	[0xcebA9300f2b948710d2653dD7B07f33A8B32118C](https://celoscan.io/token/0xcebA9300f2b948710d2653dD7B07f33A8B32118C)
Ethereum	[0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48](https://etherscan.io/token/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48)
Flow	[A.b19436aae4d94622.FiatToken](https://www.flowdiver.io/contract/A.b19436aae4d94622.FiatToken)
Hedera	[0.0.456858](https://hashscan.io/mainnet/token/0.0.456858)
NEAR	[17208628f84f5d6ad33f0da3bbbeb27ffcb398eac501a31bd6ad2011e36133a1](https://nearblocks.io/token/17208628f84f5d6ad33f0da3bbbeb27ffcb398eac501a31bd6ad2011e36133a1)
Noble	[uusdc](http://mintscan.io/noble/assets)
OP Mainnet	[0x0b2c639c533813f4aa9d7837caf62653d097ff85](https://optimistic.etherscan.io/token/0x0b2c639c533813f4aa9d7837caf62653d097ff85)
Polkadot Asset Hub	[1337](https://assethub-polkadot.subscan.io/assets/1337)
Polygon PoS	[0x3c499c542cef5e3811e1192ce70d8cc03d5c3359](https://polygonscan.com/token/0x3c499c542cef5e3811e1192ce70d8cc03d5c3359)
Solana	[EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v](https://solscan.io/token/EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v)
Stellar	[USDC-GA5ZSEJYB37JRC5AVCIA5MOP4RHTM335X2KGX3IHOJAPP5RE34K4KZVN)](https://stellar.expert/explorer/public/asset/USDC-GA5ZSEJYB37JRC5AVCIA5MOP4RHTM335X2KGX3IHOJAPP5RE34K4KZVN)
TRON*	[TEkxiTehnzSmSe2XqrBj4w32RUN966rdz8](https://tronscan.org/#/token20/TEkxiTehnzSmSe2XqrBj4w32RUN966rdz8)
zkSync	[0x1d17CBcF0D6D143135aE902365D2E5e2A16538D4](https://explorer.zksync.io/address/0x1d17CBcF0D6D143135aE902365D2E5e2A16538D4)
*Effective Feb. 20, 2024: Circle is discontinuing support for USDC on TRON. For details, see [Circle is Discontinuing Support for USDC on the TRON blockchain](https://www.circle.com/blog/circle-is-discontinuing-support-for-usdc-on-the-tron-blockchain).

Updated 24 days ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/quickstart-transfer-10-usdc-on-solana
Title: Quickstart: Transfer 10 USDC on Solana
Description: undefined
Keywords: undefined

This step-by-step guide teaches you how to write a script to send 10 USDC between two wallets using the Solana Devnet, which is Solana’s testing network. 

To better understand the process and its components, you will use third-party tooling such as Javascript libraries, node services, and wallets. Completing this tutorial will help prepare you to integrate USDC payment flows within your app. Building with USDC enables you to move money near-instantly around the globe using decentralized protocols.

##📘
Note: Use Testnet Scripts as Development Framework

-
As with most of our quickstarts, all API calls and transactions in this guide take place within the testnet environment; no real-world funds will be transferred. 

-
The sample code in this tutorial can be adapted for real-world transactions and can serve as a framework for your further development.

##👍
Tip: Consider the EVM-Compatible Alternative Tutorial

You can go through this process using an EVM compatible chain with the on-chain tutorial, [here](https://developers.circle.com/stablecoins/docs/quickstart-transfer-10-usdc-on-chain?_gl=1*2vmi5w*_ga*MjA0Mjg4MDAxMC4xNjk1MzMxMjk4*_ga_GJDVPCQNRV*MTY5OTI5MzMxNi4yNC4xLjE2OTkyOTMzMzAuNDguMC4w).

##
USDC and Its Token Contract
[](#usdc-and-its-token-contract)
USDC is a stablecoin backed 1:1 to the U.S dollar and operates natively on [many public blockchains](https://developers.circle.com/stablecoins/docs/usdc-on-main-networks). Like many currencies, USDC is powered by a token contract, a programmable piece of code that manages user balances autonomously across a decentralized network. 

As transactions occur, the token contract automatically updates the digital ledger, ensuring real-time tracking of funds. This mechanism allows individuals and businesses to send and receive dollars seamlessly, capitalizing on the transparency, security, and efficiency of blockchain technology. 

##
Prerequisites
[](#prerequisites)
Before you begin this tutorial, you will need the following apps, tools, and accounts:

-
[Circle Mint Sandbox Account](https://app-sandbox.circle.com/signup/sandbox): an account that provides access to testnet USDC.

-
[Phantom](https://phantom.app/): An app for creating wallets, which are devices for storing USDC on chain:

-
Create two wallets, one for sending and one for receiving testnet USDC transactions on the Solana Devnet network. These will serve as your origin wallet, from which one you send USDC, and the destination wallet, which receives USDC. Keep note of the addresses for both.

-
Make sure you securely record the private key for the origin wallet.

-
Import the Solana Devnet [USDC token contract](https://explorer.solana.com/address/4zMMC9srt5Ri5X14GAgXhaHii3GnPAEERYPJgZJDncDU?cluster=devnet) into your origin wallet to enable it to read the token balance. Note the contract address: 4zMMC9srt5Ri5X14GAgXhaHii3GnPAEERYPJgZJDncDU

##📘
Note: Access to the Testnet Scripts

Refer to our testnet scripts, [here](https://developers.circle.com/developer/docs/usdc-on-testnet).

-
[Node.js](https://nodejs.org/en): JavaScript runtime environment to run code locally on your machine.

-
[Web3.js](https://solana-labs.github.io/solana-web3.js/): JavaScript library containing functions to call the USDC token contract and initiate transactions.

-
Code Editor: A source code editor of your choice. One example, [Visual Studio](https://code.visualstudio.com/), is available as a desktop application.

##
Tutorial
[](#tutorial)
Follow the steps in this tutorial to transfer USDC on Solana:

###
Set up a Circle Mint Sandbox Account to access the USDC testnet.
[](#set-up-a-circle-mint-sandbox-account-to-access-the-usdc-testnet)
- Navigate to Circle's [signup page](https://app-sandbox.circle.com/signup/sandbox), enter your information in the required fields, and click "Sign Up." Completing the signup process takes you to the sandbox account Home:


-
Navigate to the APIs tab in the sidebar, and click the “CREATE A KEY” button:

-
Complete the “Key Name” field.

-
Click the “CREATE KEY” button. A modal title “API Key Created” will populate upon success.   

-
Click “SHOW” to view the value of your new key.

-
You must click the key to record a copy of its value. 

##👍
Tip: Do not alter the API Key string:

You must use the entire value of your key, including the first part of the string, which is “SAND_API_KEY:”.



###
Gather Testnet USDC and SOL
[](#gather-testnet-usdc-and-sol)
You will need to use native gas tokens to pay the transaction (gas) fee:

-
Configure the Circle sample sandbox application by adding your API key: 

-
Navigate to [sample-sandbox.circle.com](http://sample-sandbox.circle.com/). 

-
Click on the settings (gear) menu in the top right-hand corner.

-
Complete the field for "Your API Key" with the value from your sandbox account.

-
Click on the hamburger button in the top left corner to access API methods.

-
Mint testnet USDC with a simulated bank transfer using methods in the Core Functionality menu:

-
Click  the POST /businessAccount/banks/wires menu item.

-
Click the PREFILL FORM button and then choose US BANK ACCOUNT to add a simulated bank account for testing.

-
Click the MAKE API CALL button.

-
Copy the value for the id key. 

-
Click the GET /businessAccount/banks/wires/{id}/instructions menu item.

-
Complete the Account Id field using the value of the id key returned from the previous step.

-
Click the MAKE API CALL button.  

-
From the successful response, record the value of the trackingRef and accountNumber fields.

-
Click the POST /mocks/payments/wire menu item.

-
Complete the Tracking Ref and Account Number fields with the values you recorded from the previous step.

-
Complete the Amount field with a dollar amount you choose for your mock payment. 

-
Click the MAKE API CALL button.

-
Add the SOL wallet address to your address book:

- You can use a REST client to make a POST request to the address recipients endpoint. The sandbox app does not yet support SOL blockchain:
cURL

curl -X POST \
     --url https://api-sandbox.circle.com/v1/addressBook/recipients \
     --header 'accept: application/json' \
     --header "Authorization: Bearer <YOUR_SANDBOX_API_KEY>" \
     --header 'content-type: application/json' \
     --data '{"idempotencyKey": "<YOUR_IDEMPOTENCY_KEY>", "address": "<YOUR_ADDRESS>", "chain": "SOL", "metadata": {"email": "", "bns": "", "nickname": "" } }'
- Record the value of the id from the successful response.
-
Click the POST /payouts menu item.

-
Complete the Amount field with a dollar amount you choose for your mock payment. 

-
Complete the id field with the values you recorded from the previous step.

-
Click the MAKE API CALL button.

-
Request testnet SOL:

-
In a new browser tab or window, navigate to the Sol Faucet at [https://solfaucet.com/](https://solfaucet.com/).

-
Complete the Enter Solana account address field with your Fantom deposit address from step 2.

-
Below the wallet address input field, select the amount of SOL tokens you want.

-
Select the Devnet environment. Your SOL should automatically be air dropped into your wallet upon success.

-
Make payouts in USDC from the Payouts API menu: 

-
From [sample-sandbox.circle.com](http://sample-sandbox.circle.com/), click the Payounts APIs menu item. 

-
Click  the POST /payouts menu item.

-
Complete the Amount field.

-
Set the Currency to USD. 

-
Complete the Destination field with the recipient ID that corresponds to your destination wallet address. 

-
Select address_book from the Destination Type drop-down. 

-
Complete the Beneficiary Email with your recipient email address.

-
Click the MAKE API CALL button.

-
Record the value of the id for your payout. 

-
Monitor your payouts from the Payouts API menu:

-
From [sample-sandbox.circle.com](http://sample-sandbox.circle.com/), click the Payounts APIs menu item. 

-
Click  the GET /payouts/{id} menu item.

-
Complete the Payout Id field with the value of your payout ID from the previous step.

-
Click the MAKE API CALL button to check the status of your payout.

##
Install Web3.js library to enable API calls
[](#install-web3js-library-to-enable-api-calls)
Install Web3.js by running the command below at the command line.

javasc

// Type this command to install libraries
npm install @solana/web3.js @solana/spl-token bs58
##
Customize sample JavaScript code by inputting variables
[](#customize-sample-javascript-code-by-inputting-variables)
In your text editor, create a new JavaScript file called send10.js. Review the commented code below to understand its structure. Copy and paste it into your new file:

JavaScript

// Write JavaScript Code
const bs58 = require('bs58');
const {
    Connection,
    Keypair,
    PublicKey,
    clusterApiUrl,
} = require("@solana/web3.js");

const { getOrCreateAssociatedTokenAccount, transfer } = require("@solana/spl-token");
// Replace these with your own keys and desired transfer amount
const PRIVATE_KEY = ""; // Your private key in Base58 encoding
const RECEIVER_PUBLIC_KEY = ""; // Receiver's public key
const TRANSFER_AMOUNT = 10000000; // 10 amount of USDC to transfer (in smallest unit)
// The address of the USDC token on Solana Devnet
const USDC_DEV_PUBLIC_KEY = "4zMMC9srt5Ri5X14GAgXhaHii3GnPAEERYPJgZJDncDU";
// Convert the private key from Base58 to a byte array and create a Keypair
const senderPrivateKeyBytes = bs58.decode(PRIVATE_KEY);
const senderKeypair = Keypair.fromSecretKey(senderPrivateKeyBytes);
// Create a new connection to the Solana Devnet
const connection = new Connection(clusterApiUrl("devnet"), "confirmed");
(async () => {
    try {
        // Fetch the sender's USDC token account
        const senderTokenAccount = await getOrCreateAssociatedTokenAccount(
            connection,
            senderKeypair,
            new PublicKey(USDC_DEV_PUBLIC_KEY),
            senderKeypair.publicKey,
        );
        // Fetch or create the receiver's associated token account for USDC
        const receiverTokenAccount = await getOrCreateAssociatedTokenAccount(
            connection,
            senderKeypair,
            new PublicKey(USDC_DEV_PUBLIC_KEY),
            new PublicKey(RECEIVER_PUBLIC_KEY),
            true, // Allow creating a token account for the receiver if it doesn't exist
        );
        // Perform the transfer
        const signature = await transfer(
            connection,
            senderKeypair,
            senderTokenAccount.address,
            receiverTokenAccount.address,
            senderKeypair.publicKey,
            TRANSFER_AMOUNT,
        );
        // Log the transaction signature
        console.log(`Transaction signature: ${signature}`);
        console.log(`You can verify the transaction on https://explorer.solana.com/tx/${signature}?cluster=devnet`);
    } catch (error) {
        console.error("Error performing the transfer:", error);
    }
})();

###
Modify the Code
[](#modify-the-code)
Replace the value of the PRIVATE_KEY variable with your secure private key, which must be in Base58 format. Replace the value of the RECEIVER_PUBLIC_KEY variable with the receiver’s public key.

##📘
Note: Transfer amount format

The set transfer amount value of 10000000 is equal to 10 USDC. This setting is because ISDC uses a 6 decimal place format, and the amount value should be in the smallest unit.

Save the Javascript file.

##
Execute JavaScript code to initiate USDC transfer
[](#execute-javascript-code-to-initiate-usdc-transfer)
From the terminal command line, enter the following command to execute the code and transfer USDC: 


// Type node filename.js to run code
node filename.js
You should see a similar output to the following code if your file executes successfully: 


> node send10.js

Transaction signature: dsLFw3rtDEWT2b3vpzournXspeTbeEZvzVAh3bmNaLzrqQB7nWrgj4rdh4j9gVb51p4mkDm1ivFUgkuC6Q7aehT
You can verify the transaction on https://explorer.solana.
Check the status of your USDC transaction by visiting the [Solana Devnet block explorer](https://explorer.solana.com/tx/2Uh8RV7afW1zNeyFoSBP6MXRjoVsdt8ZfGWsSQckPrtj14PaDySPxnTjxiHsuybiPRmzjRTvYSjdHVzRKSPUuUR4?cluster=devnet). 



##
Conclusion
[](#conclusion)
By completing this quickstart guide for USDC, you have started to use the essential skills of programmable money. You can adapt the script provided in this tutorial to enable real-world transactions and empower your own app development. 

Now, you can begin to apply this knowledge to create frictionless financial experiences to empower users with seamless, secure global transactions.

Updated 5 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/required-block-confirmations
Title: Required Block Confirmations
Description: undefined
Keywords: undefined

Before signing an attestation for a source chain event, Circle waits for a specified number of on-chain block confirmations to pass to determine finality. The following table displays the average time it takes for an attestation to become available after a source chain event, which includes the ~20 seconds it takes for the attestation service to observe and sign the source chain event.

Note: These values are subject to change.

###
Mainnet
[](#mainnet)
Source Chain	Number of Blocks	Average Time
Ethereum	~65*	~13 minutes
Avalanche	1	~20 seconds
OP Mainnet	~65 ETH blocks*	~13 minutes
Arbitrum	~65 ETH blocks*	~13 minutes
Noble	1	~20 seconds
Base	~65 ETH blocks*	~13 minutes
Polygon PoS	~200*	~8 minutes
Solana	32	~25 seconds
###
Testnet
[](#testnet)
Source Chain	Number of Blocks	Average Time
Ethereum Sepolia	5	~1 minute
Avalanche Fuji	1	~20 seconds
OP Sepolia	5	~20 seconds
Arbitrum Sepolia	5	~20 seconds
Noble Testnet	1	~20 seconds
Base Sepolia	5	~20 seconds
Polygon PoS Amoy	1	~20 seconds
Solana Devnet	32	~25 seconds
##📘
Block confirmations for L2s to Ethereum

Arbitrum, Base, and OP Mainnet are L2 blockchains that publish transaction data to their L1 blockchain, Ethereum. Before attesting to CCTP messages in an Ethereum block, Circle waits for the Ethereum block to finalize, which requires at least 65 block confirmations.

##📘
Block confirmations for Polygon PoS

We have currently set our time-to-attestation at 200 blocks for events on Polygon PoS mainnet. Deterministic finality (PIP-11) was rolled out on Polygon PoS mainnet in 2023, and we may reduce our time-to-attestation time as we observe PIP-11's performance.

Updated about 1 month ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/what-is-usdc
Title: What is USDC?
Description: undefined
Keywords: undefined

USDC is a digital dollar issued by Circle, also known as a stablecoin, running on many of the world’s leading blockchains. Designed to represent US dollars on the internet, USDC is backed 100% by highly liquid cash and cash-equivalent assets so that it's always redeemable 1:1 for USD.

Circle publishes monthly attestation reports for USDC reserve holdings on [our Transparency page](http://circle.com/en/usdc#transparency).

##📘
You may be interested in Cross-Chain Transfer Protocol []()

Cross-Chain Transfer Protocol (CCTP) is a permissionless on-chain utility that enables USDC to move securely between supported blockchain networks. Just like USDC, CCTP is open and free for [anyone to build upon](/stablecoins/docs/cctp-getting-started).

##
USDC Revolutionizes Commerce and Financial Services
[](#usdc-revolutionizes-commerce-and-financial-services)
Circle developed USDC to usher in a world where the US dollar is accessible to businesses and people around the globe without needing a traditional bank account. Digital currencies like USDC inherit the speed, efficiency, and security of public blockchain networks to bring about transformative benefits:

- Open and programmable
- Globally accessible
- Available 24/7/365
- Instant settlement at near-zero cost
Before USDC, businesses needed traditional banking solutions to accept payments, send remittances to vendors and suppliers, and manage their corporate treasuries. In contrast to USDC, legacy payment methods are slow, expensive, and limited by their lack of interoperability with other systems.

USDC makes money programmable, providing businesses and developers the ability to build innovative applications and services on an open platform that anyone can connect to -- globally, quickly, and cost-effectively.

##
How to Access USDC
[](#how-to-access-usdc)
Qualified businesses can apply for a [Circle Mint](https://developers.circle.com/circle-mint/docs) account to readily convert USD to and from USDC at no additional cost.

Developers can add support for USDC in their applications on a variety of public blockchain networks:

- Crypto-native developers often choose to integrate directly with the [USDC smart contract](https://developers.circle.com/stablecoins/edit/quickstart-transfer-10-usdc-on-chain) to design customizable funds flows for their applications.
- Developers new to the blockchain industry can benefit from [Circle's Web3 Services](https://developers.circle.com/w3s/docs) product suite, designed to make it easy to build applications that leverage the power of USDC.
- Developers can use [Cross-Chain Transfer Protocol](/stablecoins/docs/cctp-getting-started) to build novel cross-chain apps that stack together the various functionalities of trading, lending, payments, NFTs, gaming, and more, all while keeping things simple for users.
Updated 5 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/glossary
Title: Glossary
Description: undefined
Keywords: undefined

##
Airdrop
[](#airdrop)
A marketing tactic in which a company sends tokens of their cryptocurrency into the wallet address of a member of a blockchain at no charge, to raise awareness of their cryptocurrency.

##
Blockchain
[](#blockchain)
The fundamental basis of cryptocurrency. A blockchain is a digital ledger of transactions stored simultaneously on multiple nodes of the same network. Unlike traditional banking and payments solutions, a blockchain's open and decentralized network allows for instant, low-cost transactions where anyone can participate, while securing transactional history by cryptography and social consensus.

##
Burn
[](#burn)
To remove tokens from circulation permanently. See also [Mint](#mint)).

##
Chain
[](#chain)
A short-hand way to reference a blockchain. See also [blockchain](#blockchain).

##
Coin
[](#coin)
A short-hand way to reference a cryptocurrency (such as ETH) running on a given blockchain, or a single unit of that cryptocurrency. (See also [Token](#token)).

##
Custody
[](#custody)
The capacity of a third-party or an individual to retain and safeguard a user’s assets in a wallet address. Custody solutions can be either custodial or self custody, depending on which entity has control over the private keys of the wallet address. (See also [Private Keys]()).

##
ETH
[](#eth)
The native cryptocurrency built into the Ethereum blockchain that is used to pay gas (or network fees) and can be staked to secure the network. (See also [Gas]()).

##
Ethereum
[](#ethereum)
A public blockchain network with smart contract functionality.

##
EVM (Ethereum Virtual Machine)
[](#evm-ethereum-virtual-machine)
A virtual machine that executes code in a precise manner to create smart contracts on Ethereum. Many other blockchain networks are based on the EVM and are known as EVM-compatible blockchains.

##
Faucet
[](#faucet)
A developer tool that issues testnet tokens of no monetary value to be used for testing payment flows and interactions with smart contracts on blockchain test networks.

##
Gas
[](#gas)
A network fee charged in ETH by EVM-compatible blockchains to execute transactions. Non-EVM blockchains such as Solana charge network fees in SOL. End users pay the fee by default, although developers can sponsor the fee to provide a simpler user experience.

##
Mint
[](#mint)
To generate new tokens and put them into circulation (see also [Burn](#burn)).

##
NFT
[](#nft)
A non-fungible token is a unique digital identifier that is recorded on a blockchain, and is used to certify ownership and authenticity. It cannot be copied, substituted, or subdivided.

##
On-chain
[](#on-chain)
A short-hand way to reference "on the blockchain". Often used to denote an activity that takes place on a blockchain.

##
Primitive
[](#primitive)
A fundamental building block that developers can build on top of on a blockchain network.

##
Private key
[](#private-key)
An encrypted password that safeguards a user’s holdings in a wallet address. This can take the form of a data string of letters and numbers 64 characters or longer. A user (or a user's app) must present a private key to authorize a transaction. Translated to the world of traditional finance, the private key could be compared to a personal signature or PIN code.

##
Public key
[](#public-key)
A public-facing address of a user’s wallet that must be shared to receive funds. Every wallet address has both a private key and a public key. Translated to the world of traditional finance, the public key could be compared to an account number or ATM card.

##
Smart contract
[](#smart-contract)
A protocol on a blockchain network that is facilitated, verified, or enforced automatically with no third-party approval or interaction required.

##
Stablecoin
[](#stablecoin)
A cryptocurrency that is pegged to an off-chain reserve asset like the US dollar or the euro. Stablecoins are designed to provide a sound store of value and facilitate payments in a familiar currency. Circle issues two stablecoins, USDC and EURC, which reflect 1:1 the value of the US dollar and the euro respectively.

##
Testnet
[](#testnet)
A blockchain network used for testing purposes by developers, similar to a staging environment. A testnet will operate just like its corresponding mainnet (similar to a production environment), but the transactions on a testnet use tokens of no monetary value.

##
Token
[](#token)
A cryptocurrency running on a given blockchain, or a single unit of that cryptocurrency. (See also [Coin](#coin))

##
USDC
[](#usdc)
A US dollar-backed stablecoin issued by Circle on public blockchain networks. USDC is fully reserved and can always be exchanged 1:1 for US dollars.

##
Wallet
[](#wallet)
A software or hardware device that is designed to hold and safeguards a user’s wallet address containing their digital assets (e.g. tokens or NFTs).

##
Wallet address
[](#wallet-address)
A data string that identifies a location where digital assets are stored on a public blockchain. Similar to a public key, however a public key serves to facilitate transactions for a wallet address.

Updated 5 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/transfer-usdc-on-testnet-from-ethereum-to-avalanche
Title: Transfer USDC on testnet from Ethereum to Avalanche
Description: undefined
Keywords: undefined

To get started with CCTP, follow the example script provided [here](https://github.com/circlefin/evm-cctp-contracts/blob/d1c24577fb627b08483dc42e4d8a37a810b369f7/docs/index.js). The example uses [web3.js ](https://web3js.readthedocs.io/en/v1.8.1/getting-started.html)to transfer USDC from a wallet address on Ethereum Sepolia testnet to another wallet address on Avalanche Fuji testnet.

##📘
Interactive Tutorial with Web3 Services

If you are new to building smart contracts, check out our [interactive tutorial](https://learn.circle.com/quickstarts/smart-contract-platform-cctp) where you can transfer USDC using Circle's Web3 Services: Smart Contract Platform and Programmable Wallets.

The script has 5 steps:

- In this first step, you initiate a transfer of USDC from one blockchain to another, and specify the recipient wallet address on the destination chain. This step approves the Ethereum Sepolia TokenMessenger contract to withdraw USDC from the provided Ethereum Sepolia wallet address.
JavaScript

const approveTx = await usdcEthContract.methods.approve(ETH_TOKEN_MESSENGER_CONTRACT_ADDRESS, amount).send({gas: approveTxGas})
- In this second step, you facilitate a burn of the specified amount of USDC on the source chain. This step executes depositForBurn function on the Ethereum Sepolia TokenMessenger contract deployed on [Sepolia testnet](https://sepolia.etherscan.io/address/0x9f3B8679c73C2Fef8b59B4f3444d4e156fb70AA5).
JavaScript

const burnTx = await ethTokenMessengerContract.methods.depositForBurn(amount, AVAX_DESTINATION_DOMAIN, destinationAddressInBytes32, USDC_ETH_CONTRACT_ADDRESS).send();
- In this third step, you make sure you have the correct message and hash it. This step extracts messageBytes emitted by MessageSent event from depositForBurn transaction logs and hashes the retrieved messageBytes using the keccak256 hashing algorithm.
JavaScript

const transactionReceipt = await web3.eth.getTransactionReceipt(burnTx.transactionHash);
const eventTopic = web3.utils.keccak256('MessageSent(bytes)')
const log = transactionReceipt.logs.find((l) => l.topics[0] === eventTopic)
const messageBytes = web3.eth.abi.decodeParameters(['bytes'], log.data)[0]
const messageHash = web3.utils.keccak256(messageBytes);
- In this fourth step, you request the attestation from Circle, which provides authorization to mint the specified amount of USDC on the destination chain. This step polls the attestation service to acquire the signature using the messageHash from the previous step.
##📘
Rate Limit

The attestation service rate limit is 10 requests per second. If you exceed 10 requests per second, the service blocks all API requests for the next 5 minutes and returns an HTTP 429 response.

JavaScript

let attestationResponse = {status: 'pending'};
while(attestationResponse.status != 'complete') {
    const response = await fetch(`https://iris-api-sandbox.circle.com/attestations/${messageHash}`);
    attestationResponse = await response.json()
    await new Promise(r => setTimeout(r, 2000));
}
- In this final step, you enable USDC to be minted on the destination chain. This step calls the receiveMessage function on the Avalanche Fuji MessageTransmitter contract to receive USDC at the Avalanche Fuji wallet address.
JavaScript

const receiveTx = await avaxMessageTransmitterContract.receiveMessage(receivingMessageBytes, signature);
Updated about 1 month ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/message-format
Title: Message Format
Description: undefined
Keywords: undefined

##
Message Header
[](#message-header)
The top-level message header format is standard for all messages passing through CCTP:

Field	Bytes	Type	Index	Description
version	4	uint32	0	Version identifier
sourceDomain	4	uint32	4	Source domain id
destinationDomain	4	uint32	8	Destination domain id
nonce	8	uint64	12	Unique message nonce
sender	32	bytes32	20	Address of MessageTransmitter caller on source domain
recipient	32	bytes32	52	Address to handle message body on destination domain
destinationCaller	32	bytes32	84	Address permitted to call MessageTransmitter on destination domain, or bytes32(0) if message can be received by any address
messageBody	dynamic	bytes	116	Application-specific message to be handled by recipient
##📘
Why we use bytes32 type for addresses

CCTP is built to support EVM chains, which use 20 byte addresses, and non-EVM chains, many of which use 32 byte addresses. We provide a [Message.sol library](https://github.com/circlefin/evm-cctp-contracts/blob/40111601620071988e94e39274c8f48d6f406d6d/src/messages/Message.sol#L145-L157) as a reference implementation for converting between address and bytes32 in Solidity.

##
Message Body
[](#message-body)
The message format includes a dynamically sized messageBody field, used for application-specific messages. For example, TokenMessenger defines a [BurnMessage](https://github.com/circlefin/evm-cctp-contracts/blob/master/src/messages/BurnMessage.sol) with data related to cross-chain transfers:

Field	Bytes	Type	Index	Description
version	4	uint32	0	Version identifier
burnToken	32	bytes32	4	Address of burned token on source domain
mintRecipient	32	bytes32	36	Address to receive minted tokens on destination domain
amount	32	uint256	68	Amount of burned tokens
messageSender	32	bytes32	100	Address of caller of depositForBurn (or depositForBurnWithCaller) on source domain
##
Message Nonce
[](#message-nonce)
A message nonce is a unique identifier for a message that can only be used once on the destination domain. The next available nonce on a source domain is an integer. On the destination domain, messages can be received in any order, and used nonces are stored as a hash of the source domain and nonce integer value.

Updated 5 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/generic-message-passing
Title: Generalized Message Passing
Description: undefined
Keywords: undefined

Cross-Chain Transfer Protocol (CCTP) uses generalized message passing to facilitate the native burning and minting of USDC across supported blockchains, also known as [domains](/stablecoins/docs/supported-domains). Message passing is a three-step process:

- An on-chain component on source domain emits a message.
- Circle's off-chain attestation service signs the message.
- The on-chain component at the destination domain receives the message, and forwards the message body to the specified recipient.
##
Architecture
[](#architecture)
On-chain components on all domains have the same purpose, but implementation differs between EVM-compatible and non-EVM domains.

###
CCTP on EVM Domains
[](#cctp-on-evm-domains)
The relationship between CCTP's on-chain components and Circle's off-chain attestation service is illustrated below for a burn-and-mint of USDC between EVM-compatible domains:


On EVM domains, the on-chain component for cross-chain burning and minting is called TokenMessenger, which is built on top of Message Transmitter, an on-chain component for generalized message passing.

In the diagram above, a token depositor calls the [TokenMessenger#depositForBurn](https://github.com/circlefin/evm-cctp-contracts/blob/adb2a382b09ea574f4d18d8af5b6706e8ed9b8f2/src/TokenMessenger.sol#L169) function to deposit a native token (such as USDC), which delegates to the TokenMinter contract to burn the token. The TokenMessenger contract then sends a message via the [MessageTransmitter#sendMessage](https://github.com/circlefin/evm-cctp-contracts/blob/adb2a382b09ea574f4d18d8af5b6706e8ed9b8f2/src/MessageTransmitter.sol#L108) function. After [sufficient block confirmations](/stablecoins/docs/required-block-confirmations), Circle's off-chain attestation service, Iris, signs the message. An API consumer queries this attestation and submits it on-chain to the destination domain's [MessageTransmitter#receiveMessage](https://github.com/circlefin/evm-cctp-contracts/blob/adb2a382b09ea574f4d18d8af5b6706e8ed9b8f2/src/MessageTransmitter.sol#L250) function. For more details, see [Quickstart: Cross-chain USDC transfer](/stablecoins/docs/quickstart-cross-chain-usdc-transfer).

To send an arbitrary message, directly call [MessageTransmitter#sendMessage](https://github.com/circlefin/evm-cctp-contracts/blob/adb2a382b09ea574f4d18d8af5b6706e8ed9b8f2/src/MessageTransmitter.sol#L108). Note that the message recipient must implement [IMessageHandler#handleReceiveMessage](https://github.com/circlefin/evm-cctp-contracts/blob/master/src/interfaces/IMessageHandler.sol#L31C14-L31C34). It is not currently possible to perform a burn-and-mint of USDC and add arbitrary data to the same message; arbitrary data must be included in a separate message.

###
CCTP on Non-EVM Domains
[](#cctp-on-non-evm-domains)
####
Noble
[](#noble)
Noble is a Cosmos application-specific blockchain (or "appchain") that provides native asset issuance for the Cosmos ecosystem. USDC is natively issued on Noble and can be transferred via the Inter-Blockchain Communication (IBC) protocol to other supported appchains in Cosmos, or via CCTP to any supported domain (e.g. Ethereum).

Note that there are key differences between Cosmos appchains like Noble and EVM-compatible blockchains. Unlike on EVM domains where CCTP is a set of smart contracts, CCTP on Noble is a Cosmos SDK module, which is deployed by Noble governance and built into the Noble blockchain. Cosmos appchains can use IBC to build composable flows with CCTP on Noble.

####
Solana
[](#solana)
Solana is a layer-1 blockchain where USDC is natively issued as an SPL-token. CCTP is deployed to Solana as two Anchor programs: MessageTransmitter and TokenMessengerMinter. Developers can compose programs on top of CCTP programs through CPI's (Cross-Program Invocations). Arbitrary messages can be sent by directly calling MessageTransmitter#send_message just like as described in the EVM section above.

Updated about 1 month ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/solana-programs
Title: Solana Programs
Description: undefined
Keywords: undefined

#
Overview
[](#overview)
Solana CCTP programs are written in Rust and leverage the Anchor framework. The Solana CCTP protocol implementation is split into two programs: MessageTransmitter and TokenMessengerMinter. TokenMessengerMinter encapsulates the functionality of both TokenMessenger and TokenMinter contracts on EVM chains. To ensure alignment with EVM contracts' logic and state, and to facilitate future upgrades and maintenance, the code and state of Solana programs reflect the EVM counterparts as closely as possible.

##
Mainnet Program Addresses
[](#mainnet-program-addresses)
Program	[Domain](/stablecoins/docs/supported-domains)	Address
MessageTransmitter	5	CCTPmbSD7gX1bxKPAmg77w8oFzNFpaQiQUWD43TKaecd
TokenMessengerMinter	5	CCTPiPYPc6AsJuwueEnWgSgucamXDZwBd53dQ11YiKX3
##
Devnet Program Addresses
[](#devnet-program-addresses)
Program	[Domain](/stablecoins/docs/supported-domains)	Address
MessageTransmitter	5	CCTPmbSD7gX1bxKPAmg77w8oFzNFpaQiQUWD43TKaecd
TokenMessengerMinter	5	CCTPiPYPc6AsJuwueEnWgSgucamXDZwBd53dQ11YiKX3
The Solana CCTP source code is available at [https://github.com/circlefin/solana-cctp-contracts/](https://github.com/circlefin/solana-cctp-contracts/). The interface below serves as a reference for permissionless messaging functions exposed by the programs.

#
Interface
[](#interface)
The interface below serves as a reference for permissionless messaging functions exposed by the TokenMessengerMinter and MessageTransmitter programs. The full IDLs can be found [here](https://explorer.solana.com/address/CCTPiPYPc6AsJuwueEnWgSgucamXDZwBd53dQ11YiKX3/anchor-program) and [here](https://explorer.solana.com/address/CCTPmbSD7gX1bxKPAmg77w8oFzNFpaQiQUWD43TKaecd/anchor-program).

Please see the instruction rust files or quick-start for PDA information.

##
TokenMessengerMinter
[](#tokenmessengerminter)
###
[depositForBurn](https://github.com/circlefin/solana-cctp-contracts/blob/master/programs/token-messenger-minter/src/token_messenger/instructions/deposit_for_burn.rs)
[](#depositforburn)
Deposits and burns tokens from sender to be minted on destination domain. Minted tokens will be transferred to mintRecipient.

Parameters

Field	Type	Description
amount	u64	Amount of tokens to deposit and burn.
destinationDomain	u32	Destination domain identifier.
mintRecipient	Pubkey	Public Key of token account mint recipient on destination domain. Address should be the 32 byte version of the hex address in base58. See Additional Notes on mintRecipient section for more information.
##📘
MessageSent event storage

To ensure persistent and reliable message storage, MessageSent events are stored in accounts. MessageSent event accounts are generated client-side, passed into the instruction call, and assigned to have the MessageTransmitter program as the owner. Please see the [Quickstart Guide](https://developers.circle.com/stablecoins/docs/transfer-testnet-usdc-between-solana-devnet) for how to generate this account and pass it to the instruction call.

For depositForBurn messages, this costs ~0.00295104 SOL in rent. This rent is paid by the [event_rent_payer](https://github.com/circlefin/solana-cctp-contracts/blob/master/programs/message-transmitter/src/instructions/send_message.rs#L16C9-L16C25) account which can be the user or subsidized by a calling program or integrator.

Once an attestation is available and the message has been received on the destination chain, the event account can be closed and have the SOL reclaimed to the event_rent_payer account. This is done by calling the [reclaim_event_account](https://github.com/circlefin/solana-cctp-contracts/blob/master/programs/message-transmitter/src/instructions/reclaim_event_account.rs) instruction. This can only be called by the event_rent_payer account from when the message was sent.

Details on the message format can be found on the [Message Format page](https://developers.circle.com/stablecoins/docs/message-format).

###
[depositForBurnWithCaller](https://github.com/circlefin/solana-cctp-contracts/blob/master/programs/token-messenger-minter/src/token_messenger/instructions/deposit_for_burn_with_caller.rs)
[](#depositforburnwithcaller)
The same as depositForBurn but with an additional parameter, destinationCaller. This parameter specifies which address has permission to call receiveMessage on the destination domain for the message.

Parameters

Field	Type	Description
amount	u64	Amount of tokens to deposit and burn.
destinationDomain	u32	Destination domain identifier.
mintRecipient	Pubkey	Public Key of mint recipient on destination domain. Address should be converted to base58. See [Mint Recipient for Solana as Source Chain Transfers](Mint Recipient for Solana as Source Chain Transfers)
destinationCaller	Pubkey	Public Key of caller on destination domain. Address should be converted to base58.
###
[replaceDepositForBurn](https://github.com/circlefin/solana-cctp-contracts/blob/master/programs/token-messenger-minter/src/token_messenger/instructions/replace_deposit_for_burn.rs)
[](#replacedepositforburn)
Replace a BurnMessage to change the mint recipient and/or destination caller. Allows the sender of a previous BurnMessage (created by depositForBurn or depositForBurnWithCaller) to send a new BurnMessage to replace the original. The new BurnMessage will reuse the amount and burn token of the original, without requiring a new deposit.

This is useful in situations where the user specified an incorrect address and has no way to safely mint the previously burned USDC.

##📘
Note on replaceDepositForBurn

Only the owner account of the original depositForBurn has access to call replaceDepositForBurn. The resulting mint will supersede the original mint, as long as the original mint has not confirmed yet on-chain. When using a third-party app/bridge that integrates with CCTP to burn and mint USDC, it is the choice of the app/bridge if and when to replace messages on behalf of users. When sending USDC to smart contracts, be aware of the functionality that those contracts have and their respective trust model.

Parameters

Field	Type	Description
originalMessage	Vec	Original message bytes (to replace).
originalAttestation	Vec	Original attestation bytes.
newDestinationCaller	Pubkey	The new destination caller, which may be the same as the original destination caller, a new destination caller, or an empty destination caller, indicating that any destination caller is valid. Address should be converted to base58.
newMintRecipient	Pubkey	The new mint recipient, which may be the same as the original mint recipient, or different. Address should be converted to base58.
##
MessageTransmitter
[](#messagetransmitter)
###
[receiveMessage](https://github.com/circlefin/solana-cctp-contracts/blob/master/programs/message-transmitter/src/instructions/receive_message.rs)
[](#receivemessage)
Messages with a given nonce can only be broadcast successfully once for a pair of domains. The message body of a valid message is passed to the specified recipient for further processing.

Parameters

Field	Type	Description
message	Vec	Message bytes.
attestation	Vec	Signed attestation of message.
Remaining Accounts

If the receiveMessage instruction is being called with a deposit for burn message that will be received by the TokenMessengerMinter, additional remainingAccounts are required so they can be passed with the CPI to TokenMessengerMinter#handleReceiveMessage:

Account Name	PDA Seeds	PDA ProgramId	isSigner?	isWritable?	Description
token_messenger	["token_messenger"]	tokenMessengerMinter	false	false	TokenMessenger Program Account
remote_token_messenger	["remote_token_messenger", sourceDomainId]	tokenMessengerMinter	false	false	Remote token messenger account where the remote token messenger address is stored for the given source domain id
token_minter	["token_minter"]	tokenMessengerMinter	false	true	TokenMinter Program Account
local_token	["local_token", localTokenMint.publicKey]	tokenMessengerMinter	false	true	Local token account where the information for the local token (e.g. USDCSOL) being minted is stored
token_pair	["token_pair", sourceDomainId, sourceTokenInBase58]	tokenMessengerMinter	false	false	Token pair account where the info for the local and remote tokens are stored. sourceTokenInBase58 is the remote token that was burned converted into base58 format.
user_token_account	N/A	N/A	false	true	User token account that will receive the minted tokens. This address must match the mintRecipient from the source chain depositForBurn call.
custody_token_account	["custody", localTokenMint.publicKey]	tokenMessengerMinter	false	true	Custody account that holds the pre-minted USDCSOL that can be minted for CCTP usage.
SPL.token_program_id	N/A	N/A	false	false	The native SPL token program ID.
token_program_event_authority	["__event_authority"]	tokenMessengerMinter	false	false	Event authority account for the TokenMessengerMinter program. Needed to emit Anchor CPI events.
program	N/A	N/A	false	false	Program id for the TokenMessengerMinter program.
###
[sendMessage](https://github.com/circlefin/solana-cctp-contracts/blob/master/programs/message-transmitter/src/instructions/send_message.rs)
[](#sendmessage)
Sends a message to the destination domain and recipient. Emits a MessageSent event which will be attested by Circle’s attestation service.

Parameters

Field	Type	Description
destinationDomain	u32	Destination domain identifier.
recipient	Pubkey	Address to handle message body on destination domain.
messageBody	Vec	Application-specific message to be handled by recipient.
###
[sendMessageWithCaller](https://github.com/circlefin/solana-cctp-contracts/blob/master/programs/message-transmitter/src/instructions/send_message_with_caller.rs)
[](#sendmessagewithcaller)
Same as sendMessage but with an additional parameter, destinationCaller. This parameter specifies which address has permission to call receiveMessage on the destination domain for the message.

Parameters

Field	Type	Description
destinationDomain	u32	Destination domain identifier.
recipient	Pubkey	Address of message recipient on destination domain.
destinationCaller	Pubkey	Address of caller on the destination domain. Address should be converted to base58.
messageBody	Vec	Application-specific message to be handled by recipient.
###
[replaceMessage](https://github.com/circlefin/solana-cctp-contracts/blob/master/programs/message-transmitter/src/instructions/replace_message.rs)
[](#replacemessage)
Replace a message with a new message body and/or destination caller. The originalAttestation must be a valid attestation of originalMessage, produced by Circle’s attestation service.

Parameters

Field	Type	Description
originalMessage	Vec	Original message to replace.
originalAttestation	Vec	Attestation of originalMessage.
newMessageBody	Vec	New message body of replaced message.
newDestinationCaller	Pubkey	The new destination caller, which may be the same as the original destination caller, a new destination caller, or an empty destination caller (bytes32(0) or PublicKey.default, indicating that any destination caller is valid). Address should be converted to base58.
###
[reclaimEventAccount](https://github.com/circlefin/solana-cctp-contracts/blob/master/programs/message-transmitter/src/instructions/reclaim_event_account.rs)
[](#reclaimeventaccount)
Closes the given event account and reclaims the paid rent in SOL back to the event_rent_payer account. This instruction can only be called by the event_rent_payer account that paid the rent when the message was sent.

Parameters

Field	Type	Description
attestation	Vec	Valid attestation for the message stored in the account. This is required to ensure the attestation service has processed and stored the message before it is deleted.
##🚧
Warning

Once this instruction is executed for a message account, the message can no longer be read on-chain. We recommend not calling this instruction until the message has been received on the destination chain. If the message is lost before receiving the message, it can be fetched from the attestation service using the [messages endpoint](https://developers.circle.com/stablecoins/reference/getmessages).

#
Additional Notes
[](#additional-notes)
##
Mint Recipient for Solana as Destination Chain Transfers
[](#mint-recipient-for-solana-as-destination-chain-transfers)
When calling depositForBurn on a non-Solana chain with Solana as the destination, the mintRecipient should be a hex encoded USDC token account address. The token account must exist at the time receiveMessage is called on Solana or else this instruction will revert. An example of converting an address from Base58 to hex taken from the Solana quickstart tutorial in Typescript can be seen below:

TypeScript

import { bs58 } from "@coral-xyz/anchor/dist/cjs/utils/bytes";
import { hexlify } from "ethers";

const solanaAddressToHex = (solanaAddress: string): string => hexlify(bs58.decode(solanaAddress));
##
Mint Recipient for Solana as Source Chain Transfers
[](#mint-recipient-for-solana-as-source-chain-transfers)
When specifying the mintRecipient for Solana depositForBurn instruction calls, the address must be given as the 32 byte version of the hex address in base58 format. An example taken fro the Solana quickstart tutorial in Typescript can be seen below:

TypeScript

import { getBytes } from 'ethers';
import { PublicKey } from '@solana/web3.js';

const evmAddressToBytes32 = (address: string): string => `0x000000000000000000000000${address.replace("0x", "")}`;

const evmAddressToBase58PublicKey = (addressHex: string): PublicKey => new PublicKey(getBytes(evmAddressToBytes32(addressHex)));
##
Program Events
[](#program-events)
Program events like [DepositForBurn](https://github.com/circlefin/solana-cctp-contracts/blob/master/programs/token-messenger-minter/src/token_messenger/events.rs#L35-L45) , [MintAndWithdraw](https://github.com/circlefin/solana-cctp-contracts/blob/master/programs/token-messenger-minter/src/token_messenger/events.rs#L47-L52) , and [MessageReceived](https://github.com/circlefin/solana-cctp-contracts/blob/master/programs/token-messenger-minter/src/token_messenger/events.rs#L47-L52) are emitted as Anchor CPI events. This means a self-CPI is made into the program with the serialized event as instruction data so it is persisted in the transaction and can be fetched later on as needed. More information can be seen in the [Anchor implementation PR](https://github.com/coral-xyz/anchor/pull/2438), and an example of reading CPI events can be seen [here](https://github.com/circlefin/solana-cctp-contracts/blob/master/tests/utils.ts#L62-L111).

[MessageSent](https://github.com/circlefin/solana-cctp-contracts/blob/master/programs/message-transmitter/src/events.rs#L49-L55) events are different, as they are stored in accounts. Please see the [MessageSent Event Storage section](/stablecoins/docs/solana-programs#depositforburn) for more info.

Updated 16 days ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/quickstart-transfer-10-usdc-on-chain
Title: Quickstart: Transfer 10 USDC On-Chain
Description: undefined
Keywords: undefined

You can utilize USDC to move money near-instantly around the globe using decentralized protocols. This guide describes the steps to send 10 USDC between two wallets using the Ethereum Sepolia testnet network. To complete the steps in this guide, you use third-party tooling, such as Javascript libraries, node services, and wallets.

This guide provides the foundational knowledge you can build on to integrate USDC payment flows into your app or platform. You can adapt the sample code in this guide to serve as a framework for your development. Note that all API calls and transactions in this guide occur on a testnet environment. The tokens you use have no financial value.

After you validate and optimize your app in a testnet environment, you can migrate to a production or mainnet environment and create transactions that do transfer financial value using USDC.

##
Overview of USDC and its token contract
[](#overview-of-usdc-and-its-token-contract)
USDC is a stablecoin backed 1:1 to the U.S dollar and operates natively on many public blockchains. Like many digital currencies, USDC is powered by a token contract, which is a programmable piece of code that manages user balances autonomously across a decentralized network. As transactions occur, the token contract automatically updates the digital ledger, ensuring real-time tracking of funds. This mechanism allows individuals and businesses to send and receive dollars seamlessly, capitalizing on the transparency, security, and efficiency of blockchain technology. 

##
Before you get started
[](#before-you-get-started)
For this tutorial, you need the following apps, tools, and accounts:

-
[Circle Mint Sandbox Account](https://app-sandbox.circle.com/signup/sandbox): Get an account that provides access to testnet USDC. 

-
[MetaMask](https://metamask.io/): A resource for creating wallets, which are devices for storing USDC on chain:

- Create two wallets, one for sending and one for receiving testnet USDC transactions on the Ethereum “Sepolia” testnet network. These will serve as the origin wallet (the one you’re sending USDC from) and the destination wallet (the one you’re sending to). Note the addresses for both. 
- Note the private key for the origin wallet. 
- Import the Ethereum Sepolia testnet [USDC token contract](https://sepolia.etherscan.io/address/0x1c7D4B196Cb0C7B01d743Fbc6116a902379C7238) into your origin wallet to enable it to read the token balance. The contract’s address is 0x1c7D4B196Cb0C7B01d743Fbc6116a902379C7238. 
To view the testnet USDC smart contracts for supported blockchains, see [USDC on Test Networks](https://developers.circle.com/developer/docs/usdc-on-testnet).

-
[Infura](https://www.infura.io/): A node service for connecting to the blockchain and submitting your USDC transaction: 

- Create an API key for the Web3 API offering. 
- Note the Ethereum “Sepolia” testnet endpoint provided. 
-
[Node.js](https://nodejs.org/en): Use the JavaScript runtime environment to run the code locally on your machine.

-
[Ethers.js](https://ethers.org/): JavaScript library containing functions to call the USDC token contract and initiate transactions.

-
[Visual Studio](https://code.visualstudio.com/): a lightweight, but powerful, source code editor available as a desktop app.

##
1. Set up a Circle Mint sandbox account
[](#1-set-up-a-circle-mint-sandbox-account)
Set up a sandbox account so you can get testnet USDC tokens to use for testing.

- Visit [Circle's sandbox signup page](https://app-sandbox.circle.com/signup/sandbox) and click Sign Up.
- Log in to the sandbox account you create.
- Click APIs in the left pane, then click CREATE A KEY in the API Keys section.
- Provide a name for the key, then click CREATE KEY.
- Copy the key value and store it a location you can easily access. You use the key for steps later in this topic.
##
2. Obtain testnet USDC and native gas token
[](#2-obtain-testnet-usdc-and-native-gas-token)
Use the Circle sample app to get testnet USDC to transfer, and testnet ETH to pay the gas (transaction fee) for the transaction.

- Configure the sample app to use you API key: 
- Navigate to [sample-sandbox.circle.com](http://sample-sandbox.circle.com/) 
- Click on the Settings cog wheel in the top-right corner.
- In the Your API Key field, paste the API key you created.
- Click on the menu (three horizontal bars) in the top-left corner, to display available API endpoints.
- Mint testnet USDC via simulated bank transfer in the Core Functionality set:
- Click POST /businessAccount/banks/wires. 
- Click PREFILL FORM to add a simulated bank account for testing.
- Click MAKE API CALL to simulate a request.
- Scroll down to display the response to the request, then copy the value for the id in the response. It should look similar to the following:
"id": "d783e25e-e8b3-42eb-90b4-626f7a9580ce"
This is the Account ID to use for the wire transfer.
- Click GET /businessAccount/banks/wires/{id}/instructions.
- In the Account ID field, paste the id value from the previous step.
- Click MAKE API CALL.
- From the Response section, copy and save the values for trackingRef and accountNumber. 
- Click POST /mocks/payments/wire.
- In the Tracking Ref field, paste the value for trackingRef from the previous step.
- In the Account Number field, paste the value for accountNumber from the previous step. 
- In the Amount field, enter 10.00.
- Click MAKE API CALL.
- Add recipient address from the Payouts API set: 
- Click POST /addressBook/recipients.
- In the Address field, enter your origin wallet address.
- In the Blockchain field, select ETH.
- In the Email field, enter your email address (as recipient).
- In the bns field, enter the nickname for your destination wallet.
- In the Nickname field, enter your user nickname.
- Click MAKE API CALL.
Copy and store the ID from the response.
- Click GET addressBook/recipients/{id}.
- In the Recipient Id field, enter the ID from the previous step.
- Click MAKE API CALL.
In the Response, confirm that the request successfully added the address.
- Perform a payout in USDC and native token (ETH) for gas fees from the Payouts API set:
- Click POST /payouts.
- In the Amount field, enter 10.00.
- Set the To Currency to USD.
- In the Destination field, enter the should be the recipient ID that corresponds to the destination wallet address.
- For Destination Type, select address_book.
- In the Beneficiary Email field, enter the email address of the recipient.
- Click MAKE API CALL.
- From the Response, copy the ID for the payout.
Repeat these steps, but choose ETH for the To Currency field, to provide native tokens to cover gas fees for the transaction.
- Click GET /payouts/{id}.
- In the Payout Id field, enter the payout ID from the previous step.
- Click MAKE API CALL.
View the Response to monitor the payout status. 
##
3. Install Ethers.js library to enable API calls
[](#3-install-ethersjs-library-to-enable-api-calls)
Run the following command to install [Ethers.js](https://ethers.org/):


npm install ethers
##
4. Customize the sample JavaScript code with input variables
[](#4-customize-the-sample-javascript-code-with-input-variables)
In Visual Studio Code, create a new JavaScript file named send10.js. Copy the following code sample and paste it into the send10.js file:

JavaScript

const { ethers } = require('ethers');

// 1. Input the Sepolia RPC endpoint
const rpcEndpoint = '<RPC_ENDPOINT_URL>'; // Insert the INFURA Network Endpoint

// 2. Initialize the Ethers.js provider
const provider = new ethers.JsonRpcProvider(rpcEndpoint);

// 3. Input USDC token contract address for Ethereum Sepolia
const tokenAddress = '<USDC_TOKEN_CONTRACT>'; // USDC TokenAddress

// 4. Define the USDC token contract ABI
const minTokenAbi = [
  {
    inputs: [
      { internalType: 'address', name: 'to', type: 'address' },
      { internalType: 'uint256', name: 'value', type: 'uint256' },
    ],
    name: 'transfer',
    outputs: [{ internalType: 'bool', name: '', type: 'bool' }],
    stateMutability: 'nonpayable',
    type: 'function',
  },
  {
    inputs: [{ internalType: 'address', name: 'account', type: 'address' }],
    name: 'balanceOf',
    outputs: [{ internalType: 'uint256', name: '', type: 'uint256' }],
    stateMutability: 'view',
    type: 'function',
  },
  {
    inputs: [],
    name: 'decimals',
    outputs: [{ internalType: 'uint8', name: '', type: 'uint8' }],
    stateMutability: 'view',
    type: 'function',
  },
];

// 5. Create a new contract instance
const contract = new ethers.Contract(tokenAddress, minTokenAbi, provider);

// 6. Input the addresses and the private key; specify number of tokens to send
const senderAddress = '<SENDER_WALLET_ADDRESS>';
const recipientAddress = '<RECIPIENT_WALLET_ADDRESS>';
const senderPrivateKey = '<SENDER_WALLET_PRIVATE_KEY>';

const usdcAmount = 1.0;

async function main() {
  // 7. Check the number of decimals for the USDC token
  const decimals = await contract.decimals();

  // 8. Check the balance of the sender's address
  const balance = await contract.balanceOf(senderAddress);
  console.log('Sender USDC balance:', ethers.formatUnits(balance, decimals));

  // 9. Calculate the actual amount in the smallest unit
  const value = ethers.parseUnits(usdcAmount.toString(), decimals);

  // 10. Create the transaction
  const tx = await contract.connect(new ethers.Wallet(senderPrivateKey, provider)).transfer(recipientAddress, value);

  // 11. Wait for the transaction to be mined and log the transaction hash
  const receipt = await tx.wait();
  console.log('Tx Hash:', receipt);
}

main().catch((error) => {
  console.error('Error:', error);
  process.exit(1);
});
###
Customize Code
[](#customize-code)
To customize the code for this guide, replace the following variable values: 

- Under comment // 2 replace <RPC_ENDPOINT_URL> with the INFURA endpoint.
- Under comment // 4, replace <USDC_TOKEN_CONTRACT> with the token contract address: 0x07865c6E87B9F70255377e024ace6630C1Eaa37F.
- Under comment // 6:
- Replace <SENDER_WALLET_ADDRESS> with the origin wallet address.
- Replace <RECIPIENT_WALLET_ADDRESS> with the destination wallet address.
- Replace <SENDER_WALLET_PRIVATE_KEY> with the origin wallet’s private key.
- Update the value for usdcAmount to the number of USDC to send. For this example, 10.
Save the Javascript file.

##
5. Execute JavaScript code to initiate USDC transfer
[](#5-execute-javascript-code-to-initiate-usdc-transfer)
In Terminal, enter the following command to execute the code and transfer USDC. 


// Type node <FILENAME>.js to run code
node send10.js
Check the status of your USDC transaction by visiting the [Sepolia testnet block explorer](https://sepolia.etherscan.io/):

Paste the transactionHash value from your terminal log into the search bar. Under Status, “Success” means the transaction successfully settled. 

Congratulations! Completing this quickstart guide for USDC has equipped you with the essential skills of programmable money.

You can adapt the script provided in this guide to enable real-world transactions and empower your own app development. Now, you can apply this newfound knowledge to create frictionless financial experiences, empowering users with seamless, secure global transactions.

Updated 17 days ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/generic-message-passing
Title: Generalized Message Passing
Description: undefined
Keywords: undefined

Cross-Chain Transfer Protocol (CCTP) uses generalized message passing to facilitate the native burning and minting of USDC across supported blockchains, also known as [domains](/stablecoins/docs/supported-domains). Message passing is a three-step process:

- An on-chain component on source domain emits a message.
- Circle's off-chain attestation service signs the message.
- The on-chain component at the destination domain receives the message, and forwards the message body to the specified recipient.
##
Architecture
[](#architecture)
On-chain components on all domains have the same purpose, but implementation differs between EVM-compatible and non-EVM domains.

###
CCTP on EVM Domains
[](#cctp-on-evm-domains)
The relationship between CCTP's on-chain components and Circle's off-chain attestation service is illustrated below for a burn-and-mint of USDC between EVM-compatible domains:


On EVM domains, the on-chain component for cross-chain burning and minting is called TokenMessenger, which is built on top of Message Transmitter, an on-chain component for generalized message passing.

In the diagram above, a token depositor calls the [TokenMessenger#depositForBurn](https://github.com/circlefin/evm-cctp-contracts/blob/adb2a382b09ea574f4d18d8af5b6706e8ed9b8f2/src/TokenMessenger.sol#L169) function to deposit a native token (such as USDC), which delegates to the TokenMinter contract to burn the token. The TokenMessenger contract then sends a message via the [MessageTransmitter#sendMessage](https://github.com/circlefin/evm-cctp-contracts/blob/adb2a382b09ea574f4d18d8af5b6706e8ed9b8f2/src/MessageTransmitter.sol#L108) function. After [sufficient block confirmations](/stablecoins/docs/required-block-confirmations), Circle's off-chain attestation service, Iris, signs the message. An API consumer queries this attestation and submits it on-chain to the destination domain's [MessageTransmitter#receiveMessage](https://github.com/circlefin/evm-cctp-contracts/blob/adb2a382b09ea574f4d18d8af5b6706e8ed9b8f2/src/MessageTransmitter.sol#L250) function. For more details, see [Quickstart: Cross-chain USDC transfer](/stablecoins/docs/quickstart-cross-chain-usdc-transfer).

To send an arbitrary message, directly call [MessageTransmitter#sendMessage](https://github.com/circlefin/evm-cctp-contracts/blob/adb2a382b09ea574f4d18d8af5b6706e8ed9b8f2/src/MessageTransmitter.sol#L108). Note that the message recipient must implement [IMessageHandler#handleReceiveMessage](https://github.com/circlefin/evm-cctp-contracts/blob/master/src/interfaces/IMessageHandler.sol#L31C14-L31C34). It is not currently possible to perform a burn-and-mint of USDC and add arbitrary data to the same message; arbitrary data must be included in a separate message.

###
CCTP on Non-EVM Domains
[](#cctp-on-non-evm-domains)
####
Noble
[](#noble)
Noble is a Cosmos application-specific blockchain (or "appchain") that provides native asset issuance for the Cosmos ecosystem. USDC is natively issued on Noble and can be transferred via the Inter-Blockchain Communication (IBC) protocol to other supported appchains in Cosmos, or via CCTP to any supported domain (e.g. Ethereum).

Note that there are key differences between Cosmos appchains like Noble and EVM-compatible blockchains. Unlike on EVM domains where CCTP is a set of smart contracts, CCTP on Noble is a Cosmos SDK module, which is deployed by Noble governance and built into the Noble blockchain. Cosmos appchains can use IBC to build composable flows with CCTP on Noble.

####
Solana
[](#solana)
Solana is a layer-1 blockchain where USDC is natively issued as an SPL-token. CCTP is deployed to Solana as two Anchor programs: MessageTransmitter and TokenMessengerMinter. Developers can compose programs on top of CCTP programs through CPI's (Cross-Program Invocations). Arbitrary messages can be sent by directly calling MessageTransmitter#send_message just like as described in the EVM section above.

Updated about 1 month ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/usdc-on-test-networks
Title: USDC on Test Networks
Description: undefined
Keywords: undefined

##
What is a test network?
[](#what-is-a-test-network)
[USDC](/stablecoins/docs/what-is-usdc) is a dollar-backed stablecoin that runs on multiple blockchains, including the [Ethereum blockchain](https://en.wikipedia.org/wiki/Ethereum), the [Arbitrum blockchain](https://arbitrum.io/), the [Solana blockchain](https://solana.com/developers), and more.

Each blockchain has various test networks, or testnet, that enable developers to test their applications before deploying code on the main network or mainnet.

##🚧
Testnet Tokens have no Financial Value

Because the testnet networks listed below are used only for testing, the USDC testnet tokens in circulation on these networks are programmatically minted, have no financial value, and are not backed by real US dollars.

Similarly, ETH testnet tokens on Ethereum Sepolia testnet have no financial value.

##
USDC token addresses for blockchain test networks
[](#usdc-token-addresses-for-blockchain-test-networks)
Blockchain	Token Address
Algorand TestNet	[10458941](https://testnet.algoexplorer.io/asset/10458941)
Arbitrum Sepolia	[0x75faf114eafb1BDbe2F0316DF893fd58CE46AA4d](https://sepolia.arbiscan.io/address/0x75faf114eafb1BDbe2F0316DF893fd58CE46AA4d)
Avalanche Fuji	[0x5425890298aed601595a70ab815c96711a31bc65](https://testnet.snowtrace.io/token/0x5425890298aed601595a70ab815c96711a31bc65)
Base Sepolia	[0x036CbD53842c5426634e7929541eC2318f3dCF7e](https://base-sepolia.blockscout.com/address/0x036CbD53842c5426634e7929541eC2318f3dCF7e)
Celo Alfajores	[0x2F25deB3848C207fc8E0c34035B3Ba7fC157602B](https://alfajores.celoscan.io/token/0x2F25deB3848C207fc8E0c34035B3Ba7fC157602B)
Ethereum Sepolia	[0x1c7D4B196Cb0C7B01d743Fbc6116a902379C7238](https://sepolia.etherscan.io/address/0x1c7D4B196Cb0C7B01d743Fbc6116a902379C7238)
Flow Testnet	[A.a983fecbed621163.FiatToken](https://testnet.flowdiver.io/contract/A.a983fecbed621163.FiatToken)
Hedera Testnet	[0.0.429274](https://hashscan.io/testnet/token/0.0.429274)
NEAR Testnet	[3e2210e1184b45b64c8a434c0a7e7b23cc04ea7eb7a6c3c32520d03d4afcb8af](https://testnet.nearblocks.io/token/3e2210e1184b45b64c8a434c0a7e7b23cc04ea7eb7a6c3c32520d03d4afcb8af)
Noble Testnet	[uusdc](https://www.mintscan.io/noble-testnet/assets)
OP Sepolia	[0x5fd84259d66Cd46123540766Be93DFE6D43130D7](https://sepolia-optimism.etherscan.io/address/0x5fd84259d66Cd46123540766Be93DFE6D43130D7)
Polkadot Westmint	[Asset ID 31337](https://westmint.statescan.io/#/assets/31337)
Polygon PoS Amoy	[0x41e94eb019c0762f9bfcf9fb1e58725bfb0e7582](https://www.oklink.com/amoy/address/0x41e94eb019c0762f9bfcf9fb1e58725bfb0e7582)
Solana Devnet	[4zMMC9srt5Ri5X14GAgXhaHii3GnPAEERYPJgZJDncDU](https://explorer.solana.com/address/4zMMC9srt5Ri5X14GAgXhaHii3GnPAEERYPJgZJDncDU?cluster=devnet)
Stellar Testnet	[GBBD47IF6LWK7P7MDEVSCWR7DPUWV3NY3DTQEVFL4NAT4AQH3ZLLFLA5](https://stellar.expert/explorer/testnet/asset/USDC-GBBD47IF6LWK7P7MDEVSCWR7DPUWV3NY3DTQEVFL4NAT4AQH3ZLLFLA5)
zkSync Testnet	[0xAe045DE5638162fa134807Cb558E15A3F5A7F853](https://sepolia.explorer.zksync.io/address/0xAe045DE5638162fa134807Cb558E15A3F5A7F853)
Updated 23 days ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/usdc-ecosystem-catalog
Title: Ecosystem Catalog
Description: undefined
Keywords: undefined

[USDC Ecosystem Catalog:](https://ecosystem.circle.com/) The best place to discover apps built with USDC. Leverage the ecosystem catalog to promote your app, drive developers to extend your app, and inspire your next big idea.

##
Submit your app
[](#submit-your-app)
We need your help! With the rapid growth of the USDC ecosystem, we call upon you and other active members in the community to submit your app to the catalog. Adding your app is as simple as creating a pull request in our GitHub repo [Circle Ecosystem](https://github.com/circlefin/circle-ecosystem).

The data points to bring with you:

- Name
- Company Name
- Description
- Website
- Category
- Logo [optional]
Updated 5 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/stablecoins/docs/eurc-on-main-networks
Title: EURC on Main Networks
Description: undefined
Keywords: undefined

##
What is a Mainnet?
[](#what-is-a-mainnet)
EURC is a euro-backed stablecoin that runs on multiple blockchains.

Each of these blockchains have both main and test networks, or mainnets and testnets. Mainnets are the production blockchains that move and store digital assets, tokens, NFTs, smart contracts, and messages, while testnets are used by developers to test their applications before deploying code on mainnet.

##🚧
Mainnet Tokens have Financial Value

Because the addresses listed below are used to transfer financial value on mainnet blockchains, take precautions before you initiate a transfer. Precautions include testing your code (e.g. using testnet first), verifying addresses, and ensuring your private keys are securely stored and safely used.

##❗️
Do not Test on Mainnet

Because mainnet blockchains move and store tokens that have financial value, do not perform any testing on mainnet. Instead, use a [testnet blockchain](https://developers.circle.com/stablecoins/edit/euroc-on-testing-networks).

##
EURC Mainnet Addresses
[](#eurc-mainnet-addresses)
Blockchain	EURC Mainnet Address
Avalanche C-Chain	[0xc891eb4cbdeff6e073e859e987815ed1505c2acd](https://snowtrace.io/token/0xc891eb4cbdeff6e073e859e987815ed1505c2acd)
Ethereum	[0x1aBaEA1f7C830bD89Acc67eC4af516284b1bC33c](https://etherscan.io/token/0x1aBaEA1f7C830bD89Acc67eC4af516284b1bC33c)
Solana	[HzwqbKZw8HxMN6bF2yFZNrht3c2iXXzpKcFu7uBEDKtr](https://explorer.solana.com/address/HzwqbKZw8HxMN6bF2yFZNrht3c2iXXzpKcFu7uBEDKtr)
Stellar	[EURC-GDHU6WRG4IEQXM5NZ4BMPKOXHW76MZM4Y2IEMFDVXBSDP6SJY4ITNPP2](https://stellar.expert/explorer/public/asset/EURC-GDHU6WRG4IEQXM5NZ4BMPKOXHW76MZM4Y2IEMFDVXBSDP6SJY4ITNPP2)
Updated 5 months ago

