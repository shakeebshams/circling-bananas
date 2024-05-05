
URL: https://developers.circle.com/circle-mint/docs/accounts-api-infrastructure
Title: Accounts API Infrastructure
Description: undefined
Keywords: undefined

This article describes the Accounts APIs, and its structure, which abstracts the complexity inherent to custody and security of digital assets.

##
Master Wallet
[](#master-wallet)
Your [Circle Mint account](https://www.circle.com/en/circle-mint) has a master wallet that is automatically generated for you once you sign up.This master wallet is the default destination for the settlement of fiat and crypto deposits and the default origin for fiat and crypto withdrawals.The Circle Mint account is an omnibus account, hosted by Circle, meaning the balance you see in your account is blockchain-agnostic.

Your master wallet and any sub-accounts you create are buckets of value within Circle’s systems, allowing you to perform off-chain transfers within your overarching account infrastructure.

##
Sub-accounts
[](#sub-accounts)
Sub-accounts are unique and distinct accounts that form the foundation of your individual Accounts API infrastructure. These sub-accounts allow you to segregate and manage funds based on your needs, such as customer funds, internal ledger use, or various other use cases tailored to your business requirements. With sub-accounts, you gain granular control over your funds.

##
Obtaining Your Master Wallet ID
[](#obtaining-your-master-wallet-id)
You can find your master wallet identifier by inspecting the value of property masterWalletId in the response obtained from the [configuration endpoint](https://developers.circle.com/circle-mint/reference/management). Do so by making the following request.

Retrieve Configuration:

Replace ${YOUR\_API\_KEY} with your API key.

cURL

curl -H 'Accept: application/json' \
-H "Authorization: Bearer ${YOUR\_API\_KEY}" \
-X GET --url https\://api-sandbox.circle.com/v1/configuration
Configuration Response

The response from the API call above will look like the following:

JSON

{
  "data": {
    "payments": {
      "masterWalletId": "0000000001"
    }
  }
}
You can store your masterWalletId value, as it will remain consistent for your account. However, if needed, you can always retrieve it again by making a request to the configuration endpoint.

##
Ready for the next step?
[](#ready-for-the-next-step)
Take a look at how to create sub-accounts and make internal transfers with the [Sub-Accounts quickstart guide](/circle-mint/docs/create-a-sub-account)!

Updated 3 months ago


URL: https://developers.circle.com/circle-mint/docs/entity-errors
Title: Entity Errors
Description: undefined
Keywords: undefined

Circle processes API transfer entities asynchronously after you make the initial create request. If a problem occurs while processing the request, Circle sets the errorCode property on the entity. This error can be retrieved either by polling the GET endpoint or from a notification.

##
Transfer Error Codes
[](#transfer-error-codes)
Code	Description
transfer_failed	The transfer failed due to unknown reasons
transfer_denied	The transfer was denied by Circle Risk Service, see transfer riskEvaluation for more details
blockchain_error	There was an error processing the transfer on-chain
insufficient_funds	There was not enough funding to cover the transfer amount
Updated 6 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/crypto-payments-quickstart
Title: Quickstart: Crypto Deposits
Description: undefined
Keywords: undefined

USDC is a “stablecoin,” a cryptocurrency that provides a stable level of purchasing power. As such, it is a popular currency for online transactions, and your customers will want to use it as a payment medium. This guide will walk you through the basics of accepting USDC on your site. Here we use the Ethereum blockchain as an example, but the same steps apply to other currencies and chains as well.

Steps:

- Set up payment intent to pay with Crypto
- Acquire blockchain address customer will pay to
- Customer pays
- Receive payment

#
1. Pay with Crypto
[](#1-pay-with-crypto)
Once the customer reaches checkout and confirms they would like to pay with USDC on Ethereum, your system will send Circle a request to [create a payment intent](/circle-mint/reference/createpaymentintent). Within this payment intent request you will specify the amount, currency, settlement currency, and chain.

Create a payment intent:

cURL
JavaScript

curl --location --request POST 'https://api-sandbox.circle.com/v1/paymentIntents' \
--header 'X-Requested-Id: ${GUID}' \
--header 'Authorization: Bearer ${YOUR_API_KEY}' \
--header 'Content-Type: application/json' \
--data-raw '{
 "idempotencyKey": "17607606-e383-4874-87c3-7e46a5dc03dd",
 "amount": {
   "amount": "1.00",
   "currency": "USD"
 },
 "settlementCurrency": "USD",
 "paymentMethods": [
   {
     "type": "blockchain",
     "chain": "ETH"
   }
 ]
}'
Response

{
 "data": {
   "id": "6e4d4047-db14-4c09-b238-1215aee50d03",
   "amount": {
     "amount": "1.00",
     "currency": "USD"
   },
   "amountPaid": {
     "amount": "0.00",
     "currency": "USD"
   },
   "amountRefunded": {
     "amount": "0.00",
     "currency": "USD"
   },
   "settlementCurrency": "USD",
   "paymentMethods": [
     {
       "type": "blockchain",
       "chain": "ETH"
     }
   ],
   "paymentIds": [],
   "timeline": [
     {
       "status": "created",
       "time": "2022-07-21T20:13:35.579331Z"
     }
   ],
   "createDate": "2022-07-21T20:13:35.578678Z",
   "updateDate": "2022-07-21T20:19:24.859052Z",
 }
}
Webhook notification

{
 "clientId": "f1397191-56e6-42fd-be86-0a7b9bd91522",
 "notificationType": "paymentIntents",
 "version": 1,
 "customAttributes": { "clientId": "f1397191-56e6-42fd-be86-0a7b9bd91522" },
 "paymentIntent": {
   "id": "6e4d4047-db14-4c09-b238-1215aee50d03",
   "amount": { "amount": "1.00", "currency": "USD" },
   "amountPaid": { "amount": "0.00", "currency": "USD" },
   "amountRefunded": { "amount": "0.00", "currency": "USD" },
   "settlementCurrency": "USD",
   "paymentMethods": [{ "type": "blockchain", "chain": "ETH" }],
   "paymentIds": [],
   "refundIds": [],
   "timeline": [
     { "status": "created", "time": "2022-07-21T20:13:35.579331Z" }
   ],
   "createDate": "2022-07-21T20:13:35.578678Z",
   "updateDate": "2022-07-21T20:13:35.578678Z"
 }
}
If you wish, you can create “long-lived” payment intent at a sub-wallet level (e.g., one deposit address and sub-wallet per sender). Start by creating a deposit wallet using the [Create a Wallet](https://developers.circle.com/developer/reference/createwallet) endpoint, then, send a request to create a continuous payment intent. Within this payment intent request specify the type: "continuous", currency, settlementCurrency, chain, and merchantWalletId (optional) to receive funds into the sub-wallet instead of a master wallet.

#
2. Acquire blockchain address customer will pay to
[](#2-acquire-blockchain-address-customer-will-pay-to)
For security reasons, we do not synchronously return the deposit blockchain address. To retrieve the blockchain deposit address, you have two options:

- Subscribe to webhook notifications
- Poll Circle APIs
##
Option 1: Webhook Notification
[](#option-1-webhook-notification)
To receive webhook notifications, visit the [notification quickstart](/circle-mint/docs/circle-apis-notifications-quickstart). and follow the steps shown. After you subscribe to notifications, you will receive updates for the payment intent whenever the resource has been updated. In this case, when the paymentMethods.address has been set, a notification will be sent with a new timeline object with astatus of pending.

Payment intent webhook notification

{
 "clientId": "f1397191-56e6-42fd-be86-0a7b9bd91522",
 "notificationType": "paymentIntents",
 "version": 1,
 "customAttributes": { "clientId": "f1397191-56e6-42fd-be86-0a7b9bd91522" },
 "paymentIntent": {
   "id": "6e4d4047-db14-4c09-b238-1215aee50d03",
   "amount": { "amount": "1.00", "currency": "USD" },
   "amountPaid": { "amount": "0.00", "currency": "USD" },
   "amountRefunded": { "amount": "0.00", "currency": "USD" },
   "settlementCurrency": "USD",
   "paymentMethods": [
     {
       "type": "blockchain",
       "chain": "ETH",
       "address": "0x97de855690955e0da79ce5c1b6804847e7070c7f"
     }
   ],
   "fees": [
     { "type": "blockchainLeaseFee", "amount": "0.00", "currency": "USD" }
   ],
   "paymentIds": [],
   "refundIds": [],
   "timeline": [
     { "status": "pending", "time": "2022-07-21T20:13:38.188286Z" },
     { "status": "created", "time": "2022-07-21T20:13:35.579331Z" }
   ],
   "createDate": "2022-07-21T20:13:35.578678Z",
   "updateDate": "2022-07-21T20:13:38.186831Z",
   "expiresOn": "2022-07-21T21:13:38.087275Z"
 }
}
##
Option 2: Poll Payment Intent Endpoint
[](#option-2-poll-payment-intent-endpoint)
If you prefer to poll [get a payment intent](/circle-mint/reference/getpaymentintent), you can send get request until you receivepaymentMethods.address.

Retrieve payment intent:

cURL
JavaScript

curl --location --request GET 'https://api-sandbox.circle.com/v1/paymentIntents/{id}' \
--header 'X-Requested-Id: ${GUID}' \
--header 'Authorization: Bearer ${YOUR_API_KEY}'
Response

{
 "data": {
   "id": "6e4d4047-db14-4c09-b238-1215aee50d03",
   "amount": {
     "amount": "1.00",
     "currency": "USD"
   },
   "amountPaid": {
     "amount": "0.00",
     "currency": "USD"
   },
   "amountRefunded": {
     "amount": "0.00",
     "currency": "USD"
   },
   "settlementCurrency": "USD",
   "paymentMethods": [
     {
       "type": "blockchain",
       "chain": "ETH",
       "address": "0x97de855690955e0da79ce5c1b6804847e7070c7f"
     }
   ],
   "fees": [
     { "type": "blockchainLeaseFee", "amount": "0.00", "currency": "USD" }
   ],
   "paymentIds": [],
   "refundIds": [],
   "timeline": [
     { "status": "pending", "time": "2022-07-21T20:13:38.188286Z" },
     { "status": "created", "time": "2022-07-21T20:13:35.579331Z" }
   ],
   "createDate": "2022-07-21T20:13:35.578678Z",
   "updateDate": "2022-07-21T20:13:38.186831Z",
   "expiresOn": "2022-07-21T21:13:38.087275Z"
 }
}
#
3. Enable customer payment
[](#3-enable-customer-payment)
Once you receive the deposit address via paymentMethods.address, you’ll provide it to the customer along with the amount and currency to be paid. You can present the address two ways:

- as plain text the customer can cut and paste; or
- as a QR code the customer can scan via an app.
The customer then sends payment from their wallet (custodial or non-custodial).

##📘
You can specify the timeframe in which the customer must send payment by adjusting the exipiresOn setting.

#
4. Receive payment from Circle
[](#4-receive-payment-from-circle)
Once Circle obtains payment on-chain, we create a Payment resource linked to the Payment Intent created earlier and update the status of that Payment Intent. Your firm will then receive payment via the method specified. See the State Management Timeline for more details.

##
Option 1: Webhook Notifications
[](#option-1-webhook-notifications)
Payment intent webhook notification

{
 "clientId": "f1397191-56e6-42fd-be86-0a7b9bd91522",
 "notificationType": "paymentIntents",
 "version": 1,
 "customAttributes": { "clientId": "f1397191-56e6-42fd-be86-0a7b9bd91522" },
 "paymentIntent": {
   "id": "6e4d4047-db14-4c09-b238-1215aee50d03",
   "amount": { "amount": "1.00", "currency": "USD" },
   "amountPaid": { "amount": "1.00", "currency": "USD" },
   "amountRefunded": { "amount": "0.00", "currency": "USD" },
   "settlementCurrency": "USD",
   "paymentMethods": [
     {
       "type": "blockchain",
       "chain": "ETH",
       "address": "0x97de855690955e0da79ce5c1b6804847e7070c7f"
     }
   ],
   "fees": [
     { "type": "blockchainLeaseFee", "amount": "0.00", "currency": "USD" },
     { "type": "totalPaymentFees", "amount": "0.01", "currency": "USD" }
   ],
   "paymentIds": ["66c56b6a-fc79-338b-8b94-aacc4f0f18de"],
   "refundIds": [],
   "timeline": [
     {
       "status": "complete",
       "context": "paid",
       "time": "2022-07-21T20:19:24.861094Z"
     },
     { "status": "pending", "time": "2022-07-21T20:13:38.188286Z" },
     { "status": "created", "time": "2022-07-21T20:13:35.579331Z" }
   ],
   "createDate": "2022-07-21T20:13:35.578678Z",
   "updateDate": "2022-07-21T20:19:24.859052Z",
   "expiresOn": "2022-07-21T21:13:38.087275Z"
 }
}
Payment webhook notification

{
 "clientId": "f1397191-56e6-42fd-be86-0a7b9bd91522",
 "notificationType": "payments",
 "version": 1,
 "customAttributes": { "clientId": "f1397191-56e6-42fd-be86-0a7b9bd91522" },
 "payment": {
   "id": "66c56b6a-fc79-338b-8b94-aacc4f0f18de",
   "type": "payment",
   "status": "paid",
   "amount": { "amount": "1.00", "currency": "USD" },
   "fees": { "amount": "0.01", "currency": "USD" },
   "createDate": "2022-07-21T20:16:35.092Z",
   "updateDate": "2022-07-21T20:19:24.719Z",
   "merchantId": "f1397191-56e6-42fd-be86-0a7b9bd91522",
   "merchantWalletId": "1000999922",
   "paymentIntentId": "6e4d4047-db14-4c09-b238-1215aee50d03",
   "settlementAmount": { "amount": "1.00", "currency": "USD" },
   "fromAddresses": {
    "chain": "ETH",
    "addresses" : ["0x0d4344cff68f72a5b9abded37ca5862941a62050"]
   },
   "depositAddress": {
     "chain": "ETH",
     "address": "0x97de855690955e0da79ce5c1b6804847e7070c7f"
   },
   "transactionHash": "0x7351585460bd657f320b9afa02a52c26d89272d0d10cc29913eb8b28e64fd906"
 }
}
##
Option 2: Retrieve Payment Intent and Payment
[](#option-2-retrieve-payment-intent-and-payment)
Retrieve a payment intent:

cURL
JavaScript

curl --location --request GET 'https://api-sandbox.circle.com/v1/paymentIntents/{id}' \
--header 'X-Requested-Id: ${GUID}' \
--header 'Authorization: Bearer ${YOUR_API_KEY}'
Response

{
 "data": {
   "id": "6e4d4047-db14-4c09-b238-1215aee50d03",
   "amount": {
     "amount": "1.00",
     "currency": "USD"
   },
   "amountPaid": {
     "amount": "1.00",
     "currency": "USD"
   },
   "amountRefunded": {
     "amount": "0.00",
     "currency": "USD"
   },
   "settlementCurrency": "USD",
   "paymentMethods": [
     {
       "type": "blockchain",
       "chain": "ETH",
       "address": "0x97de855690955e0da79ce5c1b6804847e7070c7f"
     }
   ],
   "fees": [
     {
       "type": "blockchainLeaseFee",
       "amount": "0.00",
       "currency": "USD"
     },
     {
       "type": "totalPaymentFees",
       "amount": "0.01",
       "currency": "USD"
     }
   ],
   "paymentIds": ["66c56b6a-fc79-338b-8b94-aacc4f0f18de"],
   "refundIds": [],
   "timeline": [
     {
       "status": "complete",
       "context": "paid",
       "time": "2022-07-21T20:19:24.861094Z"
     },
     {
       "status": "pending",
       "time": "2022-07-21T20:13:38.188286Z"
     },
     {
       "status": "created",
       "time": "2022-07-21T20:13:35.579331Z"
     }
   ],
   "createDate": "2022-07-21T20:13:35.578678Z",
   "updateDate": "2022-07-21T20:19:24.859052Z",
   "expiresOn": "2022-07-21T21:13:38.087275Z"
 }
}
Retrieve a payment:

cURL
JavaScript

curl --location --request GET 'https://api-sandbox.circle.com/v1/payments/{id}' \
--header 'X-Requested-Id: ${GUID}' \
--header 'Authorization: Bearer ${YOUR_API_KEY}'
Response

{
   "data": {
     "id": "66c56b6a-fc79-338b-8b94-aacc4f0f18de",
     "type": "payment",
     "status": "paid",
     "amount": {
       "amount": "1.00",
       "currency": "USD"
     },
     "fees": {
       "amount": "0.01",
       "currency": "USD"
     },
     "createDate": "2022-07-21T20:16:35.092852Z",
     "updateDate": "2022-07-21T20:19:24.719313Z",
     "merchantId": "f1397191-56e6-42fd-be86-0a7b9bd91522",
     "merchantWalletId": "1000999922",
     "paymentIntentId": "6e4d4047-db14-4c09-b238-1215aee50d03",
     "settlementAmount": {
       "amount": "1.00",
       "currency": "USD"
     },
     "fromAddresses": {
       "chain": "ETH",
       "addresses" : ["0x0d4344cff68f72a5b9abded37ca5862941a62050"]
     },
     "depositAddress": {
       "chain": "ETH",
       "address": "0x97de855690955e0da79ce5c1b6804847e7070c7f"
     },
     "transactionHash": "0x7351585460bd657f320b9afa02a52c26d89272d0d10cc29913eb8b28e64fd906"
   }
 }
##📘
For blockchains that require a 'memo' or 'address tag' (XLM, HBAR, etc.), the optional addressTag field will be present in the depositAddress object.

#
Gas Abstracted Payment: Network Fee Flexibility
[](#gas-abstracted-payment-network-fee-flexibility)
In a regular payment, the customer pays for the gas fees (e.g., blockchain transaction fees). In a pull-based payment, the gas fees can be paid by you or by the customer. In the latter case, customers can pay via USDC on an EVM-compatible chain such as Ethereum rather than in a native token. For example, while making a USDC payment on Ethereum, the customer can pay for the gas in USDC rather than ETH. By doing so, the customer does not need native tokens and can solely rely on USDC for both gas and the actual payment. Gas fees and the preferences can be configured by working with Circle’s customer success team.

In the case where a customer is paying for gas, Circle makes a real-time gas fee estimate and adds that in the total amount of payment that the customer authorizes. There are four steps in the flow:

- Set up payment intent and acquire blockchain address for deposits
- Receive a typed message for customers to sign and authorize the payment
- Circle will receive the signature and broadcast the transaction for payment
- Receive payment


Refer to the Pay with Crypto section to set up payment intent and retrieve a blockchain address. Once that has been completed, follow the steps below for gas decoupled payments.

##
1. Get a Typed Message for Signing
[](#1-get-a-typed-message-for-signing)
Call the GET: /payment/presign endpoint to fetch the typed message. The two required parameters are paymentIntentId , which can be retrieved from the GET: /paymentIntent response or webhook notification; and endUserAddress which is the consumer wallet address.

cURL

curl --location 'https://api-sandbox.circle.com/v1/payments/presign?paymentIntentId=6e4d4047-db14-4c09-b238-1215aee50d03&endUserAddress=0x8381470ED67C3802402dbbFa0058E8871F017A6F' \
--header 'X-Requested-Id: ${GUID}' \
--header 'Authorization: Bearer ${YOUR_API_KEY}'
Response

{
  "data": {
    "typedData": {
      "domain": {
        "name": "USD Coin",
        "verifyingContract": "0x07865c6e87b9f70255377e024ace6630c1eaa37f",
        "version": "2",
        "chainId": 5
      },
      "message": {
        "from": "0x8381470ED67C3802402dbbFa0058E8871F017A6F",
        "to": "0xdB055877e6c13b6A6B25aBcAA29B393777dD0a73",
        "value": "5003456",
        "validAfter": 1675317371,
        "validBefore": 1675107967,
        "nonce": "0x4f716a578b96592599c3ca7b2a46453df87511d4809f608cec982d6e83632604"
      },
      "totalAmount": {
        "amount": "3.14",
        "currency": "USD"
      },
      "feeChargeModel": "endUser",
      "networkFeeQuote": {
        "quoteId": "5d0bb4e7-bd83-44dd-b8f5-53ddd5f35a3a",
        "amount": {
          "amount": "3.14",
          "currency": "USD"
        },
        "expiresAt": "2023-02-02T06:01:21.152737680Z"
      },
      "types": {
        "EIP712Domain": [
          {
            "name": "name",
            "type": "string"
          },
          {
            "name": "version",
            "type": "string"
          },
          {
            "name": "chainId",
            "type": "uint256"
          },
          {
            "name": "verifyingContract",
            "type": "address"
          }
        ],
        "TransferWithAuthorization": [
          {
            "name": "from",
            "type": "address"
          },
          {
            "name": "to",
            "type": "address"
          },
          {
            "name": "validAfter",
            "type": "uint256"
          },
          {
            "name": "validBefore",
            "type": "uint256"
          },
          {
            "name": "nonce",
            "type": "byte32"
          }
        ]
      },
      "primaryType": "TransferWithAuthorization"
    }
  }
}
##
2. Customers Sign the Transaction From Their Wallet
[](#2-customers-sign-the-transaction-from-their-wallet)
You will need to trigger the customer’s wallet using the response from /presign.

For example, if you use the [signTypedData function from wagmi](https://wagmi.sh/core/actions/signTypedData), you will need to fill out the domain, types and values fields.



Once the customer signs into the wallet, you will receive a string of raw signatures.

##
3. Create a Crypto Payment with Customers’ Signature
[](#3-create-a-crypto-payment-with-customers-signature)
With a customer’s raw signature, Circle can create a crypto payment with the POST /payment/crypto endpoint. After the payment has been created, Circle will be able to track the payment status following the instructions [here](#4-payment-received).

cURL

curl --location 'https://api-sandbox.circle.com/v1/payments/crypto' \
--header 'X-Requested-Id: ${GUID}' \
--header 'Authorization: Bearer ${YOUR_API_KEY}' \
--header 'Content-Type: application/json' \
--data '{
    "idempotencyKey": "ba943ff1-ca16-49b2-ba55-1057e70ca5c7",
    "paymentIntentId": "4e9fa5b1-3964-4f02-a7ba-811cc5d94be1",
    "protocolMetadata": {
        "type": "TransferWithAuthorization",
        "metaTxNonce": "0xfdb476566b75311fdd14444e6a77630c36e653a3e255adcaa7c34f3babc1de76",
        "signatureValidAfter": "1675104393",
        "signatureValidBefore": "1675107967",
        "rawSignature": "0xcff7ef7a24b88d83fa3d6e81b41c9cef19cc0119c085a6ef98cb1b6bc9436a9f18dcb2d46b9cb4d31a7031466b450bbe1e6c0230c5503c7a68e04055b4be0cbc1b"
    },
    "amount": {
        "amount": "3.14",
        "currency": "USD"
    },
    "source": {
        "address": "0xdB055877e6c13b6A6B25aBcAA29B393777dD0a73",
        "type": "blockchain"
    },
    "destination": {
        "address": "0xda1ab716f7f7b3cb036a7fd74e5ca852126834c1",
        "chain": "ETH"
    },
    "quoteId": "c6ac001e-9812-4bc1-8dc3-1549b5adaa23"
}'
Response

{
  "data": {
    "id":"27827c45-6a3e-4012-8eaa-f6d712f70c2e",
    "type":"payment",
    "status":"pending",
    "amount":{
      "amount":"10.00",
      "currency":"USD"
    }
    "createDate":"2023-02-14T01:21:07.116369Z",
    "updateDate":"2023-02-14T01:21:07.116371Z",
    "merchantId":"a49f9b1d-75e0-44a9-b8d2-4293b3f11ebd",
    "merchantWalletId":"1000563095",
    "paymentIntentId":"dfcf96ab-dd3f-417e-b009-ef899ee4e8da",
    "fromAddresses":{
      "chain":"ETH",
      "addresses":[
        "0xbdE3d06b700A5f622767c85e79e42014C5376B6B"
      ]
    }
    "depositAddress":{
      "chain":"ETH",
      "address":"0x5f1090b5db3e00e13e94b93055b3d1c4254e1720"
    }
  }
}

##
4. Payment Received
[](#4-payment-received)
Refer to the [payments received](#4-payment-received) section for more details.

Updated about 1 month ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/circle-apis-api-errors
Title: Circle APIs: API and Entity Errors
Description: undefined
Keywords: undefined

The error codes used by Circle APIs fall into two categories, API response errors and entity errors. API response errors are returned immediately in response to API requests. Entity errors are associated with API entities such as payments and generally take seconds or minutes to return.

#
API Error Responses
[](#api-error-responses)
When an API request is made and the error is known immediately, Circle APIs will return the appropriate HTTP status code along with a JSON error response. Because HTTP status codes do not always provide sufficient information about the cause of an error, Circle’s API response errors provide more detailed JSON responses to help you determine what’s gone wrong.

In the example below, the request is missing a required field. The HTTP Status code in this case would be HTTP/1.1 400 Bad Request

JSON

{"code":2,"message":"Invalid entity.
metadata.email may not be empty (was null)","errors":[{"error":"invalid_value","location":"metadata.email","message":"metadata.email may not be empty (was null)"}]}
##
API Error Types
[](#api-error-types)
You can automate the handling of certain error types. These include the following error names, which include example messages and constraints, where applicable:

error	message	constraints
value_must_be_true	field1 must be true (was false)	NA
value_must_be_false	field1 must be false (was true)	NA
required	field1 may not be null (was null)	NA
not_required	field1 must be null (was foo)	NA
min_value	field1 must be greater than or equal to 2 (was 0)	"constraints": { "min": 2 }
min_value	field1 must be greater than 2.0 (was 2.0)	"constraints": { "min": "2.0", "inclusive": false }
max_value	field1 must be less than or equal to 99 (was 1024)	"constraints": { "max": 99 }
max_value	field1 be less than or equal to 99.99 (was 100)	"constraints": { "max": "99.99", "inclusive": true }
length_outside_bounds	field1 must be between 2 and 5 (was qwerty)	"constraints": { "min": 2, "max": 5 }
pattern_mismatch	field1 must match "[a-z]+" (was 123456)	"constraints": { "pattern": "[a-z]+" }
date_not_in_past	field1 must be in the past (was 2020-08-31T18:18:54.211Z)	NA
date_not_in_future	field1 must be in the future (was 2020-08-31T18:18:17.621Z)	NA
number_format	field1 numeric value out of bounds (<3 digits>.<2 digits> expected) (was 123.456)	"constraints": { "max-integral-digits": 3, "max-fractional-digits": 2 }
##
API Error Format
[](#api-error-format)
Whenever an API request results in an error, the JSON response will contain both a high-level error code and a human-readable message within the body.

JSON

HTTP/1.1 400 Bad Request
Content-Type: application/json

{
 "code": 2,
 "message": "Invalid entity.\n[<error-message1>...\n<error-messageN>]"
}
###
Extended error format
[](#extended-error-format)
In some cases, you’ll receive extended information about why a request has failed.

Example: If you fail to supply a value for a required field, you’ll receive the following error response:

JSON

HTTP/1.1 400 Bad Request
Content-Type: application/json

{
 "code": 2,
 "message": "Invalid entity.\nfield1 may not be null (was null)",
 "errors": [
   {
     "error": "required",
     "message": "field1 may not be null (was null)",
     "location": "field1",
     "invalidValue": "null",
     "constraints": {} # see below for details
   }
 ]
}
This extended error response contains one or more associated error descriptions. Error description attributes can be read as follows:

key	meaning	optional
error	type of an error	false
message	human-friendly message	false
location	period-separated path to the property that causes this error. An example could be field1 or address.billingCountry	false
invalidValue	actual value of the property specified in location key, as received by the server	true
constraints	special object that contains additional details about the error and could be used for programmatic handling on the client side	true
##
List of API Error Codes
[](#list-of-api-error-codes)
The table below shows the list of JSON error codes that may be returned in an API error response.

Code	Message	Description
-1	Unknown Error	An unknown error occurred processing the API request
1	Malformed authorization. Is the authorization type missing?	API Key is missing or malformed
2	Invalid Entity	Error with the JSON object passed in the request body
3	Forbidden	API Key used with request does not have one of the roles authorized to call the API endpoint
1032	Account number is invalid or missing	An invalid number was provided when creating a card
1051	Payment not found	The payment id requested was not found
1068	Merchant account not associated with a marketplace	Attempting to create a marketplace payment with a merchant that is not associated with the marketplace
1069	A wallet account could not be found	Unable to find the wallet account referenced in a marketplace create payment request
1070	Marketplace info is required to create marketplace payment	Marketplace info was not provided with a marketplace create payment request
1076	Payment amount did not fall within merchant charge limits	The payment amount was greater than the maximum allowed limit or below the minimum allowed limit
1077	Payment amount is invalid	Payment amount must be greater than zero
1078	Payment currency not supported	An invalid currency value was used when making a payment
1083	Idempotency key already bound to other request	The idempotency key used when making a request was used with another payment. Please retry with a different value
1084	This item cannot be canceled	A cancel or refund request cannot be canceled
1085	This item cannot be refunded	A cancel or refund request cannot be refunded
1086	This payment was already canceled	This payment was already canceled
1087	Total amount to be refunded exceeds payment amount	Total amount to be refunded exceeds payment amount
1088	Invalid source account	An invalid source account was specified in a payout or transfer request
1089	The source account could not be found	Unable to find the source account specified in a payout or transfer request
1091	Invalid wire routing number	Could not find a bank with that routing number when creating wire bank account
1092	Invalid IBAN	Invalid IBAN used in request to create a wire bank account
1093	Source account has insufficient funds	The source account has insufficient funds for the payout or transfer amount
1094	The billing last name cannot be empty	When creating a card or bank account the billing last name must be provided
1096	Encryption key id could not be found	An encryption key id must be provided if request includes encrypted data
1097	Original payment is failed	Attempting to cancel or refund a failed payment
1098	Wire payment amount failed	Wire payment must be greater than the minimum amount
1099	The merchantWalletId query parameter is missing	The merchantWalletId query parameter must be set in association with the walletId parameter to retrieve wallet settlements
1100	Invalid fiat account type	The fiat account is invalid for the attempted payout type (e.g. using a business account for a marketplace payout)
1101	Invalid country format	An invalid ISO 31660-2 country code was provided
1102	IBAN country mismatch	IBAN does not match the provided bank country code
1103	IBAN required	IBAN is required is this request
1104	Additional bank details required	There are additional bank details required for this request; details of which are provided in the response
1105	Additional billing details required	There are additional billing details required for this request; details of which are provided in the response
1106	Invalid district format	Invalid district format, must be a 2 character value
1107	Payout limit exceeded	Payout can't be accepted as it would exceed the given limit
1108	Unsupported Country	Country not supported for customer
1109	Invalid bin range	No bin range found for the card
1110	Invalid card number	Card number is invalid
1111	Invalid issuer country	Issuer country is invalid
1143	Checkout session not found	The checkout session id passed in the request doesn't exist in the DB
1144	Checkout session is already in a completed state	The checkout session cannot be extended because it is already in a complete state
2003	The recipient address already exists	The blockchain address has already been associated with the account
2004	The address is not a verified withdrawal address	The blockchain address must first be verified before it can be used as a destination in a transfer request
2005	The address belongs to an unsupported blockchain	The blockchain type used as a transfer destination is not supported
2006	Wallet type is not supported	The wallet type specified when creating an end user wallet is not supported
2007	Unsupported transfer	A transfer from the provided source to the provided destination is not supported
5000	Invalid travel rule identity type	The provided identity type must be either "individual" or "business"
5001	Payout does not found	Payout doesn’t exist based on the ID provided. Please check the payout id
5002	Invalid payout amount	Payout amount must be more than 0
5003	Inactive destination address	Cannot send payout to an inactive destination address. If you have just added the address, you may have to wait for 24 hours before use
5004	Destination address not found	The destination address for this payout could not be found
5005	Source wallet not found	Source wallet for this payout could not be found
5006	Insufficient funds	The source wallet has insufficient funds for this payout
5007	Unsupported currency	currency not currently supported for this operation
5011	Invalid destination address	cannot send payout to an invalid destination address
5012	Invalid destination location types	cannot search for both crypto and fiat payouts
5013	Invalid source wallet id	source wallet id must be a number for payouts search
5014	The address is not valid for the blockchain	Provided blockchain address is not valid for the corresponding blockchain
5015	Invalid destination chain	Provided blockchain address has an invalid chain in respect to the currency used
#
Entity Error Responses
[](#entity-error-responses)
With entities such as payments, cards, bank wires, and transfers, an error is generally not known immediately, so no error code can be returned at the time of the request.

For instance, when making a payment, the transaction is processed asynchronously and the create payment request will have a status of pending. After processing, the status will eventually be set to approved or failed.

API entities such as payments and cards are processed asynchronously after the initial create request is made. If a problem occurs while processing the request (the status is shown as failed), the errorCode property is set on the entity and can be retrieved either by polling the GET endpoint or via a notification. The response explains why the payment was unsuccessful.

Here are some Entity error codes associated with payments, cards, bank wires, and transfers.

##
Payment Error Codes
[](#payment-error-codes)
Code	Description
payment_failed	Payment failed due to an unknown reason
payment_fraud_detected	Suspected fraud detected by Issuing bank. Please instruct your user to contact their bank directly to resolve
payment_denied	Payment denied by Circle Risk Service, see payment riskReasonCode for more details
payment_not_supported_by_issuer	Issuer bank was unable to process the transaction
payment_not_funded	There were insufficient funds to cover the payment amount
payment_stopped_by_issuer	A stop has been placed by the issuer or customer
payment_canceled	Payment was canceled
payment_failed_balance_check	Payment failed the Plaid balance check due to insufficient funds
payment_unprocessable	The provided encryptedData could not be processed
card_failed	The payment failed due to a problem with the card such as an incorrect card number
card_invalid	The card number was invalid
card_cvv_invalid	Incorrect CVV value provided
card_expired	Card expired
card_limit_violated	The amount or frequency of payments exceeded card limits
card_not_honored	The issuing bank did not approve the payment
card_account_ineligible	The card is not linked to an eligible bank account
card_restricted	Transaction not permitted to cardholder. Issuer has declined the transaction because the card cannot be used for this type of transaction. Please instruct your user to use a different payment method
unauthorized_transaction	The user has advised the bank that the payment was not authorized
bank_account_ineligible	The account is not eligible and no other default account was found
bank_transaction_error	The bank reported an error processing the transaction
invalid_account_number	The account number is invalid or missing
invalid_wire_rtn	The wire routing number is invalid
ref_id_invalid	Push payment reference Id not recognized
account_name_mismatch	Account name does not match Circle resource account name
account_number_mismatch	Push payment account number does not match Circle resource account number
account_ineligible	Ineligible fiat account due to invalid type or state
customer_name_mismatch	Customer full name doesn't match sender name
institution_name_mismatch	Institution name doesn't match sender name
##
Card Verification Error Codes
[](#card-verification-error-codes)
Code	Description
verification_failed	Verification failed due to an unknown reason
verification_fraud_detected	Card suspected to be used for fraud
risk_denied	Card denied by Circle Risk Service, see riskEvaluation for more details
verification_not_supported_by_issuer	Issuer bank was unable to process the transaction
verification_stopped_by_issuer	A stop has been placed on the card
card_failed	Verification failed due to a problem with the card such as the card number does not exist
card_invalid	The card number was invalid
card_address_mismatch	The billing address provided in the card creation request did not match the one recorded by the issuer bank
card_zip_mismatch	The postal code provided in the card creation request did not match the one recorded by the issuer bank
card_cvv_invalid	Incorrect CVV value provided
card_expired	Card expired
card_not_honored	The issuing bank did not authorize the card
card_account_ineligible	The card is not linked to an eligible bank account
card_limit_violated	The amount or frequency of payments exceeded card limits
card_cvv_required	The cvv is either incorrect or missing
three_d_secure_not_supported	3DS transactions are not supported by the issuing bank
three_d_secure_required	3DS is required by the issuing bank
three_d_secure_failure	The customer failed the 3DS authentication step
three_d_secure_action_expired	The customer took too long to finish the 3DS process. This typically expires after 15 minutes
three_d_secure_invalid_request	The 3DS request was submitted with invalid parameters
##
Payout Error Codes
[](#payout-error-codes)
Code	Description
insufficient_funds	Exchange insufficient funds
transaction_denied	The transaction was denied as the fiat account is not verified
transaction_failed	Transaction failed due to an unknown reason
transaction_returned	The transaction was returned
bank_transaction_error	The bank reported an error processing the transaction
fiat_account_limit_exceeded	The Fiat account limit exceeded
invalid_bank_account_number	The bank account number is invalid or missing
invalid_wire_rtn	The wire routing number is invalid
sen_not_supported	account_number_to must be a valid SEN account
##
Transfer Error Codes
[](#transfer-error-codes)
Code	Description
transfer_failed	The transfer failed due to unknown reasons
transfer_denied	The transfer was denied by Circle Risk Service, see transfer riskEvaluation for more details
blockchain_error	There was an error processing the transfer on-chain
insufficient_funds	There was not enough funding to cover the transfer amount
Updated 4 months ago


URL: https://developers.circle.com/circle-mint/docs/a-note-on-idempotent-requests
Title: Idempotent Requests
Description: undefined
Keywords: undefined

Circle APIs support [idempotent requests](https://en.wikipedia.org/wiki/Idempotence), which are a type of request method that executes only once no matter how many times the request is sent. Result: you can safely retry API calls when things go wrong during a transaction (such as a network connection issue) before you receive a response.

To ensure success, please note the following:

- To be sure a request is idempotent, you must generate and provide an idempotency key formatted as a GUID.
- Subsequent requests with the same idempotencyKey parameter value will produce the same result as the initial request.
- The idempotencyKey body parameter must be sent in [UUID format](https://en.wikipedia.org/wiki/Universally_unique_identifier).
#
Operations that support idempotency
[](#operations-that-support-idempotency)
The following Circle API operations support idempotent requests. Check the API reference for more details.

-
Core Functionality

- [Create a payout.](/circle-mint/reference/createbusinesspayout)
- [Create a wire bank account.](/circle-mint/reference/createbusinesswireaccount)
- [Create a business transfer.](/circle-mint/reference/createbusinesstransfer)
- [Create a deposit address.](/circle-mint/reference/createbusinessdepositaddress)
- [Create a recipient address.](/circle-mint/reference/createbusinessrecipientaddress)
-
Payments API

- [Create a payment intent.](/circle-mint/reference/createpaymentintent)
- [Expire a payment intent.](https://developers.circle.com/developer/reference/expirepaymentintent)
- [Refund a payment intent.](https://developers.circle.com/developer/reference/refundpaymentintent)
-
Payouts API

- [Create a payout.](/circle-mint/reference/createpayout)
- [Create a recipient address.](/circle-mint/reference/createaddressbookrecipient)
-
Accounts API

- [Create a sub-account.](/circle-mint/reference/createwallet)
- [Create a blockchain address.](/circle-mint/reference/generateaddress)
- [Create a transfer.](/circle-mint/reference/createtransfer)
Updated 5 months ago


URL: https://developers.circle.com/circle-mint/docs/accept-card-payments-online
Title: Accept Card Payments
Description: undefined
Keywords: undefined

See below for a sample end-to-end flow covering the steps involved in accepting a card payment using the Circle Payments API.



#
Prepare Encryption of Sensitive Data
[](#prepare-encryption-of-sensitive-data)
Card details are encrypted in the browser so sensitive card details are never accessible to you server.

The Circle Payments API exposes a [public key endpoint](/circle-mint/reference/getpublickey) that is used to encrypt data on the client-side.

On page load you will load the [OpenPgP.js](https://github.com/openpgpjs/openpgpjs) JavaScript library used for encryption.

The request to [get your public key](/circle-mint/reference/getpublickey) to be used for encryption can also be done on page load so that it is already available when the user wants to make a payment. Your public keys change infrequently, so we further recommend that you cache their values for a duration of 24 hours or more.

#
Encrypt End-user's Card Details
[](#encrypt-end-users-card-details)
Before card details are saved using the API, the sensitive data elements first need to be encrypted on the client-side. Below is a sample code that describes the data-types and functions involved in performing the encryption of card details.

Encrypting card details

import { createMessage, encrypt, readKey } from 'openpgp'

// Object to be encrypted
interface CardDetails {
 number?: string,    // required when storing card details
 cvv?: string        // required when cardVerification is set to cvv
}

// Encrypted result
interface EncryptedValue {
 encryptedData: string,
 keyId: string
}
 
const pciEncryptionKey = await getPCIPublicKey()
 
/**
* Encrypt card data function
*/
return async function(dataToEncrypt: CardDetails): Promise<EncryptedValue> {
 const decodedPublicKey = await readKey({ armoredKey: atob(publicKey) })
  const message = await createMessage({ text: JSON.stringify(dataToEncrypt) })
  return encrypt({
    message,
    encryptionKeys: decodedPublicKey,
  }).then((ciphertext) => {
    return {
      encryptedMessage: btoa(ciphertext),
      keyId,
    }
  })
}
The returned encryptedData and keyId values will then be included in the request made to [save card details](/circle-mint/reference/createcard).

#
Save End-user's Card Details
[](#save-end-users-card-details)
The next step is to save the end-user's card details by using the [create card endpoint](/circle-mint/reference/createcard).

As well as passing the encrypted card details some extra fields need to be added to the request before it can be sent to the [create card endpoint](/circle-mint/reference/createcard). You need to collect billing details for your end user, as well as provide a unique ID for the active session (sessionId) and the IP address of the end-user (ipAddress).

Creating a card will respond with an id value that can be stored on your side to refer to this end-user's card in future payment requests.
The card creation response also contains the result of the different verification checks performed under the verification property. See [Verifying Card Details](/circle-mint/docs/verifying-card-details) for details on what checks are performed and the meaning of the returned values.
The card will also be checked by the Circle Risk Service. If the card is denied by Risk then an errorCode and riskEvaluation property will be set on the response see [Risk Evaluation](/circle-mint/docs/cards-banks-and-payments-risk-evaluation) for details on the evaluation responses.
Both the card verification and Risk Service checks are performed asynchronously so the results will not be set in the initial response. To get the results you can either poll the GET /cards/{id} endpoint or use a subscription notification.

#
Create a Card Payment
[](#create-a-card-payment)
You then need to make a [payment request](/circle-mint/reference/createpayment). You will use the id of the card created in the previous step as the "source" for this payment.

When making a payment request you can choose to verify the CVV value again by setting the verification property in the request to cvv. The code to encrypt the CVV value when making a payment is the same as shown above except this time the CardDetails object passed to the encryption function needs only to contain the cvv property. If the verification value in the request is set to none then the encryptedData property does not need to be provided.

You also need to provide an amount and currency to be charged against this end-user's card. Amounts are expressed in units of the currency, with decimals prefixed with a . separator. Currency is expressed in ISO 4217 currency codes - note that for the time being, only [USD is supported](/circle-mint/docs/supported-currencies). You will receive settlement for this payment in [USDC](https://developers.circle.com/stablecoins/docs/what-is-usdc) equivalent (minus processing fees).

Make sure to include a refId property to allow for [idempotent requests](/circle-mint/docs/a-note-on-idempotent-requests) . The end-user's sessionId and ipAddress fields referenced above need to be included once more in the request when making a payment (those values are used for preventing fraud).

#
Auth and Capture (Beta)
[](#auth-and-capture-beta)
When creating a payment POST /v1/payments you have the option to either authorize a payment only (thus delaying capture), or auth and capture a payment at the same time.

By default, POST /v1/payments will auth and capture a payment at the same time. For scenarios where you want to auth a payment only and delay capture until a later time, send autoCapture: false in the request.

For a payment that is authorized only, it is your responsibility to capture the payment using POST /v1/payments/id/capture. Auths that are not captured will expire 6 days + 18 hours after creation. If you do not intend to capture an auth, use POST /v1/payments/id/cancel to release the cardholder's funds.

Authorization (Auth): Check the cardholder has sufficient funds to cover the cost of the transaction.
Capture: Request the transfer of funds from the cardholder's issuing bank.

##📘
Partial Capture (Beta)

The ability to partially capture a payment is not yet supported for all accounts. Please reach out to your Account Manager to confirm if partial capture is enabled for your account.

#
Check for Payment Status
[](#check-for-payment-status)
The Circle Payments API processes payments asynchronously. In order to determine the status of a payment you initiated, you have to poll the API periodically until the payment status has changed from pending to either confirmed or failed. To poll for payment status, use the [retrieve payment endpoint](/circle-mint/reference/getpayment) with the id of the payment you initiated.

#
Congratulations!
[](#congratulations)
🎉 You have accepted your first card payment and your USDC settlement is on its way!

#
Are you ready for the next step?
[](#are-you-ready-for-the-next-step)
If you are in advanced stages of experimenting with our APIs and want to plan moving to production, please start by [applying for a Circle Account](https://www.circle.com/en/circle-account) and subsequently [reach out to sales](mailto:sales@circle.com). We'll be happy to walk you through to the next steps.

We can't wait to see what you are going to build!

Updated 5 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/supported-currencies
Title: Supported Currencies
Description: undefined
Keywords: undefined

##🚧
Attention: Circle is no longer supporting new customers for card payments via our Payments API. For any questions, please reach out to [customer-support@circle.com](mailto:customer-support@circle.com).

##
Crypto Payments & Payouts
[](#crypto-payments--payouts)
Payments: USDC, EURC, BTC, and ETH

Payouts: USDC, EURC, BTC, ETH and MATIC

Updated 5 months ago


URL: https://developers.circle.com/circle-mint/docs/pagination-and-filtering
Title: Date Filtering and Pagination Queries
Description: undefined
Keywords: undefined

Circle’s API query parameters enable you to locate specific transactions quickly and confidently. They allow you to query by date range and/or by page number within a collection resource. These common query parameters are available across most of our collection resources.

##
Common Filters
[](#common-filters)
The following section lists and describes common query parameters that are available across most of our collection resources.

###
Date Ranges
[](#date-ranges)
Our collection resources use createDate DESC to sort data, placing the most recent items at the top of the list.

But if you’re only looking for items in a specific time range, you can specify this range via from and to query parameters:

Parameter	Description	Default
from	Inclusive start of the date range filer applied to the createDate property of the related item.	* (All historic items are included)
to	Inclusive end of the date range filter applied to the createDate property of the related item.	now() (defaults to the timestamp of request processing)
The value format for both parameters is defined as an ISO 8601 Coordinated Universal Time (UTC) string.

Example: Let’s say you want to review all payments from January 2020. Your request would look like this:

Bash
JavaScript

curl 'https://api-sandbox.circle.com/v1/payments?from=2020-01-01T00:00:00Z&to=2020-01-31T23:59:59Z'
##
By Page Number
[](#by-page-number)
The amount of information accessible via Circle APIs can get quite vast. To keep things fresh and snappy all the time, our APIs will automatically paginate the returned items on all collection resources.

Example: Let's say you want to fetch some payments via the [Get a list of payments](/circle-mint/reference/listpayments) resource.

###
Pagination characteristics:
[](#pagination-characteristics)
-
Each API collection endpoint has its own defaults regarding the returned page size. You can specify the required page size via the pageSize query parameter, but be aware that each resource has a fixed maximum for returned items per page. Consult the appropriate API docs for the applicable values for your specific call.

-
To navigate through a collection, use the next/previous page cursors.

-
The pagination cursor is linked to the filter query parameters you provide when requesting the first page. To change a filter parameter, start again from the first page.

-
Once you reach the last page, the pagination cursor will no longer link to the next page, so your iteration ends there. In the same way, there is no previous page link available on the first page.

###
Link relations:
[](#link-relations)
Circle APIs provide the pagination cursor information within a single [HTTP Link header](https://tools.ietf.org/html/rfc5988). The following link relations are used for pagination navigation:

Relation	Description
self	Provides the URL pointing to the current page.
first	Provides the URL pointing to the first page.
next	Provides the URL pointing to the next page. Will be omitted on the last page.
prev	Provides the URL pointing to the previous page. Will be omitted on the first page.
Note: It's important to form calls with Link header values instead of constructing your own URLs.

##
Tutorial:
[](#tutorial)
To familiarize yourself with pagination, try fetching payments via the Get a list of payments resource.

In the Circle [sandbox environment](/circle-mint/docs/circle-apis-production-sandbox-environments), a pagination cursor Link header for a page of payments looks like this:

cURL
JavaScript

curl -I 'https://api-sandbox.circle.com/v1/payments?to=2020-06-02T12:00:00Z&pageAfter=5c5abf90-b2ad-4c8c-8ce3-dbb6a06a040e'i
Look for the relevant pagination cursor information in the response headers:


Link: <https://api-sandbox.circle.com/v1/payments?to=2020-06-02T12:00:00Z&pageSize=50&pageAfter=5c5abf90-b2ad-4c8c-8ce3-dbb6a06a040e>; rel="self", <https://api-sandbox.circle.com/v1/payments?to=2020-06-02T12:00:00Z&pageSize=50>; rel="first", <https://api-sandbox.circle.com/v1/payments?to=2020-06-02T12:00:00Z&pageSize=50&pageAfter=6f14b84d-7a98-4675-a6b9-d7b0c1912091>; rel="next", <https://api-sandbox.circle.com/v1/payments?to=2020-06-02T12:00:00Z&pageSize=50&pageBefore=5c5abf90-b2ad-4c8c-8ce3-dbb6a06a040e>; rel="prev"
For this initial request, we are in the middle of paginating the result set and thus receive a previous and next page link. Let's check the first page and see what’s different:

cURL
JavaScript

curl -I 'https://api-sandbox.circle.com/v1/payments
The response Link header differs from the previous example:


Link: <https://api-sandbox.circle.com/v1/payments?to=2020-06-02T12:01:00Z&pageSize=50>; rel="self", <https://api-sandbox.circle.com/v1/payments?to=2020-06-02T12:00:00Z&pageSize=50>; rel="first", <https://api-sandbox.circle.com/v1/payments?to=2020-06-02T12:00:00Z&pageSize=50&pageAfter=12cd8527-5270-4acd-a1d4-431bd09f2a0e>; rel="next"
Even if not specified in the request, the response pagination links have two additional query parameters set: to and pageSize. This is necessary to keep the pagination stable. Let's say other payments are made while you are reviewing the result set. The first page will update to contain the new payments, changing the pagination.

Updated 7 months ago


URL: https://developers.circle.com/circle-mint/docs/receive-external-funds
Title: Receive External, Cryptocurrency or USDC Funds
Description: undefined
Keywords: undefined

#
1. Get an API key
[](#1-get-an-api-key)
The Accounts API uses [API keys](/circle-mint/docs/api-keys) as the mechanism to authenticate client requests. The API key must be set in the Authorization header of the request sent from your backend server. The format of the header is Bearer secret-key-value.

To obtain an API key for the sandbox environment, simply create an account and generate a new key in settings - it only takes a few seconds.


[
Get an API key
](https://app-sandbox.circle.com)
Once you have generated your API key, record it in a secure place.

#
2. Create a New Wallet
[](#2-create-a-new-wallet)
For the purpose of this guide, we'll create a brand new wallet to receive the external funds. You can create new hosted wallets using the [create wallet endpoint](/circle-mint/reference/createwallet). Use the command below (create your own [idempotency key](/circle-mint/docs/a-note-on-idempotent-requests)).

Terminal

# Replace ${YOUR_API_KEY} with your API key
curl -H 'Content-type: application/json' \
  -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X POST https://api-sandbox.circle.com/v1/wallets \
  --data '{"idempotencyKey": "4ddef365-f2fc-4a56-95f5-a214c84ba8f4"}'
You should receive a response like the below.

Create Wallet Response

{
  "data":{
    "walletId":"1000005062",
    "entityId":"7cbdedb1-d526-46b4-af12-4162a002eb9c",
    "type":"end_user_wallet"
  }
}
#
3. Create a Deposit Address
[](#3-create-a-deposit-address)
Now it's time to create a new blockchain deposit address and associate it with the newly created wallet. You can create a new deposit address using the [generate new address endpoint](/circle-mint/reference/generateaddress).

Since the Accounts API supports [multiple chains and currencies](/circle-mint/docs/supported-chains-and-currencies), you have to specify the currency and chain you want to utilize. You can generate an Ethereum address to receive USDC using the command below (generate your own [idempotency key](/circle-mint/docs/a-note-on-idempotent-requests)).

Terminal

# Replace ${YOUR_API_KEY} with your API key
# Replace ${WALLET_ID} with the wallet id from the previous step
curl -H 'Accept: application/json' \
  -H 'content-type: application/json' \
  -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X POST --url https://api-sandbox.circle.com/v1/wallets/${walletId}/addresses \
  --data '{"idempotencyKey": "ed9a7b3b-b45f-4262-a8aa-8dfee5a4f31b", "currency": "USD", "chain": "ETH"}'
You could similarly create an Algorand deposit address by setting chain to ALGO.

You will receive a response like the below.

Generate Address Response

{
  "data":{
    "address":"0x618b24211048c6ec8b29e8129c2bbc5cf80c9f0b",
    "currency":"USD",
    "chain":"ETH"
  }
}
##📘
When creating a Stellar deposit address, note that the response will include a secondary blockchain address identifier, addressTag. This addressTag value must likewise be included when performing transfers to the deposit address, otherwise the fund will not go through.

#
4. Receive the Funds
[](#4-receive-the-funds)
The [sandbox environment](/circle-mint/docs/circle-apis-production-sandbox-environments) is connected to the [testing networks](https://developers.circle.com/stablecoins/docs/usdc-on-test-networks) of the blockchains where [USDC is supported](/circle-mint/docs/supported-chains-and-currencies). For BTC and ETH, we use testnet and Sepolia, respectively.

For Ethereum, you can send USDC from a Sepolia wallet in order to fund your Circle hosted wallet. You can obtain some USDC on Sepolia for testing, you can get some by using a [USDC faucet](https://faucet.circle.com/). Go to [faucet.circle.com](https://faucet.circle.com/) and make sure you choose the Sepolia button. Paste the address you generated above (be very careful to not miss any characters) and hit Submit. You will be able to monitor the transfer on the blockchain by using the Etherscan link provided by the faucet.

##❗️
Do NOT send real USDC to addresses generated in the sandbox environment as your funds might be permanently lost.

##❗️
Use supported chains and currencies only

Transferring unsupported currencies using the Accounts API may result in a permanent loss of funds. You can verify the token contract details for supported currencies [here](/circle-mint/docs/supported-chains-and-currencies).

#
5. Check the Status of the Transfer
[](#5-check-the-status-of-the-transfer)
You can use the [retrieve transfers endpoint](/circle-mint/reference/listtransfers) to find transfers that are destined to the wallet you created earlier. You can use it as in the command below.

Terminal

# Replace ${YOUR_API_KEY} with your API key
# Replace ${WALLET_ID} with the wallet id from the previous step
curl -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X GET https://api-sandbox.circle.com/v1/transfers?destinationWalletId=${WALLET_ID}
You will receive a response like below.

Get List of Transfers Response

{
  "data":[
    {
      "id":"8239d630-f4fc-3da1-a4fc-e641186eee79",
      "source":{
        "type":"blockchain",
        "address":null,
        "chain":"ETH"
      },
      "destination":{
        "type":"wallet",
        "id":"1000005062",
        "address":"0x618b24211048c6ec8b29e8129c2bbc5cf80c9f0b"
      },
      "amount":{
        "amount":"1.00",
        "currency":"USD"
      },
      "transactionHash":"0x3ca73e108173b2270f81c09e23dfb518d5e175c0e674df5d2ac9a0743765f5ac",
      "status":"running",
      "createDate":"2020-04-21T11:58:48.505Z"
    }
  ]
}
You can look up the transactionHash on [Etherscan's tracker on Sepolia](https://sepolia.etherscan.io). The full list of USDC chain testnet explorers please refer to [USDC on Testing Networks](https://developers.circle.com/stablecoins/docs/usdc-on-test-networks). For BTC, you can use a [Bitcoin testnet explorer](https://www.blockchain.com/explorer?view=btc-testnet).

As you can see on the response above, the status shows up initially as running because the transaction has been recently initiated. After waiting a few minutes for it to reach the required block [confirmations](/circle-mint/docs/blockchain-confirmations), the status will change to complete. Once it does so (and only once it does so), Circle will credit the receiving wallet with the value of the transfer.

🎉 Congratulations! You have successfully received USDC using Circle's Accounts API.

Make sure you check our quickstart guide on [sending funds externally](/circle-mint/docs/send-funds-externally).

#
6. Ready for the next step?
[](#6-ready-for-the-next-step)
If you are in advanced stages of experimenting with our APIs and want to plan moving to production, please start by [applying for a Circle Mint account](https://www.circle.com/en/circle-mint) and subsequently [reach out to sales](mailto:sales@circle.com). We'll be happy to walk you through to the next steps.

We can't wait to see what you are going to build!

Updated 4 months ago

-
[✨ Send Funds Externally](/circle-mint/docs/send-funds-externally)
-
[✨ Swap USDC Across Blockchains](/circle-mint/docs/swap-usdc-across-blockchains)
-
[Funding Test Wallets](/circle-mint/docs/funding-test-wallets)
-
[Supported Chains and Currencies](/circle-mint/docs/supported-chains-and-currencies)
-
[Wallets, Addresses and Transfers](/circle-mint/docs/wallets-addresses-and-transfers)
-
[Blockchain Confirmations](/circle-mint/docs/blockchain-confirmations)
Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/verifying-card-details
Title: Verifying Card Details
Description: undefined
Keywords: undefined

#
Why is verifying card details important?
[](#why-is-verifying-card-details-important)
As with other types of "pull" payment methods, card transactions are reversible. In other words, you might assume a payment has been completed and it is final, only to find out weeks later that it has been reversed.

While reversible payments are useful for end users and provide them with stronger protection, they are also a reason to think very carefully about how to minimize payment reversals. End users have several reasons to pursue a payment reversal, but many times the root cause is a fraudulent transaction in the first place.

##🚧
Fraud Liability

You are ultimately liable for any and all losses caused by fraudulent transactions or other types of payment reversals.

Essentially you want to have a reasonable level of certainty that the end user using your product is actually in control of the payment method being used. Verifying card details is a tool to help ensure that is the case.

#
CVV Verification
[](#cvv-verification)
##
What is CVV?
[](#what-is-cvv)
CVV stands for card verification value, also known as card verification code (CVC). It is a code part of a debit or credit card that is private to the end user. Asking for the CVV of a debit or a credit card is a way to increase the level of confidence that the end user is actually in physical possession of the card, given that databases of stolen cards often don't include the CVV.

##
CVV Check
[](#cvv-check)
CVV check must be performed at the time of [card creation](/circle-mint/reference/createcard) but is optional at the time of [payment creation](/circle-mint/reference/createpayment). If the CVV check is successful at the time of card creation, you can assume that card is "good" for future payments, and you can opt not to run CVV checks for subsequent payments for that card.

You can come up with more sophisticated rules where you run CVV checks for subsequent payments depending on the payment amount (if it's significantly higher than usual) and other risk variables. Keep in mind that not running CVV checks during payments time significantly reduces your ability to dispute payment reversals later on.

CVV is sent to the API encrypted in the encryptedData field. Check the encryption section of the [accepting card payments](/circle-mint/docs/accept-card-payments-online#section-encrypt-end-users-card-details) guide for more details.

Code	Description
pending	Check in progress
pass	Value matched
fail	Value not matched
unavailable	Card issuer does not support CVV check
not_requested	Check not requested (only for payment request - CVV is always attempted for card creation)
#
Address Verification
[](#address-verification)
##
What is AVS?
[](#what-is-avs)
Address Verification Service (AVS) is another mechanism that customers can use to prevent card fraud. The address verification service compares the billing address submitted by the end user against the billing address kept on file by the card issuer.

##📘
AVS Coverage

The address verification check only applies to cards issued in the United States, Canada, and the United Kingdom.

##
The AVS Check
[](#the-avs-check)
The address verification check is executed automatically for every [card creation](/circle-mint/reference/createcard) and [payment creation](/circle-mint/reference/createpayment) transaction. The check can compare 2 distinct parts of the end user's billing address: the postal code, and the first address line.

Code	Summary	Description
A	Partial match	Street address matches, but both 5-digit and 9-digit ZIP Code do not match.
B	Partial match	Street Address Match for International Transaction. Postal Code not verified due to incompatible formats.
C	Verification unavailable	Street Address and Postal Code not verified for International Transaction due to incompatible formats.
D	Full Match (International Transaction)	Street Address and Postal Code match for International Transaction.
E	Data invalid	AVS data is invalid or AVS is not allowed for this card type.
F	Full Match (UK only)	Street address and postal code match. Applies to U.K. only.
G	Verification unavailable	Non-US Issuer does not participate.
I	Verification unavailable	Address information not verified for international transaction.
K	Address mismatch	Card member’s name matches but billing address and billing postal code do not match.
L	Partial match	Card member’s name and billing postal code match, but billing address does not match.
M	Full match (International Transaction)	Street Address match for international transaction. Address and Postal Code match.
N	No match	No match for address or ZIP/postal code.
O	Partial match	Card member’s name and billing address match, but billing postal code does not match.
P	Partial match (International Transaction)	Postal code match. Acquirer sent both postal code and street address, but street address not verified due to incompatible formats.
R	Verification unavailable	Issuer system unavailable, retry.
S	Verification unavailable	AVS not supported
U	Verification unavailable	Address unavailable
W	Partial match	Postal code matches but address does not match
X	Full match	Street address and postal code match
Y	Full match	Street address and postal code match
Z	Partial match	5 digit zip code match only
-	Verification unavailable	An error occurred attempting AVS check
#
Running CVV and AVS Checks
[](#running-cvv-and-avs-checks)
##
Card Creation Time
[](#card-creation-time)
The results of the verification checks are returned in the verification property of the response. The checks are done asynchronously so the value of verification.cvv and verification.avs will be initially be returned as pending. You will need to poll the GET /cards/{id} endpoint or use a subscription notification to wait for the checks to complete and inspect the returned values.

For more details, check the [create card endpoint](/circle-mint/reference/createcard).

##
Payment Time
[](#payment-time)
When creating a payment, you have 2 options in terms of card verification. If the value of the verification property in the request is cvv then a CVV check will be performed, with a value of none or three_d_secure the CVV check is skipped. As with the card creation request the verification results will be returned in the verification property of the payment response.
If a CVV check is requested and the result is not pass then the payment will be failed. An AVS check will be performed for cvv three_d_secure and none values but the payment will not be failed in the case of a mismatch.
For more details, check the [create payments endpoint](/circle-mint/reference/createpayment).

Updated 5 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/circle-payments-api-quickstart
Title: Card Payments Quickstart
Description: undefined
Keywords: undefined

#
1. Get an API key
[](#1-get-an-api-key)
The Circle Payments API uses API keys as the mechanism to authenticate client requests. The API key must be set in the Authorization header of the request sent from your backend server. The format of the header is Bearer secret-key-value.

[Learn more about authenticating API calls here](/circle-mint/docs/getting-started-with-the-circle-apis#section-authentication).

To obtain an API key for the sandbox environment, simply create an account and generate a new key in settings - it only takes a few seconds.


[
Get an API key
](https://app-sandbox.circle.com)
Once you have generated your API key, record it in a secure place.

#
2. Install the sample application
[](#2-install-the-sample-application)
We have built a [sample web application](https://github.com/circlefin/payments-sample-app) to demonstrate the use of Circle Payments API. You can clone the Github repository, configure the app and run it locally.

##📘
NPM and Yarn

If you don't already, make sure you have [npm](https://www.npmjs.com/get-npm) and [yarn](https://yarnpkg.com/lang/en/docs/install/) installed.

Terminal

# Clone the sample app Github repository
git clone https://github.com/circlefin/payments-sample-app.git

# Change into sample app folder
cd payments-sample-app

# Create a .env file and configure the base url for api calls
echo BASE_URL=https://api-sandbox.circle.com > .env

# Install the dependencies
yarn install

# Run the sample app locally
yarn dev
The sample application should now be running at: [http://localhost:3011](http://localhost:3011).
##📘
Enter Your API Key

Take the API key you generated on step 1, and enter it on the settings tab on the top right corner of the sample application.

#
3. Accept your first card payment
[](#3-accept-your-first-card-payment)
On the sample application, click on the Charge Flow button under the "Example UI implementation" section.

Use one of the [test card numbers](/circle-mint/docs/test-card-numbers) available on the sandbox environment. For example, 4007400000000007, real card numbers are not accepted in the sandbox environment.

Type in an amount, pick a value for CVV and expiry date, and add some billing information details.

Submit the payment creation request by clicking on the Make Payment button.

🎉 Congrats, you processed your first card payment that settles in [USDC](https://developers.circle.com/stablecoins/docs/what-is-usdc)! You should see a payment identifier on the resulting screen, something like Payment id: 81855279-b53d-4119-9f1e-5d0af00f0c24.

#
4. Check the status of your card payment
[](#4-check-the-status-of-your-card-payment)
Now make an API call to retrieve the status of your card payment using the [get payment endpoint](/circle-mint/reference/getpayment). Use your API key from step 1 and the payment identifier you obtained on step 3.

Terminal
JavaScript

# Replace ${YOUR_API_KEY} with the API key obtained on step 1
# Replace ${PAYMENT_ID} with the payment id obtained on step 3
curl -H 'Accept: application/json' \
  -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X GET --url https://api-sandbox.circle.com/v1/payments/${PAYMENT_ID}
You should receive a response such as below.

Payment status response

{
  "data": {
    "id": "81855279-b53d-4119-9f1e-5d0af00f0c24",
    "type": "payment",
    "merchantId": "ff71551d-ae18-492d-baf1-d9205e20e0bf",
    "merchantWalletId": "1000002584",
    "amount": {
      "amount": "5.00",
      "currency": "USD"
    },
    "source": {
      "id": "1fa990d9-fd12-400c-bc7d-e54a428f7570",
      "type": "card"
    },
    "description": "Payment",
    "createDate": 1583830582515,
    "trackingRef": "20674453824672941243272",
    "status": "confirmed",
    "fees": {
      "amount": "0.10",
      "currency": "USD"
    }
  }
}
🎉 You are all done!

Your payment will eventually change its status from confirmed to paid - at that point you have funds in your account available for use.

#
5. Like what you see? Keep going!
[](#5-like-what-you-see-keep-going)
By accepting a card payment that settles in USDC, you have just touched the tip of the iceberg of [Circle APIs](/circle-mint/docs/getting-started-with-the-circle-apis).

Make sure you read the longer form guide on [accepting payments online](/circle-mint/docs/accept-card-payments-online). Check how to [subscribe to notifications on changes in payments status](/circle-mint/docs/circle-apis-notifications-quickstart). Also make sure you check the rest of the documentation - a few starting points are suggested below.

#
6. Ready for the next step?
[](#6-ready-for-the-next-step)
If you are in advanced stages of experimenting with our APIs and want to plan moving to production, please start by [applying for a Circle Account](/circle-mint/docs/sandbox-to-production-transition-guide) and subsequently [reach out to sales](mailto:sales@circle.com). We'll be happy to walk you through to the next steps.

We can't wait to see what you are going to build!

Updated 5 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/api-logs
Title: API Logs
Description: undefined
Keywords: undefined

[API Logs](https://app-sandbox.circle.com/developer/logs) are transaction logs that enable you to view your API transaction history and debug API errors with no setup required. They show the most accurate representation of what Circle received and sent. When you send an API request, Circle stores it along with its associated response. On the API logs page, you can view these API logs for seven days after the request is sent and filter them to find the specific request you are looking for. You can use the API logs to debug API errors without having to log out of your system.

- To access the logs, go to Circle Sandbox or Circle Account and click on Developer Tab --> Logs.
##
API Log Data Elements
[](#api-log-data-elements)
For each API request and response, Circle stores the following information, which you can retrieve and review in the Developer tab.

Field	Description
HTTP Status	HTTP Status code for each request such as 200 or 400.
Path	The path excludes the base URL.
Request ID	The X-Request-Id in the header provided in the request or is generated plus by Circle and returned in the response.
User Agent	The User-Agent field in the request header. It is common that the HTTP library used will provide this field by default.
Idempotency	Idempotency Key sent in the request body. This is only found in POST requests.
Origin	Includes the protocol (HTTP/HTTPS), the domain or IP address, and the port number if applicable.
Time	Timestamp of when the request was received.
Request Body	The full request body.
Response Body	The full response body.


##
API Log Filtering
[](#api-log-filtering)
To filter your search, use the search fields and popup menus at the top of the Circle Sandbox or Circle Mint page.



Filter Name	Description
Search	Filter by request ID, resource ID, or idempotency key ID.
Date Range	Filter results by date range.
Status	Filter results by succeeded and/or failed. Succeeded includes all 2## and failed includes all 4## and 5##.
Method	Filter results by HTTP Method. Supports POST, PUT, PATCH, DELETE, and/or GET.
Path	Filter results by the URL path such as /payments.
##
Data Redaction
[](#data-redaction)
Due to the sensitivity of some data provided to Circle, including financial payment methods and PII data, some values will be redacted from the request and response payloads. Values that are redacted are replaced with the value “[redacted].” If you need access to this sensitive data, reach out to your Circle customer success manager.

Updated 7 months ago


URL: https://developers.circle.com/circle-mint/docs/webhook-subscription-management
Title: Managing Webhook Subscriptions
Description: undefined
Keywords: undefined

Circle’s webhooks are an automated method for apps to receive notifications the moment a transaction is completed or fails to complete. Circle will make a request to your application when an operation is completed. That means your app doesn’t need to poll Circle to know whether an operation has completed in the blockchain—the confirmation will arrive automatically.

##
Set Up Subscriptions
[](#set-up-subscriptions)
Through the Circle UI in Sandbox or Production, you can set up Webhoook subscriptions. This can be done by:

- Navigating to Developer → Subscriptions in Sandbox or Production.
- Selecting Add Subscription
- Entering an endpoint URL you would like notifications to go to.
- Selecting Add Endpoint
##📘
If you are just getting started you can use our notification quickstart [[link](/circle-mint/docs/circle-apis-notifications-quickstart)] guide to setup you local environment and acquire a subscription endpoint URL.



##
View Subscriptions
[](#view-subscriptions)
Via Circle’s UI you can view all subscriptions registered to your account on Developer → Subscriptions. Currently, our Sandbox environment permits up to 3 subscriptions and our Production environment supports one.

##
Remove Subscriptions
[](#remove-subscriptions)
While viewing subscriptions you are also provided the option to remove subscriptions. This is done by simply selecting kebab on the Endpoint and selecting remove.

##
API Endpoints
[](#api-endpoints)
All these actions can also be done using Circle’s APIs. See [API reference subscriptions](/circle-mint/reference/createsubscription) for more detail.

Updated 5 months ago


URL: https://developers.circle.com/circle-mint/docs/overview
Title: Crypto Deposits API
Description: undefined
Keywords: undefined

##
Move Money On-Chain
[](#move-money-on-chain)
Circle Mint customers can use the Crypto Deposits, Crypto Payouts, and Accounts APIs to manage their Mint account in a programmatic way.

- Accept crypto deposits: Accept USDC and other digital asset payments via supported blockchains.
- Make crypto payouts: Send fast, low-cost crypto payouts to your customers, vendors, and suppliers.
- Manage account balances: Create and manage USDC, BTC, and ETH balances for your global customer base with our flexible, reliable accounts infrastructure. Please note that this API is available on a limited basis.
##📘
Note:

This API is available at an additional cost and require further solutioning with our team in order to best meet your organization's needs. If interested, please contact your Circle representative or [sales@circle.com](mailto:sales@circle.com) for more information.

##
Crypto Deposits API
[](#crypto-deposits-api)
The Crypto Deposits API allows you to accept [USDC](https://developers.circle.com/stablecoins/docs/usdc-on-test-networks) and other crypto deposits from your customers.

With the Crypto Deposits API you can:

- Take USDC, ETH, and BTC payments directly through on-chain transfers.
- Easily manage reporting and reconciliation through the use of payment intents.
➡ [Read the quick start guide on processing payments notifications](/circle-mint/docs/crypto-payments-quickstart).

##
Crypto Payouts API
[](#crypto-payouts-api)
The Payouts API allows you to send payouts to customers, vendors, or suppliers directly using crypto payouts.

Fund payouts in USDC using your Circle Mint account, which can receive deposits from both traditional and blockchain payment rails.

➡ [Read the quickstart guide on issuing payouts](/circle-mint/docs/crypto-payouts).

##
Accounts API
[](#accounts-api)
The Accounts API allows you to easily create and manage accounts and balances for your customers. Use it to execute transfers of funds across accounts within your Circle Mint infrastructure. This API is only available on a limited basis.

Our API eliminates the complexity usually associated with custody and security of digital assets, blockchain address management, and transaction fees.

With the Accounts API you can:

- Embed US Dollar-denominated accounts into your product or service without the hassle of legacy bank account structures.
- Manage multi-asset accounts infrastructures for your customers that offer seamless fund transfers, including hosted accounts and on-chain [USDC](https://developers.circle.com/stablecoins/docs/usdc-on-test-networks) connectivity.
- Manage customer accounts across 150+ countries.
- Benefit from Circle's extensive experience supporting over 10 million digital account customers, $5B in digital asset custody, and hundreds of billions of dollars in transactions.
➡ [Learn more about the Accounts API](/circle-mint/docs/getting-started-with-the-circle-digital-dollar-accounts-api).

#
More resources
[](#more-resources)
Dig deeper into our documents to discover Circle’s:

- [Supported countries](/circle-mint/docs/supported-countries)
- [Supported currencies](/circle-mint/docs/supported-currencies)
- [Note on idempotent requests](/circle-mint/docs/a-note-on-idempotent-requests)
Updated 4 months ago


URL: https://developers.circle.com/circle-mint/docs/notifications-data-models
Title: Notifications
Description: undefined
Keywords: undefined

Circle API Notifications are subscriber endpoints that enable you to easily receive notifications every time the status of a resource changes.

#
Common Considerations
[](#common-considerations)
All current and future implementations of notification messages have the following attributes:

Name	Type	Description	Sample
clientId	UUID	Client identifier	c60d2d5b-203c-45bb-9f6e-93641d40a599
notificationType	String	The type of notification	payments
version	int		
#
Current Implementations
[](#current-implementations)
This section lists models currently used by existing flows.

##
Payment Flow
[](#payment-flow)
This section introduces the events that may occur after a payment has been created. Each of the events lists the additional fields in a typical notification payload.

###
Confirmed
[](#confirmed)
Confirmed means that Circle accepts the payment but it is not settled yet. Following structure represents the notifications for confirmed payments.

Confirmed Payment Notification Payload

{
   "clientId": "c60d2d5b-203c-45bb-9f6e-93641d40a599",
   "notificationType": "payments",
   "payment": {...}
}
The payment payload will be a [Payment Object](/circle-mint/docs/circle-api-resources#payment-object).

###
Failed
[](#failed)
A payment might fail due to insufficient balance, invalid credentials, etc. A notification with the following structure is sent for the failed payments.

Failed Payment Notification Payload

{
   "clientId": "c60d2d5b-203c-45bb-9f6e-93641d40a599",
   "notificationType": "payments",
   "payment": {...}
}
The payment payload will be a [Payment Object](/circle-mint/docs/circle-api-resources#payment-object).

###
Refund
[](#refund)
Confirmed refund requests notifications are structured as follows:

Refunded Payment Notification Payload

{
   "clientId": "c60d2d5b-203c-45bb-9f6e-93641d40a599",
   "notificationType": "payments",
   "payment": {...}
}
The payment payload will be a [Refund Object](/circle-mint/docs/circle-api-resources#refund-object).

###
Cancel
[](#cancel)
Confirmed cancel requests notifications are structured as follows:

Cancelled Payment Notification Payload

{
   "clientId": "c60d2d5b-203c-45bb-9f6e-93641d40a599",
   "notificationType": "payments",
   "payment": {...}
}
If the payment has not been captured yet, the payment will be "voided" and a notification of the original payment will be received with a status of failed.

If the payment is already captured, the payment will be "refunded" and the payment notification will be a [Cancel Object](/circle-mint/docs/circle-api-resources#cancel-object).

##
Payout Flow
[](#payout-flow)
###
Completed
[](#completed)
Completed payouts are settled payouts. Therefore, the funds should be available in the destination accounts. Following structure represents the notifications for completed payouts.

Completed Payout Notification Payload

{
   "clientId": "c60d2d5b-203c-45bb-9f6e-93641d40a599",
   "notificationType": "payouts",
   "payout": {...}
}
The payout payload will be a [Payout Object](/circle-mint/docs/circle-api-resources#payout-object).

###
Failed
[](#failed-1)
Failed payouts notifications are structured as follows:

Failed Payout Notification Payload

{
   "clientId": "c60d2d5b-203c-45bb-9f6e-93641d40a599",
   "notificationType": "payouts",
   "payout": {...}
}
The payout payload will be a [Payout Object](/circle-mint/docs/circle-api-resources#payout-object).

##
Bank Account (Wire) Verification Flow
[](#bank-account-wire-verification-flow)
###
Failed
[](#failed-2)
Failed Bank Accounts (Wires) notifications are structured as follows:

Failed Bank Account (Wire) Notification Payload

{
   "clientId": "c60d2d5b-203c-45bb-9f6e-93641d40a599",
   "notificationType": "wire",
   "wire": {...}
}
The wire payload will be a [Wire Account Object](/circle-mint/docs/circle-api-resources#wire-account-object).

###
Approved
[](#approved)
Approved Bank Accounts (Wires) notifications are structured as follows:

Bank Account (Wire) Approval Notification Payload

{
   "clientId": "c60d2d5b-203c-45bb-9f6e-93641d40a599",
   "notificationType": "wire",
   "wire": {...}
}
The wire payload will be a [Wire Account Object](/circle-mint/docs/circle-api-resources#wire-account-object).

##
Transfer Flow
[](#transfer-flow)
###
Created
[](#created)
A notification with the structure below is sent on transfer creation.

Created Transfer Notification Payload

{
   "clientId": "c60d2d5b-203c-45bb-9f6e-93641d40a599",
   "notificationType": "transfers",
   "transfer": {...}
}
The transfer payload will be a [Transfer Object](/circle-mint/docs/circle-api-resources#transfer-object).

###
Failed
[](#failed-3)
Failed transfers notifications are structured as follows:

Failed Transfer Notification Payload

{
   "clientId": "c60d2d5b-203c-45bb-9f6e-93641d40a599",
   "notificationType": "transfers",
   "transfer": {...}
}
The transfer payload will be a [Transfer Object](/circle-mint/docs/circle-api-resources#transfer-object).

###
Completed
[](#completed-1)
Completed transfers notifications are structured as follows:

Completed Transfer Notification Payload

{
   "clientId": "c60d2d5b-203c-45bb-9f6e-93641d40a599",
   "notificationType": "transfers",
   "transfer": {...}
}
The transfer payload will be a [Transfer Object](/circle-mint/docs/circle-api-resources#transfer-object).

##
Exchange Conversion Flow
[](#exchange-conversion-flow)
For conversions, there will be a notification sent when the conversion status changes from pending to complete or failed.

###
Completed
[](#completed-2)
Completed conversions notifications are structured as follows:

Failed Conversion Notification Payload

{
  "clientId": "b1e4e9fe-0bf1-43ad-86c7-3ab993b0051b",
  "notificationType": "conversions",
  "version": 1,
  "customAttributes": {
    "clientId": "b1e4e9fe-0bf1-43ad-86c7-3ab993b0051b"
  },
  "conversion": {
    "id": "b8627ae8-732b-4d25-b947-1df8f4007a29",
    "status": "complete",
    "source": {
      "type": "wallet",
      "id": "0123456789"
    },
    "from": {
      "amount": "1.00",
      "currency": "BTC"
    },
    "to": {
      "amount": "45400.41",
      "currency": "USD"
    },
    "createDate": "2020-04-10T02:13:30.000Z"
  }
}
###
Failed
[](#failed-4)
Failed conversions notifications are structured as follows:

JSON

{
  "clientId": "b1e4e9fe-0bf1-43ad-86c7-3ab993b0051b",
  "notificationType": "conversions",
  "version": 1,
  "customAttributes": {
    "clientId": "b1e4e9fe-0bf1-43ad-86c7-3ab993b0051b"
  },
  "conversion": {
    "id": "a844411c-09e9-440f-b52d-663c9bea7040",
    "source": {
      "type": "wallet",
      "id": "1000174786"
    },
    "from": {
      "amount": "1000000000.00",
      "currency": "USD"
    },
    "to": {
      "amount": "264188.438215702990903727",
      "currency": "ETH"
    },
    "status": "failed",
    "createDate": "2022-06-02T15:02:34.810Z"
  }
}
Updated 4 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/getting-started-with-the-circle-digital-dollar-accounts-api
Title: Accounts API
Description: undefined
Keywords: undefined

#
Circle Accounts API
[](#circle-accounts-api)
The Accounts API allows you to easily create and host digital wallets for your customers, and manage transfers of funds across accounts- whether they are within the Circle platform, or in / out of the platform via on-chain [USDC](https://developers.circle.com/stablecoins/docs/what-is-usdc), EURC, BTC, and ETH connectivity.

##📘
Limited Availability

This API is only available on a limited basis. If interested, please contact your Circle representative or [sales@circle.com](mailto:sales@circle.com) for more information.

Our APIs abstract the complexity inherent to custody and security of digital assets, as well as blockchain address management and transaction fees.

##👍
With the Accounts API you can:

- Embed US Dollar, Euro, BTC, and ETH denominated digital balances into your product or service without dealing with the complexity of legacy bank account structures.
- Manage account infrastructure for your customers including seamless transfer of funds, across hosted accounts or via on-chain connectivity.
- Take [USDC](https://developers.circle.com/stablecoins/docs/what-is-usdc), EURC, BTC, and ETH payments with minimum cost and no exposure to reversals.
All of that for customers across 150+ countries, and based on Circle's extensive experience supporting over 10 million digital account customers, $5B in digital asset custody and hundreds of billions of dollars in transactions.

#
Getting Started
[](#getting-started)
You can check our [quickstart guide on sending funds externally](/circle-mint/docs/send-funds-externally) to understand how to use the Accounts API to transfer funds to other platforms via on-chain transfers.

Conversely, you can check our [quickstart guide on receiving external funds](/circle-mint/docs/receive-external-funds) to check how you can use the Accounts API to set up an infrastructure for receiving external USDC, EURC, BTC, and ETH on-chain.

#
Ready for the next step?
[](#ready-for-the-next-step)
If you are in advanced stages of experimenting with our APIs and want to plan moving to production, start by [applying for a Circle Mint account](https://www.circle.com/en/circle-mint) and subsequently [reach out to sales](mailto:sales@circle.com). We'll be happy to walk you through to the next steps.

We can't wait to see what you are going to build!

Updated 3 months ago

-
[Send funds externally](/circle-mint/docs/send-funds-externally)
-
[Receive external funds](/circle-mint/docs/receive-external-funds)
-
[Funding Test Wallets](/circle-mint/docs/funding-test-wallets)
-
[Supported Chains and Currencies](/circle-mint/docs/supported-chains-and-currencies)
-
[Wallets, Addresses and Transfers](/circle-mint/docs/wallets-addresses-and-transfers)
-
[Blockchain Confirmations](/circle-mint/docs/blockchain-confirmations)

URL: https://developers.circle.com/circle-mint/docs/getting-started-with-the-circle-payments-api
Title: Circle APIs: Getting Started with Crypto Payments
Description: undefined
Keywords: undefined

Our Payments API is an easy-to-implement API that lets you accept payments from crypto wallets and receive a settlement in [USDC](https://developers.circle.com/stablecoins/docs/what-is-usdc). It is designed to be used by any kind of business or internet commerce, not just crypto applications.

#
Getting Started
[](#getting-started)
Here's how to get started with the Circle Payments API:

Use our [Crypto Payments Quickstart guide](/circle-mint/docs/crypto-payments-quickstart) to get a sample web application up and running and process your first crypto payment that settles in [USDC](https://developers.circle.com/stablecoins/docs/what-is-usdc) or the payments source currency.


[
Start with crypto in 5 minutes
](/docs/crypto-payments-quickstart)
#
More resources
[](#more-resources)
Dig deeper into our documents to discover Circle’s:

- [Supported countries](/circle-mint/docs/supported-countries)
- [Supported currencies](/circle-mint/docs/supported-currencies)
- [Note on idempotent requests](/circle-mint/docs/a-note-on-idempotent-requests)
#
Ready for the next step?
[](#ready-for-the-next-step)
After experimenting with our APIs, you’ll want to start building test integrations in Sandbox prior to moving into production. Start by [applying for a Circle Mint account](https://www.circle.com/en/circle-mint). We'll be happy to walk you through the next steps.

Updated 5 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/openapi
Title: OpenAPI
Description: undefined
Keywords: undefined

[OpenAPI Specification](https://swagger.io/specification/) (OAS) OpenAPI Specification (OAS) is an API description format for REST APIs. Our OpenAPI 3.0 files allow you to view our APIs with your preferred tool, generate code, create mock servers and generate Postman collections.

- If you work regularly in Postman, Circle offers a pre-packaged [Postman](/circle-mint/docs/postman) collection for your convenience.
#
OpenAPI Files
[](#openapi-files)
Circle offers you an OpenAPI file for each of the products we offer. To acquire any or all of them, visit [Circle’s OpenAPI repository](https://github.com/circlefin/openapi) at Github.

#
Alternate Viewers
[](#alternate-viewers)
Would you rather view Circle’s OpenAPI files within your own API viewer rather than the provided API References? Here are some viewers that we recommend:

Whatever the reason may be, if you prefer your own API viewer over the provided API References you can use Circle’s [OpenAPI](https://github.com/circlefin/openapi) files to view them in your tool of choice.

Here are some great viewers that we like:

- [Swagger Editor/Viewer](https://editor.swagger.io)
- [Swagger Hub](https://swagger.io/tools/swaggerhub/) (Requires an account)
- Visual Studio Code: [OpenAPI (Swagger) Editor](https://marketplace.visualstudio.com/items?itemName=42Crunch.vscode-openapi) by 42Crunch or [Swagger Viewer](https://marketplace.visualstudio.com/items?itemName=Arjun.swagger-viewer) by Arjun G
#
Generate Code
[](#generate-code)
Use [Circle’s OpenAPI files](https://github.com/circlefin/openapi), to auto-generate client code in your chosen programming language. Try using an OPenAPI generator like [Swagger](https://swagger.io/).

#
Mock Server
[](#mock-server)
To reduce time in development and testing, use Circle’s OpenAPI to run a mock server of Circle’s APIs locally and in your CI environments. A mock API server allows you to emulate a real API (such as Circle) locally, in your test environments, and/or publicly. This mock server can receive requests and will respond to them with static or dynamic data that simulates what the real API would return.

##
Getting started
[](#getting-started)
Use the open-source tool [Prism](https://github.com/stoplightio/prism) to generate a mock server from Circle’s OpenAPI specification. Prism is an open-source HTTP mock server that can mimic an API’s behavior as if it were a real server. Prism generates mock HTTP servers from your OpenAPI v2/v3 (aka Swagger) documents.

To Install Prism, run either of these two commands:

Text

npm install -g @stoplight/prism-cli

# OR

yarn global add @stoplight/prism-cli
Next, feed a specification file to Prism so that it can generate the desired mock API server.

Text

prism mock https://raw.githubusercontent.com/circlefin/openapi/master/openapi/json/payments.json
At this point you should see a trace ending with the word start in green.

Now you can send requests via cURL or Postman to the destination Prism is listening on.

Updated 5 months ago


URL: https://developers.circle.com/circle-mint/docs/blockchain-confirmations
Title: Blockchain Confirmations
Description: undefined
Keywords: undefined

#
"Reorgs" and Associated Risks
[](#reorgs-and-associated-risks)
Transactions in a blockchain are packaged into blocks. Different blockchains have different rules for how to decide which transactions get packaged into a block, but they often include rules for invalidating recently generated blocks.

This rewrite of history is called a "reorganization" ("reorg" for short) and invalidates any transactions included in the invalidated blocks. You can think of invalidated transactions that moved funds as transactions that "never happened".

Such events can represent a security risk, because an attacker could use a cryptocurrency transfer as payment for a good or service, and subsequently trigger a reorg, thus invalidating his transfer (and getting his cryptocurrency back) while keeping the good or service he paid for.

#
Confirmations
[](#confirmations)
To protect against reorg attacks, it's beneficial to wait for a number of confirmed blocks before recognizing a transfer. As more blocks are processed after a given block, the difficulty with which that block may be invalidated increases. Therefore, by choosing how many blocks to wait before recognizing a transfer, a receiver is able to balance speed (waiting very few blocks in order to process the transfer quickly while taking on risk of losing money due to a reorg) and security (waiting more blocks to minimize the risk of a reorg at the cost of slower transfer processing).

The number of blocks you wait before considering a transfer valid is called the "confirmation number", which is typically different for different chains. Circle Mint and its APIs use the following confirmation numbers for each supported chain. They are based on a variety of factors that Circle has considered, including a given chain's historical performance, reorganizations, and overall architecture, and they do not reflect the blockchain networks' inherent requirements or limits.

Chain	Confirmations/Blocks	Approximate Time
Algorand ([USDC](https://algoexplorer.io/asset/31566704))	1	~5 seconds
Arbitrum ([USDC](https://arbiscan.io/token/0xaf88d065e77c8cC2239327C5EDb3A432268e5831))	300	~3 minutes
Avalanche ([USDC](https://snowtrace.io/token/0xB97EF9Ef8734C71904D8002F8b6Bc66Dd9c48a6E), [EURC](https://snowtrace.io/token/0xc891eb4cbdeff6e073e859e987815ed1505c2acd))	1	~2 seconds
Base ([USDC](https://basescan.org/token/0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913))	12	~3 minutes
Bitcoin (BTC)	4	~40 minutes
Celo ([USDC](https://celoscan.io/token/0xcebA9300f2b948710d2653dD7B07f33A8B32118C))	1	~5 seconds
Ethereum ([USDC](https://etherscan.io/token/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48), [EURC](https://etherscan.io/token/0x1aBaEA1f7C830bD89Acc67eC4af516284b1bC33c), ETH)	12	~3 minutes
Flow ([USDC](https://flowscan.org/contract/A.b19436aae4d94622.FiatToken))	1	~2.5 seconds
Hedera ([USDC](https://app.dragonglass.me/hedera/tokens/0.0.456858))	N/A	~3 seconds
NEAR ([USDC](https://nearblocks.io/token/17208628f84f5d6ad33f0da3bbbeb27ffcb398eac501a31bd6ad2011e36133a1))	1	~2 seconds
Noble ([USDC](http://mintscan.io/noble/assets))	1	~7 seconds
OP Mainnet ([USDC](https://optimistic.etherscan.io/token/0x0b2c639c533813f4aa9d7837caf62653d097ff85))	12	~4 minutes
Polkadot Asset Hub ([USDC](https://assethub-polkadot.subscan.io/assets/1337))	1	~12 seconds
Polygon PoS ([USDC](https://polygonscan.com/token/0x3c499c542cef5e3811e1192ce70d8cc03d5c3359))	372	~20 minutes
Solana ([USDC](https://solscan.io/token/EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v), [EURC](https://explorer.solana.com/address/HzwqbKZw8HxMN6bF2yFZNrht3c2iXXzpKcFu7uBEDKtr))	1	~400 milliseconds
Stellar ([USDC](https://stellar.expert/explorer/public/asset/USDC-GA5ZSEJYB37JRC5AVCIA5MOP4RHTM335X2KGX3IHOJAPP5RE34K4KZVN), [EURC](https://stellar.expert/explorer/public/asset/EURC-GDHU6WRG4IEQXM5NZ4BMPKOXHW76MZM4Y2IEMFDVXBSDP6SJY4ITNPP2))	1	~5 seconds
TRON ([USDC](https://tronscan.org/#/token20/TEkxiTehnzSmSe2XqrBj4w32RUN966rdz8))*	19	~1 minute
zkSync ([USDC](https://explorer.zksync.io/address/0x1d17CBcF0D6D143135aE902365D2E5e2A16538D4))**	35	~2 hours
* Effective Feb. 20, 2024 -Circle is discontinuing support. For details, see [Circle is Discontinuing Support for USDC on the TRON blockchain](https://www.circle.com/blog/circle-is-discontinuing-support-for-usdc-on-the-tron-blockchain).

** ~35 Ethereum blocks after the proof for the batch is posted to the L1.

##📘
Hedera

Hedera is built on a hashgraph rather than a blockchain. For this reason, there isn’t a count of confirmations/blocks before Circle determines the transfer to be valid. Instead, this determination is performed on Hedera directly and is then shared back to Circle. Please see the following link to learn more about [Hedera consensus](https://hedera.com/how-it-works).

##📘
Polygon PoS

Circle’s platform requires 372 confirmations for transactions on Polygon PoS before marking them as complete, which takes ~20 minutes. 372 represents 3X the number of confirmations needed to ensure transaction finality beyond a reasonable doubt, based on the largest reorg seen to date.

##📘
Base and OP Mainnet

These Layer 2 (L2) rollup blockchains are based on the OP Stack and maintain their security by periodically batch transferring their transaction data to their L1 blockchain, Ethereum. To ensure finality beyond a reasonable doubt, Circle's platform recognizes transaction finality for these L2 networks after their batched transaction data has settled and achieved 12 confirmations on Ethereum.

You can use L2 block explorers (such as [BaseScan](https://basescan.org) and [Optimistic Etherscan](https://optimistic.etherscan.io/)) to view transactions at any time, including during the time period that elapses while Circle's platform is waiting to recognize transaction finality on its internal systems.

#
Confirmations and Transfer Status
[](#confirmations-and-transfer-status)
As soon as an incoming transfer is included in a block, the API will make it available for you (whether you poll the [get transfers endpoint](/circle-mint/reference/gettransfer) or subscribe to notifications). However, the transfer will have a running status and it will not credit the balance of the associated hosted wallet with the transfer amount until the required number of confirmations has been reached.

At that point the transfer status will change to completed. If you have subscribed to notifications, you will receive a message indicating such status change.

You might want to deem an incoming transfer completed before such a status change and fulfill your business process that depends on that transfer. But note that you will be doing so at your own risk, as you might never actually receive the funds in case there is a reorg before the number of confirmations has been reached.

##🚧
Please note that waiting for confirmations before crediting a wallet only applies for on-chain transfers, where the source is of type blockchain. Transfers between hosted wallets, where the source is of type wallet do not require waiting on confirmations and are credited instantly.

Updated 24 days ago


URL: https://developers.circle.com/circle-mint/docs/supported-chains-and-currencies
Title: Supported Chains and Currencies
Description: undefined
Keywords: undefined

#
Introduction to Chains and Currencies
[](#introduction-to-chains-and-currencies)
If you are not familiar with blockchain concepts, a blockchain (or "chain" for short), is a distributed ledger for recording transactions. Computers across the Internet use a blockchain to synchronize (agree on) a shared transaction history for everyone on the network. You can think of it as a distributed database in which everyone who wants to transact records their actions according to certain rules that ensure nobody is cheating. [Bitcoin](https://en.wikipedia.org/wiki/Bitcoin), [Ethereum](https://en.wikipedia.org/wiki/Ethereum), and [Solana](https://solana.com) are examples of chains.

A chain typically hosts one or more cryptocurrencies (or "crypto" for short) which have value. Cryptocurrencies are often referred to as tokens. Most chains host a native currency, which often is used to enable users to pay for network fees when making transactions.

For example, the [Bitcoin](https://en.wikipedia.org/wiki/Bitcoin) chain hosts the native Bitcoin (BTC) currency. The [Ethereum](https://en.wikipedia.org/wiki/Ethereum) chain hosts the native Ethereum (ETH) currency, but also a variety of tokens that follow a token standard called [ERC-20](https://eips.ethereum.org/EIPS/eip-20), including [USDC](https://developers.circle.com/stablecoins/docs/what-is-usdc) and [EURC](https://circle.com/eurc). Similarly, the [Solana](https://solana.com/) chain hosts the native Solana (SOL) currency, but also a variety of tokens following the [SPL](https://spl.solana.com/) token standard, including [USDC](https://developers.circle.com/stablecoins/docs/what-is-usdc) and [EURC](https://explorer.solana.com/address/HzwqbKZw8HxMN6bF2yFZNrht3c2iXXzpKcFu7uBEDKtr).

#
Supported Chains and Currencies
[](#supported-chains-and-currencies)
The table below lists the chains and currencies currently supported by Circle Mint:

##❗️
Use supported chains and currencies only

Transferring unsupported currencies using the Accounts API may result in a permanent loss of funds. You can verify the token contract details for supported currencies below.

For the Cosmos ecosystem
Circle Mint and Circle APIs only support USDC from Noble. If you transfer USDC from Noble to other appchains via IBC, you must transfer back to Noble via IBC before you make a deposit into your Circle Mint address.

Do not attempt to deposit IBC-transferred USDC from an appchain other than Noble directly to your Circle Mint address, as it may not be recoverable, and could result in a loss of funds.

For the Polkadot ecosystem
Circle Mint and Circle APIs only support USDC from Polkadot Asset Hub. If you transfer USDC from Polkadot Asset Hub to other parachains via XCM, you must transfer back to Polkadot Asset Hub via XCM before you make a deposit into your Circle Mint account.

Do not attempt to deposit XCM-transferred USDC from a parachain other than Polkadot Asset Hub directly to your Circle Mint address, as it may not be recoverable, and could result in a loss of funds.

##
USDC
[](#usdc)
Chain	Currency	API Chain Code	API Currency Code
Algorand	[USDC](https://algoexplorer.io/asset/31566704)	ALGO	USD
Arbitrum	[USDC](https://arbiscan.io/token/0xaf88d065e77c8cC2239327C5EDb3A432268e5831)	ARB	USD
Avalanche C-Chain	[USDC](https://snowtrace.io/token/0xB97EF9Ef8734C71904D8002F8b6Bc66Dd9c48a6E)	AVAX	USD
Base	[USDC](https://basescan.org/token/0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913)	BASE	USD
Celo	[USDC](https://celoscan.io/token/0xcebA9300f2b948710d2653dD7B07f33A8B32118C)	CELO	USD
Ethereum	[USDC](https://etherscan.io/token/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48)	ETH	USD
Flow	[USDC](https://flowscan.org/contract/A.b19436aae4d94622.FiatToken)	FLOW	USD
Hedera	[USDC](https://app.dragonglass.me/hedera/tokens/0.0.456858)	HBAR	USD
NEAR	[USDC](https://nearblocks.io/token/17208628f84f5d6ad33f0da3bbbeb27ffcb398eac501a31bd6ad2011e36133a1)	NEAR	USD
Noble	[USDC](http://mintscan.io/noble/assets)	NOBLE	USD
OP Mainnet	[USDC](https://optimistic.etherscan.io/token/0x0b2c639c533813f4aa9d7837caf62653d097ff85)	OP	USD
Polkadot Asset Hub	[USDC](https://assethub-polkadot.subscan.io/assets/1337)	PAH	USD
Polygon PoS	[USDC](https://polygonscan.com/token/0x3c499c542cef5e3811e1192ce70d8cc03d5c3359)	POLY	USD
Solana	[USDC](https://solscan.io/token/EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v)	SOL	USD
Stellar	[USDC](https://stellar.expert/explorer/public/asset/USDC-GA5ZSEJYB37JRC5AVCIA5MOP4RHTM335X2KGX3IHOJAPP5RE34K4KZVN)	XLM	USD
TRON**	[USDC](https://tronscan.org/#/token20/TEkxiTehnzSmSe2XqrBj4w32RUN966rdz8)	TRX	USD
zkSync	[USDC](https://explorer.zksync.io/address/0x1d17CBcF0D6D143135aE902365D2E5e2A16538D4)	ZKS	USD
** Effective Feb. 20, 2024: Circle is discontinuing support for USDC on TRON. For details, see [Circle is Discontinuing Support for USDC on the TRON blockchain](https://www.circle.com/blog/circle-is-discontinuing-support-for-usdc-on-the-tron-blockchain).

##
EURC
[](#eurc)
Chain	Currency	API Chain Code	API Currency Code
Avalanche C-Chain	[EURC](https://snowtrace.io/token/0xc891eb4cbdeff6e073e859e987815ed1505c2acd)	AVAX	EUR
Ethereum	[EURC](https://etherscan.io/token/0x1aBaEA1f7C830bD89Acc67eC4af516284b1bC33c)	ETH	EUR
Solana	[EURC](https://explorer.solana.com/address/HzwqbKZw8HxMN6bF2yFZNrht3c2iXXzpKcFu7uBEDKtr)	SOL	EUR
Stellar	[EURC](https://stellar.expert/explorer/public/asset/EURC-GDHU6WRG4IEQXM5NZ4BMPKOXHW76MZM4Y2IEMFDVXBSDP6SJY4ITNPP2)	XLM	EUR
##
Additional Supported Assets
[](#additional-supported-assets)
Chain	Currency	API Chain Code	API Currency Code
Bitcoin	BTC	BTC	BTC
Ethereum	ETH	ETH	ETH
Polygon PoS	MATIC	POLY	MATIC
#
Using Chains and Currencies on the Wallets API
[](#using-chains-and-currencies-on-the-wallets-api)
Any time you refer to a currency in the context of an Circle Mint API operation, you will utilize a currency and a chain pair. As an example, to [create a USDC transfer](/circle-mint/reference/createtransfer) on the Ethereum blockchain, you will specify the USD currency on the ETH chain. Similarly, to [create a USDC transfer](/circle-mint/reference/createtransfer) on the Algorand blockchain, you will specify the USD currency on the ALGO chain.

When referring to balances, you only need to refer to the currency (USD, BTC, ETH), because the value of the currency for a Circle-hosted value is independent of the chain - and may sometimes be available on, and transferred across multiple chains.

Updated 24 days ago


URL: https://developers.circle.com/circle-mint/docs/wallets-addresses-and-transfers
Title: Wallets, Addresses and Transfers
Description: undefined
Keywords: undefined

#
Wallets
[](#wallets)
A wallet is the fundamental store of funds across the Circle platform. Think of wallets like bank accounts. We often refer to wallets on the Circle platform as "hosted wallets".

When you signup for Circle Mint, you automatically get a master wallet (which you can query using the [get configuration endpoint](/circle-mint/reference/getaccountconfig)). You can subsequently create as many additional wallets as you wish using the [create wallet endpoint](/circle-mint/reference/createwallet).

Wallets have balances. Each wallet hosted with Circle can hold balances in any of our supported chains. See supported [chains and currencies](/circle-mint/docs/supported-chains-and-currencies) for more detail.

Wallets are the destination for settlement of payments processed via the Payments API. You are able to transfer funds across hosted wallets. You are also able send funds from a wallet to external blockchain destinations. Similarly, you can have a wallet receive funds from external blockchain sources.

#
Wallet Addresses
[](#wallet-addresses)
An address is an identifier on a given blockchain that can be used to receive cryptocurrency funds. It is typically a string of alphanumeric characters, such as 0x8381470ED67C3802402dbbFa0058E8871F017A6F.

Since the wallets you control are capable of receiving funds from external blockchain sources, you are able to generate an address for your wallets by using the [create address endpoint](/circle-mint/reference/generateaddress). Blockchain transfers to that address will eventually credit your wallet balance ([read more about confirmations here](/circle-mint/docs/blockchain-confirmations)).

Different blockchains, like [Bitcoin](https://en.wikipedia.org/wiki/Bitcoin) and [Ethereum](https://en.wikipedia.org/wiki/Ethereum), have different address formats. For technical reasons, it may not be possible to receive all tokens that exist on a given blockchain using the same address.

As a result, when you generate a new address for one of your wallets using the Wallets API you will specify both the chain and the currency you wish to receive using that address.

You may generate as many addresses as you wish for a given wallet by using the [create address endpoint](/circle-mint/reference/generateaddress).

##🚧
When communicating one of your wallet addresses to an external party, it is extremely important to preserve the exact format and capitalization of the address. Otherwise funds transferred by the external party may end up somewhere else.

##🚧
Addresses generated for your master wallet using the Accounts API may appear within the Circle Mint dashboard’s deposit flow. If you are using addresses appended to your master wallet for third party deposits, it is extremely important to embed logic to avoid misdirecting funds.

##❗️
It is extremely important to ensure that only the appropriate currency on the appropriate chain gets sent to your wallet addresses. Otherwise transferred funds may be permanently lost.

#
Wallet Transfers
[](#wallet-transfers)
A wallet transfer is an operation that transfers funds (a) from an external blockchain address to a Circle hosted wallet, (b) between two Circle hosted wallets, or (c) from a Circle hosted wallet to an external blockchain address.

Wallet transfers are the main mechanism for moving money in the Accounts API. You can create wallet transfers by using the [create transfer endpoint](/circle-mint/reference/createtransfer).

##📘
When you transfer external funds to an address associated with a Circle hosted wallet and the wallet is successfully credited, Circle's platform may subsequently move the underlying cryptocurrency to another address. This is normal behavior and your wallet maintains the proper balance irrespective of any such moves. You cannot accurately determine the balance of a Circle hosted wallet by inspecting its blockchain addresses. Instead, you must use the Circle API.

Wallet transfers between two wallets are instantaneous and free. Wallet transfers from a wallet to an external blockchain address will take some time while the blockchain processes your transaction, and may incur fees in the future.

##❗️
Use supported chains and currencies only

Transferring unsupported currencies using the Accounts API may result in a permanent loss of funds. You can verify the token contract details for supported currencies [here](/circle-mint/docs/supported-chains-and-currencies).

Updated 4 months ago

-
[Send funds externally](/circle-mint/docs/send-funds-externally)
-
[Receive external funds](/circle-mint/docs/receive-external-funds)
Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/getting-started-with-the-circle-core-api
Title: Core Functionality
Description: undefined
Keywords: undefined

The Core Functionality API allows you to manage your Circle Mint Account in a programmatic way by automating the minting and redemption of USDC. Deposit traditional money from 185 countries and seamlessly convert it into "[digital currency dollars": USDC](https://developers.circle.com/stablecoins/docs/what-is-usdc). You can then use USDC for everyday payments and treasury flows.

The Core Functionality API empowers you to do the following:

-
Transfer USDC and EURC into your Circle Mint account across supported blockchains. See [Quickstart: Deposit via Blockchain Wallet](/circle-mint/docs/quickstart-deposit-via-blockchain-wallet).

-
Transfer USDC and EURC out of your Circle Mint account across supported blockchains. See [Quickstart: Withdraw via Blockchain Wallet](/circle-mint/docs/quickstart-via-blockchain-wallet).

-
Make deposits from your business bank account to seamlessly convert traditional fiat into digital currency. See [Quickstart: Deposit via Funds Transfer](/circle-mint/docs/quickstart-deposit-via-funds-transfer).

-
Make withdrawals into your business bank account to seamlessly convert digital currency into traditional fiat. See [Quickstart: Withdraw to Bank](/circle-mint/docs/quickstart-withdraw-via-blockchain-wallet).

##
Additional APIs
[](#additional-apis)
Circle Mint customers can also access APIs for accepting crypto deposits, sending crypto payouts, and managing your sub-account infrastructure. Please note these APIs are available at an additional cost and require further solutioning with our team in order to best meet your needs.

##
Ready for the next step?
[](#ready-for-the-next-step)
After experimenting with Circle Mint, you’ll want to start building test integrations in sandbox prior to moving into production. Start by [applying for a Circle Mint account](https://www.circle.com/en/circle-mint). We'll be happy to walk you through the next steps.

Updated about 2 months ago

-
[Getting Started with Circle APIs](/circle-mint/docs/getting-started-with-the-circle-apis)
-
[Testing connectivity and API keys](/circle-mint/docs/testing-connectivity-and-api-keys)
-
[Sandbox & Production Environments](/circle-mint/docs/circle-apis-production-sandbox-environments)
-
[Getting Started with the Circle Payments API](/circle-mint/docs/getting-started-with-the-circle-payments-api)

URL: https://developers.circle.com/circle-mint/docs/getting-started-with-the-circle-apis
Title: Getting Started
Description: undefined
Keywords: undefined

Circle APIs are a set of functions and procedures that allow you to use USDC for payments, payouts, and transfers. They enable your applications to connect to banks and digital wallets and enable global payments via USDC. By integrating with our APIs to automate minting and redemption, you can provide stablecoin access directly to your users through your own app or website.

To ensure success, please note the following:

- API requests without authentication will fail.
- All API requests must be made over HTTPS.
- Keep API keys secure at all times. Ensure your API key is always stored securely and never share it or record it in a medium that might be publicly accessible such as client-side code, public repositories, etc.
#
API key authentication
[](#api-key-authentication)
Set the API key in the Authorization header of the request sent from your backend server using the format Bearer secret-key-value.

API Authentication

Authorization: Bearer YOUR_API_KEY
#
API key in Sandbox
[](#api-key-in-sandbox)
By using the Circle [sandbox environment](/circle-mint/docs/circle-apis-production-sandbox-environments), you can quickly familiarize yourself with the Circle API request and response formats and build prototypes. The API key you use in sandbox will be different than the one you use in production.

If you don't yet have an API key in the sandbox environment, take a few moments to [create a sandbox account](https://app-sandbox.circle.com).


[
Create a sandbox account
](https://app-sandbox.circle.com)
After you create your sandbox account and generate your API key, follow the links below to continue exploring Circle APIs.

Updated 5 months ago

-
[Authentication](/circle-mint/docs/authentication)
-
[API Keys](/circle-mint/docs/api-keys)
-
[Sandbox & Production Environments](/circle-mint/docs/circle-apis-production-sandbox-environments)
-
[Circle Payments API - Quickstart](/circle-mint/docs/circle-payments-api-quickstart)
Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/sandbox-to-production-transition-guide
Title: Sandbox to Production Transition Guide
Description: undefined
Keywords: undefined

If you've completed your API integration in Circle’s sandbox, it's time to move your site into production. Because the code is largely the same between sandbox and production, moving into the production environment is a fairly straightforward process. Follow the steps below and take note of any additional information provided.

To ensure success, please note the following:

- If you have any questions about the differences listed below, reach out to your Circle solutions engineer or our [customer support team](mailto:customer-support@circle.com) for more information.
- Note that to obtain Production API keys, you need to apply for a [Circle Mint account](https://support.usdc.circle.com/hc/en-us/articles/4858619585044-Circle-Account-application-walk-through).
##
1. Move your code to production.
[](#1-move-your-code-to-production)
Take your sandbox code into the production environment. There are a couple ways to do this based on your needs:

- Basic users who only need Circle’s core functionality can use the same code documents they’ve been using in sandbox and convert them directly into production code using the steps below.
- Advanced users using our full suite of APIs will probably want to continue using the sandbox version of their site in parallel for continued development, testing, and troubleshooting. These users should clone their sandbox code and edit the copy to bring it into production.
##
2. Replace sandbox URLs with production URLs.
[](#2-replace-sandbox-urls-with-production-urls)
When you’re transitioning from Sandbox to Production, you’ll need to switch to the Production base URL for your requests.

Sandbox URL: <https://api-sandbox.circle.com>
Production URL: <https://api.circle.com>

In some cases, this will be as simple as performing a “find and replace” function with a text editor; in others it will be more complex depending on your approach. Consult your engineering team if you have any questions.

##
3. Replace sandbox API keys with production API keys.
[](#3-replace-sandbox-api-keys-with-production-api-keys)
Now that you’re ready to move to production, navigate to the API key tab within the Circle Mint developer dashboard. Create an API key and name it, and assign it a list of IPs permitted to originate requests. After creating your API key, you can copy and paste it as needed.

Security: Because the production API key allows you to work with real, live funds within your Circle Mint account, the key must be provided and managed by Circle. (You can’t regenerate it from your Circle Mint account.) For this reason, your production API key must be treated with appropriate security protocols. Please consult your company’s security team if you have questions.

##📘
Circle disables public access to all APIs by default. If you haven’t already connected with a Circle representative regarding using Circle's APIs, please reach out to [customer-support@circle.com](mailto:customer-support@circle.com).

##
4. Set up your system to originate requests from IPv4 addresses allow-list. [OPTIONAL]
[](#4-set-up-your-system-to-originate-requests-from-ipv4-addresses-allow-list-optional)
If you have set up an IP allowlist, Circle only accepts requests using your Production API key that originate from an IP address on the list.

- Set up your system to send production calls only from these addresses.
- Double-check that these static addresses match the ones you sent to Circle.
##
5. Check that your API Key Roles are up to date.
[](#5-check-that-your-api-key-roles-are-up-to-date)
While working in sandbox, your sandbox API key has allowed you to make calls to any Circle endpoint for testing purposes. In production, you’ll be restricted to the functionality—and endpoint access—that you are specifically solutioned for in your Solution Description and Flow of Funds documents. If you attempt to make API calls to an endpoint you aren’t solutioned for, you’ll receive a 403 response.

- Test your calls. Try out each of the API calls you created in sandbox to make sure that they still work in production. For instance, your get balances call from sandbox should still provide balances in production as long as you have updated the URL.
- Update as necessary. If you run into error codes, or if your needs have changed and you need additional functionality, contact your Circle representative. They can update your Solution Description and Flow of Funds agreements to enable the needed functionality.
##
6. Allow for actual production settlement times.
[](#6-allow-for-actual-production-settlement-times)
In the sandbox environment, settlement times (the time it takes to process a transaction) are kept as low as possible to enable you to easily test a variety of behaviors. Because they reflect real-world transactions, production settlement times will invariably be longer (see table below).

- Test actual settlement times in production.
- Take into account your own operations and whether you want to perform certain actions (such as releasing goods) after confirmation or after settlement.
- Update your interface to manage users’ expectations.
###
Crypto Payments and Payouts
[](#crypto-payments-and-payouts)
- See [blockchain confirmations](/circle-mint/docs/blockchain-confirmations).
###
Accounts
[](#accounts)
- Wallet creation: 2-5 seconds
- Wallet-to-Wallet transfers: 2-5 seconds
##
7. Anticipate transaction fees.
[](#7-anticipate-transaction-fees)
Because sandbox does not reflect your client agreement with Circle, fees in the sandbox environment may not always reflect the fees you’ll be charged in production.

- See your contract with Circle to understand what fees you’ll see when you initiate transactions in the production environment.
- Determine actual fees by testing various transactions in production.
- Update your interface if you will be passing along these fees to your customers.
Updated 5 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/quickstart-deposit-via-funds-transfer
Title: Quickstart: Deposit via Funds Transfer
Description: undefined
Keywords: undefined

Sending funds to your Circle Account from an external bank account is one of the basic primitives (building blocks) enabled by the APIs. This quickstart walks through depositing USDC USD from an external bank account. You can follow the same steps when sending EURC EUR as well.

##
1. Get an API key
[](#1-get-an-api-key)
Circle's APIs use [API keys](https://developers.circle.com/circle-mint/docs/api-keys) as the mechanism to authenticate client requests. The API key must be set in the Authorization header of the request sent from your backend server. The format of the header is Bearer secret-key-value.

To obtain an API key for the sandbox environment, simply [create an account](https://app.circle.com/get-started/create-account) and generate a new key in settings - it only takes a few seconds.

[GET AN API KEY](https://app-sandbox.circle.com/)

Once you have generated your API key, record it in a secure place.

##
2. Link a bank account
[](#2-link-a-bank-account)
To send account funds from an external bank account, you will first need to add the bank account you will be using to your Circle Mint Account. [https://developers.circle.com/circle-mint/reference/createbusinesswireaccount](https://developers.circle.com/circle-mint/reference/createbusinesswireaccount)

Add [Create A Wire Bank Account](https://developers.circle.com/circle-mint/reference/createbusinesswireaccount) walkthrough

NOTE - the parameters listed below are slightly different for US Bank accounts, Non-US bank accounts - IBAN supported and Non-US bank accounts - non-IBAN supported. Please see the above link for details.

REQUEST

cURL

curl --request POST \\
--url https\://api-sandbox.circle.com/v1/businessAccount/banks/wires \\
--header 'accept: application/json' \\
--header 'authorization: Bearer SAND\_API\_KEY:1f2ae4652d18814652aa8b9748f66d4b:e28164c99b17fdf725aba33fe52ec3a7' \\
--header 'content-type: application/json' \\
--data '

{
  "billingDetails": {
    "name": "Satoshi Nakamoto",
    "city": "Boston",
    "country": "US",
    "line1": "100 Money Street",
    "district": "MA",
    "postalCode": "01234"
  },
  "bankAddress": {
    "bankName": "SAN FRANCISCO",
    "city": "SAN FRANCISCO",
    "country": "US",
    "line1": "100 Money Street",
    "district": "CA"
  },
  "idempotencyKey": "ba943ff1-ca16-49b2-ba55-1057e70ca5c7",
  "accountNumber": "12340010",
  "routingNumber": "121000248"
}

'
RESPONSE

JSON

{
  "data": {
    "id": "9d1fa351-b24d-442a-8aa5-e717db1ed636",
    "status": "pending",
    "description": "WELLS FARGO BANK, NA ****0010",
    "trackingRef": "CIR2GKYL4B",
    "fingerprint": "a9a71b77-d83d-4fbc-997f-41a33550c594",
    "virtualAccountEnabled": true,
    "billingDetails": {
      "name": "Satoshi Nakamoto",
      "line1": "100 Money Street",
      "city": "Boston",
      "postalCode": "01234",
      "district": "MA",
      "country": "US"
    },
    "bankAddress": {
      "bankName": "WELLS FARGO BANK, NA",
      "line1": "100 Money Street",
      "city": "SAN FRANCISCO",
      "district": "CA",
      "country": "US"
    },
    "createDate": "2023-11-04T20:02:21.062Z",
    "updateDate": "2023-11-04T20:02:21.062Z"
  }
}
##
3. Get wire instructions
[](#3-get-wire-instructions)
You can easily retrieve all necessary details to make a deposit from your bank account by using the [GET Wire Instructions endpoint](https://developers.circle.com/circle-mint/reference/getbusinesswireaccountinstructions). It will return the beneficiary details, bank details, and a confirmation of the currency.

REQUEST

Replace ${YOUR\_API\_KEY} with your API key.

Replace ${TRANSFER\_ID} with the id of the transfer you created earlier.

cURL

curl -H "Authorization: Bearer ${YOUR\_API\_KEY}" \\

-X GET https\://api-sandbox.circle.com/v1/businessAccount/banks/wires/b3d9d2d5-4c12-4946-a09d-953e82fae2b0/instructions?currency=USD
RESPONSE

The response should look like this:

JSON

{
  "data": {
    "trackingRef": "CIR22FEP33",
    "beneficiary": {
      "name": "CIRCLE INTERNET FINANCIAL INC",
      "address1": "1 MAIN STREET",
      "address2": "SUITE 1"
    },
    "virtualAccountEnabled": true,
    "beneficiaryBank": {
      "name": "CRYPTO BANK",
      "address": "1 MONEY STREET",
      "city": "NEW YORK",
      "postalCode": "1001",
      "country": "US",
      "swiftCode": "CRYPTO99",
      "routingNumber": "999999999",
      "accountNumber": "123815146304",
      "currency": "USD"
    }
  }
}
##
4. Deposit to your account
[](#4-deposit-to-your-account)
Wire deposits can only be initiated from your respective bank account so this part of the deposit process will take place outside of Circle’s API.

Using the wire deposit details in the previous step, you will send funds from your bank account to your Circle Mint Account.

You must confirm the wire details are correct to avoid any delays or bank returns.

##
5. Ready for the next step?
[](#5-ready-for-the-next-step)
After experimenting with our APIs, you’ll want to start building test integrations in sandbox prior to moving into production. Start by [applying for a Circle Mint account](https://www.circle.com/en/circle-mint). We'll be happy to walk you through the next steps.

Updated 4 months ago


URL: https://developers.circle.com/circle-mint/docs/send-funds-externally
Title: Send Account Funds Externally
Description: undefined
Keywords: undefined

Sending funds from your Circle hosted wallets to external blockchain wallets is one of the most basic primitives of the Circle Accounts API.

#
1. Get an API key
[](#1-get-an-api-key)
The Accounts API uses [API keys](/circle-mint/docs/api-keys) as the mechanism to authenticate client requests. The API key must be set in the Authorization header of the request sent from your backend server. The format of the header is Bearer secret-key-value.

To obtain an API key for the sandbox environment, simply create an account and generate a new key in settings - it only takes a few seconds.


[
Get an API key
](https://app-sandbox.circle.com)
Once you have generated your API key, record it in a secure place.

#
2. Fund Your Wallet
[](#2-fund-your-wallet)
If you haven't already, make sure you [fund your wallet for testing](/circle-mint/docs/funding-test-wallets).

#
3. Send Funds
[](#3-send-funds)
To send funds externally, you need to start by choosing the hosted wallet you will send from (source) and the blockchain address to send to (destination).

You can use your [master wallet](/circle-mint/docs/your-master-wallet) as the source wallet. If you haven't noted your master wallet id yet, use [this guide](/circle-mint/docs/your-master-wallet) to obtain it.

##📘
USDC, EURC, BTC, and ETH on Testing Networks

The [sandbox environment](/circle-mint/docs/circle-apis-production-sandbox-environments) is connected to the [USDC testing networks](https://developers.circle.com/stablecoins/docs/usdc-on-test-networks) and [EURC testing]networks](doc:eurc-on-testnet) of the blockchains where [supported](/circle-mint/docs/supported-chains-and-currencies).

For BTC and ETH, we use testnet and Goerli, respectively.

The destination blockchain address you use has to be a valid address on the desired testing network. Refer to [Supported Chains and Currencies](/circle-mint/docs/supported-chains-and-currencies) to see each chains test network that we use.

For the purpose of this guide, we will transfer funds to the deposit address of the [USDC faucet service on Ethereum Goerli](https://faucet.circle.com/). That address is 0x493A9869E3B5f846f72267ab19B76e9bf99d51b1.

##🚧
You can transfer any amount you want, provided your source wallet has sufficient balance.

To send funds externally, you will use the [create transfer endpoint](/circle-mint/reference/createtransfer).

Since the Accounts API [supports multiple chains and currencies](/circle-mint/docs/supported-chains-and-currencies), you have to specify the currency and chain you want to utilize. You can create a USDC transfer to an Ethereum address by using the command below.

Terminal

# Replace ${YOUR_API_KEY} with your API key
# Replace ${YOUR_WALLET_ID} with your master wallet id
curl -H 'Accept: application/json' \
  -H 'Content-type: application/json' \
  -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X POST https://api-sandbox.circle.com/v1/transfers \
  --data '{"idempotencyKey": "4ca72ac7-5217-49bb-bbe6-e8dcb4c53c25", "source": {"type": "wallet", "id": "${YOUR_WALLET_ID}"}, "destination": {"type": "blockchain", "address": "0x493A9869E3B5f846f72267ab19B76e9bf99d51b1", "chain": "ETH"}, "amount": {"amount": "0.10", "currency": "USD"}}'
Similarly, you can create a USDC transfer to an Algorand address by using the command below.

Terminal

# Replace ${YOUR_API_KEY} with your API key
# Replace ${YOUR_WALLET_ID} with your master wallet id
curl -H 'Accept: application/json' \
  -H 'Content-type: application/json' \
  -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X POST https://api-sandbox.circle.com/v1/transfers \
  --data '{"idempotencyKey": "4ca72ac7-5217-49bb-bbe6-e8dcb4c53c25", "source": {"type": "wallet", "id": "${YOUR_WALLET_ID}"}, "destination": {"type": "blockchain", "address": "BL27TI3CYMFX5URLYNBJJZGV4ABRYJJHBMLRR5OYSVPUVDIUAF66IR4COA", "chain": "ALGO"}, "amount": {"amount": "0.10", "currency": "USD"}}'
In either case, you should receive a response like the below.

Wallet Transfer Response

{
  "data":{
    "id":"a45e27c1-5191-4202-9fcd-ac120da6a522",
    "source":{
      "type":"wallet",
      "id":"0000000001"
    },
    "destination":{
      "type":"blockchain",
      "address":"0x493A9869E3B5f846f72267ab19B76e9bf99d51b1",
      "chain":"ETH"
    },
    "amount":{
      "amount":"0.10",
      "currency":"USD"
    },
    "status":"pending",
    "createDate":"2020-04-21T09:38:31.300Z"
  }
}
#
4. Check the Status of the Transfer
[](#4-check-the-status-of-the-transfer)
You can use the [get transfer endpoint](/circle-mint/reference/gettransfer) to retrieve details about the status of the transaction. You can use it as in the command below.

cURL

# Replace ${YOUR_API_KEY} with your API key
# Replace ${TRANSFER_ID} with the id of the transfer you created earlier
curl -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X GET https://api-sandbox.circle.com/v1/transfers/${TRANSFER_ID}
You will receive a response like below.

Get Transfer Response

{
  "data":{
    "id":"a45e27c1-5191-4202-9fcd-ac120da6a522",
    "source":{
      "type":"wallet",
      "id":"0000000001"
    },
    "destination":{
      "type":"blockchain",
      "address":"0x493A9869E3B5f846f72267ab19B76e9bf99d51b1",
      "chain":"ETH"
    },
    "amount":{
      "amount":"0.10",
      "currency":"USD"
    },
    "transactionHash":"0xae7ec88780eb946528a3e443373de4c5f82d5311cef0788c72b835b52048b8b3",
    "status":"running",
    "createDate":"2020-04-21T09:38:31.300Z"
  }
}
The very first state transition for a transfer sets the status to pending and the transactionHash to null - at that point Circle has just started processing the on-chain send.

At this point you can consider the transaction complete because it has been successfully broadcasted to the network. Circle's systems will continue to track the transfer for the appropriate number of confirmations and its status will at that point change to complete. Except for a few specialized applications you do not have to worry about waiting for the complete state - that's important when receiving external transfers. You can [read more about block confirmations here](/circle-mint/docs/blockchain-confirmations).

🎉 Congratulations! You have successfully sent cryptocurrency using the Circle Accounts API.

Make sure you check our quickstart guide on [receiving external funds](/circle-mint/docs/receive-external-funds).

#
5. Ready for the next step?
[](#5-ready-for-the-next-step)
If you are in advanced stages of experimenting with our APIs and want to plan moving to production, please start by [applying for a Circle Mint account](https://www.circle.com/en/circle-mint) and subsequently [reach out to sales](mailto:sales@circle.com). We'll be happy to walk you through to the next steps.

We can't wait to see what you are going to build!

Updated 5 months ago

-
[✨ Receive External Funds](/circle-mint/docs/receive-external-funds)
-
[✨ Swap USDC Across Blockchains](/circle-mint/docs/swap-usdc-across-blockchains)
-
[Funding Test Wallets](/circle-mint/docs/funding-test-wallets)
-
[Supported Chains and Currencies](/circle-mint/docs/supported-chains-and-currencies)
-
[Wallets, Addresses and Transfers](/circle-mint/docs/wallets-addresses-and-transfers)
-
[Blockchain Confirmations](/circle-mint/docs/blockchain-confirmations)
Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/authentication
Title: Authentication
Description: undefined
Keywords: undefined

API keys are unique data strings used to authenticate a user and enable access to privileged operations on Circle APIs. All Circle APIs use API keys as the mechanism to authenticate client requests. Your API key should be kept confidential and secure at all times.

- Authentication is required for all API requests; without it, the requests will fail.
- All API requests must be made over HTTPS.
- To obtain the API key, visit the “Developer” section in sandbox and click “get new API key.” Set the key in the Authorization header of the request you send from your backend server. Use the header format:
Bearer YOUR_API_KEY.
#
API Authentication
[](#api-authentication)
API Authentication

Authorization: Bearer YOUR_API_KEY
API requests without authentication will fail. It is also important to note that all API requests must be made over HTTPS.

#
Testing Authentication
[](#testing-authentication)
To verify that your API key is correctly set up, run the command below, which causes your code to hit a read endpoint (in this case, the [configuration endpoint](/circle-mint/reference/getaccountconfig)).

cURL
JavaScript

# Replace ${YOUR_API_KEY} with your API key
curl -H 'Accept: application/json' \
  -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X GET --url https://api-sandbox.circle.com/v1/configuration
##👍
Successful Response


{"data":{"payments":{"masterWalletId":"1234567890"}}}
##❗️
Error Response


{"code":401,"message":"Malformed authorization. Are the credentials properly encoded?"}
Updated 5 months ago

-
[API Keys](/circle-mint/docs/api-keys)
-
[Sandbox & Production Environments](/circle-mint/docs/circle-apis-production-sandbox-environments)

URL: https://developers.circle.com/circle-mint/docs/circle-sdks
Title: Circle SDKs
Description: undefined
Keywords: undefined

Circle’s SDKs are developer toolkits that enable you to integrate your code with Circle APIs. Because our SDKs are open source, users like you can help us to improve them. If you find bugs or missing features, we encourage you to file an issue or contribute back to the [node-sdk](https://github.com/circlefin/circle-nodejs-sdk).

Note: This page contains code snippets and examples that can be run as is or incorporated into your apps.

Circle offers SDKs for Typescript, Java and Python. Java and Python SDKs are currently in beta.

##
Installing the SDK
[](#installing-the-sdk)
You can run one of the two commands below from your project directory to install the SDK.

Node
Java (Beta)
Python (Beta)

npm install @circle-fin/circle-sdk --save
# or
yarn add @circle-fin/circle-sdk
##
Configuration
[](#configuration)
TypeScript
Java (Beta)
Python (Beta)

import { Circle, CircleEnvironments } from "@circle-fin/circle-sdk";
 
const circle = new Circle(
 "<your-api-key>",
 CircleEnvironments.sandbox // API base url
);
##
List balances
[](#list-balances)
TypeScript
Java (Beta)
Python (Beta)

async function listBalances() {
 const balancesRes = await circle.balances.listBalances();
}
##
Create a crypto payment
[](#create-a-crypto-payment)
TypeScript
Java (Beta)
Python (Beta)

async function createCryptoPayment() {
 const createCryptoPaymentRes =
   await circle.paymentIntents.createPaymentIntent({
     idempotencyKey: "5c6e9b91-6563-47ec-8c6d-0ce1103c50b3",
     amount: {
       amount: "3.14",
       currency: "USD",
     },
     settlementCurrency: "USD",
     paymentMethods: [
       {
         chain: "ETH",
         type: "blockchain",
       },
     ],
   });
}
##
Get a crypto payment
[](#get-a-crypto-payment)
TypeScript
Java (Beta)
Python (Beta)

async function getCryptoPayment(id: string) {
 const cryptoPayment = await circle.paymentIntents.getPaymentIntent(id);
}
Updated 5 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/circle-apis-notifications-quickstart
Title: Notifications Quickstart
Description: undefined
Keywords: undefined

Circle notifications will inform you every time the status of a resource changes, such as changes in payment status. Notifications can be accessed by setting up a notification subscriber endpoint on your end. This quickstart guide shows how to set up notifications for the Circle APIs. Follow this to configure a subscriber endpoint that sends a notification each time the resource status changes.

Note: This guide addresses the payment resource, but is applicable to any of the resources mentioned in [Circle API Notifications](/circle-mint/docs/notifications-data-models).

##
1. Expose a Subscriber Endpoint
[](#1-expose-a-subscriber-endpoint)
To receive notifications on changes in resource status, you must expose a publicly accessible subscriber endpoint on your side. The endpoint must handle both HEAD and POST requests over HTTPS.

To expose an endpoint for testing, you can use [webhook.site](https://webhook.site/) to inspect, test and automate incoming HTTPS requests or e-mails directly in the web browser.

##💡
Tip:

When you visit [webhook.site](https://webhook.site/) for the first time, you should see a status message that looks similar to the following:

Your unique URL (Please copy it from here, not from the address bar!) <https://webhook.site/83fa21a0-f00a-4673-bb50-bcf62c78b1f7>

Navigate to [webhook.site]() and record the value of the URL shown as Your unique URL:


In the example above, the unique URL is:https://webhook.site/83fa21a0-f00a-4673-bb50-bcf62c78b1f7. Use the public-facing URL you receive as you progress throughout this guide.

##
2. Subscribe to Status Notifications
[](#2-subscribe-to-status-notifications)
Now that you have a publicly accessible endpoint, you need to register your endpoint as a subscriber to webhook notifications by doing the following:

- Navigate to Developer > Subscriptions in your Circle Mint account and click Add Subscription.

- Enter your endpoint URL from above. It will be similar to the earlier example: https://webhook.site/83fa21a0-f00a-4673-bb50-bcf62c78b1f7.
- Click Add Subscription:

- You should receive two requests on your local server shell that confirm the subscription with a body similar to the following:
JSON

{
  "Type" : "SubscriptionConfirmation",
  "MessageId" : "ddbdcdcf-d36a-45b5-927c-da25b9b009ae",
  "Token" : "2336412f37fb687f5d51e6e2425f004aed7b7526d5fae41bc257a0d80532a6820258bf77eb25b90453b863450713a2a5a4250696d725a306ef39962b5b543752c9003e0841c0e61253fd6c517a94edebe44f36c5fe4ba131c8ea5f6f42a43f97f6e1865505e2f29f79a62f89e18f97e03a0dd5d982a7578c8d6e21154163f2d6aae523cff25557f9bc21b2503d413006",
  "TopicArn" : "arn:aws:sns:us-west-2:908968368384:sandbox_platform-notifications-topic",
  "Message" : "You have chosen to subscribe to the topic arn:aws:sns:us-west-2:908968368384:sandbox_platform-notifications-topic.\nTo confirm the subscription, visit the SubscribeURL included in this message.",
  "SubscribeURL" : "https://sns.us-west-2.amazonaws.com/?Action=ConfirmSubscription&TopicArn=arn:aws:sns:us-west-2:908968368384:sandbox_platform-notifications-topic&Token=2336412f37fb687f5d51e6e2425f004aed7b7526d5fae41bc257a0d80532a6820258bf77eb25b90453b863450713a2a5a4250696d725a306ef39962b5b543752c9003e0841c0e61253fd6c517a94edebe44f36c5fe4ba131c8ea5f6f42a43f97f6e1865505e2f29f79a62f89e18f97e03a0dd5d982a7578c8d6e21154163f2d6aae523cff25557f9bc21b2503d413006",
  "Timestamp" : "2020-04-11T20:50:16.324Z",
  "SignatureVersion" : "1",
  "Signature" : "kBr9z/ysQrr0ldowHY4lThkOA+dwyjcsyx7NwkbTkgEKG4N61BSSEA+43aYQEB/Ml09hclybvyjyRKWYOjaxQgbUXWmyWrCQ7vY93WYhuGvOqZxAMPiDiILxLs6/KtOxneKVvzfpK4abLrYyTTA+z/dQ52h9L8eoiSKSW81e4clfYBTJkGmuAPKFC08FvEAVT89VikPp68mBf4CctPv3Em0b4J1VvDhAB21B2LekgUmwUE0aE7fUbsF3XsKGQd/fDshLOJasQEuXSqdB5X7LITBA8r24FY+wCjwm8oR3VI9IMy21fUC6wMgoFIVZHW1KxzpEkMCSe7R1ySdNIru8SQ==",
  "SigningCertURL" : "https://sns.us-west-2.amazonaws.com/SimpleNotificationService-a86cb10b4e1f29c941702d737128f7b6.pem"
}
##📘
Note:

The response body above is in a plain text format, so be sure you're not expecting a JSON content type.

- To complete the subscription process, you will need to visit the SubscribeURL from both responses. Until you make a request to this URL, no additional messages will be sent to the endpoint.
- Once you have visited both SubscribeURLs, your subscription should enter the COMPLETE status to indicate it is ready to use:
##💡
Tip:

If your subscription is stuck in the PENDING (unconfirmed) state, you will need to reach out to [customer-support@circle.com](mailto:customer-support@circle.com) to remove it.


You now have a sample local environment ready to receive notifications!

##
3. Initiate a Transfer to Receive a Notification
[](#3-initiate-a-transfer-to-receive-a-notification)
To observe a notification message of a transfer resource, follow the [Quickstart Guide](/circle-mint/docs/send-funds-externally) on sending funds externally with the Accounts API.

Once you successfully initiate a transfer, you should see a notification message on your local server shell that looks similar to the following:
Sample Transfer Notification:

JSON

{
  "Type": "Notification",
  "MessageId": "c2fff345-2ff3-5f77-9adb-ae0b05098252",
  "TopicArn": "arn:aws:sns:us-east-1:908968368384:sandbox_platform-notifications-topic",
  "Message": "{\"clientId\":\"2928df09-0b4e-43d7-93cf-7946aad24945\",\"notificationType\":\"transfers\",\"version\":1,\"customAttributes\":{\"clientId\":\"2928df09-0b4e-43d7-93cf-7946aad24945\"},\"transfer\":{\"id\":\"47314bd0-8fe5-4298-86af-ee1ffb9183d9\",\"source\":{\"type\":\"wallet\",\"id\":\"1000037212\"},\"destination\":{\"type\":\"blockchain\",\"address\":\"0x493A9869E3B5f846f72267ab19B76e9bf99d51b1\",\"chain\":\"ETH\"},\"amount\":{\"amount\":\"3.14\",\"currency\":\"USD\"},\"status\":\"pending\",\"createDate\":\"2023-08-10T00:00:58.690Z\"}}",
  "Timestamp": "2023-08-10T00:00:58.891Z",
  "SignatureVersion": "1",
  "Signature": "FPOOJhW0QVkt4NoQFlDtym5g/DYB9M9SBuUXGgH9NNJzzrQ41Xl8LZxHXOZ4r4Qskr2hUKxB1grQ3VAuvhSMP41F/C92Dr8trUSchdEaUTsaGh/y72KhnVqg2QJWAMgC6joeF0v6WgOnzZq/3aBL/GNWAz0wms3PI2wo74gfoCtNAheyAak19rk+tLPyA2/dHf2V9Pd9twV+dorrUEa/kEnL/WGca4TQZsYencRb692dyBVskTmOR9f5oCPUDDk4hPupv5IWTrJv2IhmCBOaTZyK0rEsCVPN7Ozxcmp/YCvp4clOPKCyKwqu04iO4DGwxk6YjdvmHnpXtYx3PzU2Wg==",
  "SigningCertURL": "https://sns.us-east-1.amazonaws.com/SimpleNotificationService-01d088a6f77103d0fe307c0069e40ed6.pem",
  "UnsubscribeURL": "https://sns.us-east-1.amazonaws.com/?Action=Unsubscribe&SubscriptionArn=arn:aws:sns:us-east-1:908968368384:sandbox_platform-notifications-topic:0e94e75d-ba18-4d6b-a3fc-c35b7409ef02",
  "MessageAttributes": {
    "clientId": {
      "Type": "String",
      "Value": "2928df09-0b4e-43d7-93cf-7946aad24945"
    }
  }
}

Congratulations, you have successfully received your first notification on a transfer!

Updated 4 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/cards-banks-and-payments-risk-evaluation
Title: Cards, Banks and Payments Risk Evaluation
Description: undefined
Keywords: undefined

##🚧
Attention: Circle is longer supporting new customers for card payments via our Payments API. For any questions, please reach out to [customer-support@circle.com](mailto:customer-support@circle.com).

#
What is “Risk Evaluation”?
[](#what-is-risk-evaluation)
When you [create a card](/circle-mint/reference/createcard), a bank account or [process a payment](/circle-mint/reference/createpayment) with Circle Payments APIs, the response will include a riskEvaluation section.

Risk Evaluation is the response for the check that is executed by Circle's Risk Service. The Risk Service automatically runs checks for every card, bank account and payment.

In the current version of this risk evaluation, this data element will only be present in the response in situations where the decision made by Circle's Risk Service is denied. We plan to subsequently launch additional Risk Management features which will see the expansion of the risk evaluation to decisions other than denied and also to an asynchronous mode.

When Circle's Risk Service sets the status of a card, bank account or payment to denied you should receive a response similar to below:

Risk Evaluation Response

"riskEvaluation": {
	"decision": "denied",
	"reason": "3000"
},
As you can see, the data element contains two fields:

Field	Data Type	Description
decision	String (enumerated)	The decision made by Circle's Risk Service for this risk evaluation. denied is the only possible value in the current version of the service.
reason	Integer	The reason for the decision. The reason codes are explained in detail below.
#
Risk Evaluation: Denied Reason Codes
[](#risk-evaluation-denied-reason-codes)
Below are the current reason code categories (or buckets) you will find in the risk evaluation responses.

Reason Code Category (Bucket)	Explanation	Category Code Range
Circle Blocked	Fiat Account / Payment contains criteria that Circle is unable to support at this time.
E.g. Prohibited Country.	3000-3099
Processor / Issuing Bank	Fiat Account / Payment creation contains criteria that Circle's partner processor or the Issuing Bank are unable to accept.
E.g. unsupported issuer country, authorization failure.	3100-3199
Regulatory Compliance Intervention	Interventions by Circle Risk Service due to legal & regulatory compliance requirements.
E.g. KYC verification limits.	3200-3299
Fraud Risk Intervention	Fiat Account / Payment creation was actioned by Circle's Risk Service due to identified fraud management issues.
E.g. Excessive Chargeback Rates, Immediate Fraud Pressure.	3300-3499
Customer Configuration	Unsupported

Fiat Account / Payment creation was actioned by Circle's Risk Service due to customer configuration or request.
E.g. Block Issuer Country, Block Card Type, etc.	3500-3599
Customer Configuration	Fraud

Fiat Account / Payment creation was actioned by Circle's Risk Service due to customer configuration or request.
E.g. User was added to watchlist	3600-3699
The specific reason codes that apply currently are documented below.

Reason Code	Explanation
Circle Blocked	
3000	Default
3001	Prohibited Issuer (Bank) Country
3002	Prohibited Billing Address Country
3020	Fiat Account Denied (See Fiat Account for Reason)
3022	Fiat Account (Card) Evaluation Timeout. We recommend retrying.
3023	Fiat Account (Bank Account) Evaluation Timeout. We recommend retrying.
3026	Fiat Account in Unverified State.
3027	Fiat Account in Suspended State.
3030	Unsupported Bank Account Routing Number (rtn)
3040	Unsupported Activity Type
3050	Customer suspended from Payment Processing
3070	Transaction exceeds the risk limits
3071	Limit for Daily Aggregate exceeded (email)
3072	Limit for Daily Aggregate exceeded (fiat)
3075	Limit for Weekly Aggregate exceeded (email)
3076	Limit for Weekly Aggregate exceeded (fiat)
Regulatory Compliance Intervention	
3200 - 3202	Unsupported Criteria
3210	Withdrawal Limit Exceeded (7 Day Default Payout Limit)
3211	Withdrawal Limit Exceeded (7 Day Custom Payout Limit)
3220	Compliance Limit Exceeded
Fraud Risk Intervention	
3300 - 3309	Transaction Declined by Circle Risk Service. Please reach out to [risk-investigations@circle.com](mailto:risk-investigations@circle.com) for more information
3310	Fiat Account directly associated with fraudulent activity
3311	Email Address directly associated with fraudulent activity
3320	Fiat Account associated with network fraud notification
3321	Email Account associated with network fraud notification
3330	Fiat Account flagged by Risk Team
3331	Email Account flagged by Risk team
3340	Fiat Account linked to previous fraudulent activity
3341	Email Address linked to previous fraudulent activity
3350	3DS authentication is required for this transaction
Processor / Issuing Bank	
3100	Unsupported Return Code Response from Processor / Issuing Bank - Default
3101	Invalid Return Code Response from Issuing Bank E.g Invalid Card
3102	Fraudulent Return Code Response from Issuing Bank E.g Pickup Card
3103	Redlisted Entity Return Code Response from Processor E.g Card Redlisted
3104	Account associated with an invalid ACH RTN
3105	Expired Card
3150	Administrative return from ODFI / RDFI
3151	Return indicating ineligible account from customer / RDFI
3152	Unsupported transaction type return from customer / RDFI
Customer Configuration - Unsupported	
3500	Default
3501	Blocked Issuer (Bank) Country
3502	Blocked Billing Address Country
3520	Blocked Card Type E.g. Credit
3530 - 3539	Chargeback History on Circle Platform
3540 - 3549	Chargeback History on Customer Platform
3550	Blocked Fiat (Card)
3551	Blocked Email Address
3552	Blocked Phone Number
Customer Configuration - Fraud	
3600 - 3699	Blocked by fraud watchlist
If you are interested in some custom configurations, please contact the Circle Risk team.

Updated 5 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/introducing-circle-mint
Title: Overview
Description: undefined
Keywords: undefined

Circle Mint is the fastest and most cost-effective way to access and redeem USDC and EURC, currencies issued only by Circle. Your free Circle Mint account allows you to manage funds securely and distribute them to crypto wallets around the world, 24/7 using a web UI or API integration. With Circle Mint, you can access USDC and EURC at scale from a connected bank account while avoiding extra fees, added risk, and transaction time of third-party channels.

##
Benefits of Circle Mint:
[](#benefits-of-circle-mint)
- Fiat funds from your bank account can be converted automatically to USDC and EURC, and participating banks offer instant settlement programs.
- USDC and EURC can always be redeemed 1:1 for US dollars or Euros through Circle.
##
Access USDC directly from Circle, the sole issuer
[](#access-usdc-directly-from-circle-the-sole-issuer)
Only Circle issues USDC and EURC. Use Circle Mint to deposit traditional money from 80+ countries, then seamlessly convert it into [USDC](https://developers.circle.com/stablecoins/docs/what-is-usdc). Your Circle Mint Account and corresponding APIs allow you to do the following:

- Transfer digital currency (USDC) in and out of your Circle Mint account.
- Register your business bank accounts, if any.
- Transfer funds to or from your business bank account, seamlessly converting them between digital currency and traditional fiat.
##
Additional APIs available for Circle Mint customers
[](#additional-apis-available-for-circle-mint-customers)
Circle provides APIs for making USDC deposits and withdrawals to your Circle Mint account across supported blockchains. This includes APIs for accepting crypto deposits, sending crypto payouts, and managing your sub-account infrastructure. Please note these APIs are available at an additional cost and require further solutioning with our team in order to best meet your organization's needs.

##📘
Additional APIs

Learn how to use Circle's [Additional APIs](/circle-mint/docs/overview) to accept crypto deposits, make crypto payouts, and manage customer accounts.

##
Ready for the next step?
[](#ready-for-the-next-step)
After experimenting with our APIs, you’ll want to start building test integrations in sandbox prior to moving into production. Start by [applying for a Circle Mint account](https://www.circle.com/en/circle-mint). We'll be happy to walk you through the next steps.

Updated 3 months ago

-
[Getting Started with Circle APIs](/circle-mint/docs/getting-started-with-the-circle-apis)
-
[Core API Functionality](https://developers.circle.com/v1/docs/getting-started-with-the-circle-core-api)
-
[Getting Started with the Payments API](https://developers.circle.com/v1/docs/getting-started-with-the-circle-payments-api)
-
[Getting Started with the Payouts API](https://developers.circle.com/v1/docs/getting-started-with-the-circle-payouts-api)
-
[Getting Started with the Accounts API](https://developers.circle.com/docs/getting-started-with-the-circle-digital-dollar-accounts-api)

URL: https://developers.circle.com/circle-mint/docs/circle-apis-production-sandbox-environments
Title: Sandbox Environment
Description: undefined
Keywords: undefined

Circle’s sandbox environment is a development tool that allows you to safely test prototypes and integration without generating actual financial transactions. Sandbox APIs match those in production, making it easy to move into the production phase.

To ensure success, please note the following:

- When using the [Circle Payments API](/circle-mint/docs/getting-started-with-the-circle-payments-api) in sandbox , avoid using real payment method details such as real card numbers. Though your payment method will not be charged on sandbox, we recommend using false details, such as those on our [list of test card numbers](/circle-mint/docs/test-card-numbers).
##
Safe Development
[](#safe-development)
The sandbox environment is perfect for prototyping and integration testing. The APIs on sandbox respond authentically to your requests without connecting to the live Circle backend.

##
Easy Transitions
[](#easy-transitions)
The sandbox APIs align perfectly with those in production. Once you’ve perfected your code in the sandbox environment, you can simply replace the sandbox endpoints with the production ones to create working code.

##
Simulated Transactions
[](#simulated-transactions)
The APIs on sandbox do not actually charge payment methods or manipulate actual money or USDC. The sandbox environment connects safely to [USDC using test networks only](https://developers.circle.com/stablecoins/docs/usdc-on-test-networks).

##
API Environments and Hosts
[](#api-environments-and-hosts)
Use these hosts to access APIs on sandbox and production environments.

Environment	API host
Sandbox	[https://api-sandbox.circle.com](https://api-sandbox.circle.com)
Production	[https://api.circle.com](https://api.circle.com)
##
Getting Started
[](#getting-started)
Ready to start writing code in sandbox against the API endpoints? To get started, take a few moments to [create a sandbox account](https://app-sandbox.circle.com).

Updated 5 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/funding-test-wallets
Title: Fund Test Wallets
Description: undefined
Keywords: undefined

The following sections will step through funding the master wallet with USDC, EURC, BTC and ETH.

#
Funding USDC Balance
[](#funding-usdc-balance)
There are two main ways of funding your wallets for testing. The first one is to use the Circle Payments API to accept a payment - the settlement of such payment will then fund your master wallet. The second one is to receive an external USDC transfer - we suggest using a faucet if you don't have any USDC for testing.

##
Option 1: Fund your wallet by accepting a payment
[](#option-1-fund-your-wallet-by-accepting-a-payment)
If you are also using the [Circle Payments API](/circle-mint/docs/getting-started-with-the-circle-payments-api) then the easiest way to fund a wallet for testing is to [accept a test card payment](/circle-mint/docs/circle-payments-api-quickstart).

###
1. Accept a test card payment
[](#1-accept-a-test-card-payment)
Follow the [payments quickstart guide](/circle-mint/docs/circle-payments-api-quickstart) to accept a card payment.

###
2. Wait 5-10 minutes for settlement
[](#2-wait-5-10-minutes-for-settlement)
On the [sandbox environment](/circle-mint/docs/circle-apis-production-sandbox-environments) it takes 5-10 minutes for a payment to settle. You can check on the status of your payment by using the [retrieve payment endpoint](/circle-mint/reference/listpayments). Run the following command for that.

Terminal

# Replace ${YOUR_API_KEY} with your API key
# Replace ${PAYMENT_ID} with your payment id
curl -H 'Accept: application/json' \
  -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X GET --url https://api-sandbox.circle.com/v1/payments/${PAYMENT_ID}
You will notice that the payment has settled once its status changes from confirmed to paid.

###
3. Confirm your master wallet Balance
[](#3-confirm-your-master-wallet-balance)
Once the processed payment settles in the sandbox environment, it will credit your [master wallet](/circle-mint/docs/your-master-wallet). You can confirm your master wallet balance by using the [balances endpoint](/circle-mint/reference/listbalances) in the following way.

Terminal

# Replace ${YOUR_API_KEY} with your API key
curl -H 'Accept: application/json' \
  -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X GET --url https://api-sandbox.circle.com/v1/businessAccount/balances
You should receive a response like below, and at this point you will be able to use your funds for Wallets API testing.

Balances Response

{
  "data": {
    "available": [
      {
        "amount": "98.95",
        "currency": "USD"
      }
    ],
    "unsettled": []
  }
}
##
Option 2: Fund your wallet with an external USDC transfer
[](#option-2-fund-your-wallet-with-an-external-usdc-transfer)
If you are not using the [Circle Payments API](/circle-mint/docs/getting-started-with-the-circle-payments-api) then the easiest way to fund a wallet for testing is to receive USDC from an external blockchain wallet, such as a faucet.

###
1. Generate a blockchain address for your master wallet
[](#1-generate-a-blockchain-address-for-your-master-wallet)
In order to receive funds from an external blockchain wallet, first you need to create a deposit address associated to your [master wallet](/circle-mint/docs/your-master-wallet). Use the [configuration endpoint](/circle-mint/reference/getaccountconfig) to obtain your master wallet id.

You can do so with the following command (you should generate your own [idempotency key](/circle-mint/docs/a-note-on-idempotent-requests)).

Terminal

# Replace ${YOUR_API_KEY} with your API key
# Replace ${MASTER_WALLET_ID} with your master wallet id
curl -H 'Accept: application/json' \
  -H 'content-type: application/json' \
  -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X POST --url https://api-sandbox.circle.com/v1/wallets/${MASTER_WALLET_ID}/addresses \
  --data '{"idempotencyKey": "2678c583-20d1-405d-b185-047f8d01fe6c", "currency": "USD", "chain": "ETH"}'
The response depends on the chain you've chosen, but will look similar to the following:

JSON

{
  "data":{
    "address":"0xd70ea0867959341ca159af340416a02037191c91",
    "currency":"USD",
    "chain":"ETH"
  }
}
###
2. Send USDC funds from an external wallet
[](#2-send-usdc-funds-from-an-external-wallet)
The [sandbox environment](/circle-mint/docs/circle-apis-production-sandbox-environments) is connected to the [testing networks](https://developers.circle.com/stablecoins/docs/usdc-on-testing-networks) of the blockchains where [USDC is supported](/circle-mint/docs/supported-chains-and-currencies). So you can send USDC from any of the supported blockchain test network wallets in order to fund your Circle hosted wallet. For information see our guide on [USDC on testing networks](https://developers.circle.com/stablecoins/docs/usdc-on-testing-networks).

For testing with Ethereum or Algorand, if you have USDC on an external Sepolia or Algorand TestNet wallet, simply send some amount to the address you generated above. If you don't have any USDC for testing, you can get some by using a [USDC faucet](https://faucet.circle.com/). Go to [faucet.circle.com](https://faucet.circle.com/) and make sure you choose either ETH or ALGO. Paste the address you generated above (be very careful to not miss any characters) and hit Submit. You will be able to monitor the transfer on the blockchain by using the Etherscan/AlgoExplorer link provided by the faucet.

##❗️
Lost Funds

Do NOT send real USDC to addresses generated in the sandbox environment as your funds might be permanently lost.

###
3. Confirm your master wallet balance
[](#3-confirm-your-master-wallet-balance-1)
After the required [confirmations](/circle-mint/docs/blockchain-confirmations) (a few minutes in the case of Ethereum, a few seconds in the case of most other chains), your transfer should settle on the sandbox environment and the amount sent should be credited to your wallet. You can confirm your master wallet balance by using the [balances endpoint](/circle-mint/reference/listbalances) in the following way.

Terminal

# Replace ${YOUR_API_KEY} with your API key
curl -H 'Accept: application/json' \
  -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X GET --url https://api-sandbox.circle.com/v1/businessAccount/balances
You should receive a response like below, and at this point you will be able to use your funds for Wallets API testing.

Balances Response

{
  "data": {
    "available": [
      {
        "amount": "1.00",
        "currency": "USD"
      }
    ],
    "unsettled": []
  }
}
#
Funding EURC balance
[](#funding-eurc-balance)
At the moment, to fund your wallets for testing EURC you need to receive an external EURC transfer.

##
Fund your wallet with an external EURC transfer
[](#fund-your-wallet-with-an-external-eurc-transfer)
At this time, there isn't a faucet that mints EURC. To acquire EURC on a Testnet you will need to go to a decentralized exchange (DEX) on the applicable TestNet and swap a token for EURC.

###
1. Generate a blockchain address for your master wallet
[](#1-generate-a-blockchain-address-for-your-master-wallet-1)
In order to receive funds from an external blockchain wallet, first you need to create a deposit address associated to your [master wallet](/circle-mint/docs/your-master-wallet). Use the [configuration endpoint](/circle-mint/reference/getaccountconfig) to obtain your master wallet id.

You can do so with the following command (you should generate your own [idempotency key](/circle-mint/docs/a-note-on-idempotent-requests)).

Terminal

# Replace ${YOUR_API_KEY} with your API key
# Replace ${MASTER_WALLET_ID} with your master wallet id
curl -H 'Accept: application/json' \
  -H 'content-type: application/json' \
  -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X POST --url https://api-sandbox.circle.com/v1/wallets/${MASTER_WALLET_ID}/addresses \
  --data '{"idempotencyKey": "2678c583-20d1-405d-b185-047f8d01fe6c", "currency": "EUR", "chain": "ETH"}'
The response depends on the chain you've chosen, but will look similar to the following:

JSON

{
  "data":{
    "address":"0xd70ea0867959341ca159af340416a02037191c91",
    "currency":"EUR",
    "chain":"ETH"
  }
}
###
2. Send EURC funds from an external wallet
[](#2-send-eurc-funds-from-an-external-wallet)
The [sandbox environment](/circle-mint/docs/circle-apis-production-sandbox-environments) is connected to the [testing networks](https://developers.circle.com/stablecoins/docs/eurc-on-test-networks) of the blockchains where [EURC is supported](/circle-mint/docs/supported-chains-and-currencies). So you can send EURC from any of the supported blockchain test network wallets in order to fund your Circle hosted wallet.

##❗️
Lost Funds

Do NOT send real EURC to addresses generated in the sandbox environment as your funds might be permanently lost.

###
3. Confirm your master wallet balance
[](#3-confirm-your-master-wallet-balance-2)
After the required [confirmations](/circle-mint/docs/blockchain-confirmations) (a few minutes in the case of Ethereum, a few seconds in the case of most other chains), your transfer should settle on the sandbox environment and the amount sent should be credited to your wallet. You can confirm your master wallet balance by using the [balances endpoint](/circle-mint/reference/listbalances) in the following way.

Terminal

# Replace ${YOUR_API_KEY} with your API key
curl -H 'Accept: application/json' \
  -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X GET --url https://api-sandbox.circle.com/v1/businessAccount/balances
You should receive a response like below, and at this point you will be able to use your funds for Wallets API testing.

Balances Response

{
  "data": {
    "available": [
      {
        "amount": "1.00",
        "currency": "EUR"
      }
    ],
    "unsettled": []
  }
}
#
Funding BTC and ETH Balances
[](#funding-btc-and-eth-balances)
To fund BTC and ETH balances in Sandbox, you can similarly use a [Bitcoin testnet faucet](https://kuttler.eu/en/bitcoin/btc/faucet/) or an [Ethereum Sepolia faucet](https://sepoliafaucet.com/).

Updated 4 months ago

-
[✨ Send Funds Externally](/circle-mint/docs/send-funds-externally)
-
[✨ Receive External Funds](/circle-mint/docs/receive-external-funds)
-
[✨ Swap USDC Across Blockchains](/circle-mint/docs/swap-usdc-across-blockchains)
Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/introducing-circle-mint
Title: Overview
Description: undefined
Keywords: undefined

Circle Mint is the fastest and most cost-effective way to access and redeem USDC and EURC, currencies issued only by Circle. Your free Circle Mint account allows you to manage funds securely and distribute them to crypto wallets around the world, 24/7 using a web UI or API integration. With Circle Mint, you can access USDC and EURC at scale from a connected bank account while avoiding extra fees, added risk, and transaction time of third-party channels.

##
Benefits of Circle Mint:
[](#benefits-of-circle-mint)
- Fiat funds from your bank account can be converted automatically to USDC and EURC, and participating banks offer instant settlement programs.
- USDC and EURC can always be redeemed 1:1 for US dollars or Euros through Circle.
##
Access USDC directly from Circle, the sole issuer
[](#access-usdc-directly-from-circle-the-sole-issuer)
Only Circle issues USDC and EURC. Use Circle Mint to deposit traditional money from 80+ countries, then seamlessly convert it into [USDC](https://developers.circle.com/stablecoins/docs/what-is-usdc). Your Circle Mint Account and corresponding APIs allow you to do the following:

- Transfer digital currency (USDC) in and out of your Circle Mint account.
- Register your business bank accounts, if any.
- Transfer funds to or from your business bank account, seamlessly converting them between digital currency and traditional fiat.
##
Additional APIs available for Circle Mint customers
[](#additional-apis-available-for-circle-mint-customers)
Circle provides APIs for making USDC deposits and withdrawals to your Circle Mint account across supported blockchains. This includes APIs for accepting crypto deposits, sending crypto payouts, and managing your sub-account infrastructure. Please note these APIs are available at an additional cost and require further solutioning with our team in order to best meet your organization's needs.

##📘
Additional APIs

Learn how to use Circle's [Additional APIs](/circle-mint/docs/overview) to accept crypto deposits, make crypto payouts, and manage customer accounts.

##
Ready for the next step?
[](#ready-for-the-next-step)
After experimenting with our APIs, you’ll want to start building test integrations in sandbox prior to moving into production. Start by [applying for a Circle Mint account](https://www.circle.com/en/circle-mint). We'll be happy to walk you through the next steps.

Updated 3 months ago

-
[Getting Started with Circle APIs](/circle-mint/docs/getting-started-with-the-circle-apis)
-
[Core API Functionality](https://developers.circle.com/v1/docs/getting-started-with-the-circle-core-api)
-
[Getting Started with the Payments API](https://developers.circle.com/v1/docs/getting-started-with-the-circle-payments-api)
-
[Getting Started with the Payouts API](https://developers.circle.com/v1/docs/getting-started-with-the-circle-payouts-api)
-
[Getting Started with the Accounts API](https://developers.circle.com/docs/getting-started-with-the-circle-digital-dollar-accounts-api)
Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/create-a-sub-account
Title: Quickstart: Create a Sub-Account
Description: undefined
Keywords: undefined

Learn the basics of sub-accounts within Circle’s Accounts API.

The Accounts API empowers you to seamlessly create and host digital wallets for your customers, and manage the transfers of funds across accounts within the Circle platform. Sub-accounts are the building blocks within the Accounts infrastructure where the digital funds reside and can be moved between.

##
1. Get an API key
[](#1-get-an-api-key)
The Accounts API uses [API keys](https://developers.circle.com/circle-mint/docs/api-keys) as the mechanism to authenticate client requests. The API key must be set in the Authorization header of the request sent from your backend server. The format of the header is Bearer secret-key-value.To obtain an API key for the sandbox environment, simply create an account and generate a new key in settings - it only takes a few seconds:

- [GET AN API KEY](https://app-sandbox.circle.com/)
Once you have generated your API key, record it in a secure place.

##
2. Create a new wallet
[](#2-create-a-new-wallet)
For the purpose of this guide, we'll create a brand new sub-account wallet. You can create new hosted wallets using the [create wallet endpoint](https://developers.circle.com/circle-mint/reference/createwallet). Use the command below (create your own [idempotency key](https://developers.circle.com/circle-mint/docs/a-note-on-idempotent-requests)).

Request

Replace ${YOUR\_API\_KEY} with your API key.

cURL

curl -H 'Content-type: application/json'
 -H "Authorization: Bearer ${YOUR\_API\_KEY}"
 -X POST https\://api-sandbox.circle.com/v1/wallets
 --data '{"idempotencyKey": "4ddef365-f2fc-4a56-95f5-a214c84ba8f4"}'You should receive a response like the below\.Create Wallet Response{  "data":{    "walletId":"1000005062",    "entityId":"7cbdedb1-d526-46b4-af12-4162a002eb9c",    "type":"end\_user\_wallet"  }}**
##
3. Get a wallet
[](#3-get-a-wallet)
Now that you’ve created a sub-account wallet, we will use the walletId from the response above to retrieve details. This is an important request for checking balance and description details for a given wallet at any time after creation.

Request

cURL

curl --request GET
    --url https\://api-sandbox.circle.com/v1/wallets/1016889587\     --header 'accept: application/json'
    --header 'authorization: Bearer ${YOUR\_API\_KEY}'You should receive a response like the below\.GET a Wallet Response{  "data":{    "walletId":"1000005062",    "entityId":"7cbdedb1-d526-46b4-af12-4162a002eb9c",    "type":"end\_user\_wallet"  }}**
##
3. List all wallets
[](#3-list-all-wallets)
In addition to requesting a specific wallet, you have the ability to retrieve a list of all sub-account wallets that have been created. You can make a general request to list all wallets or you can specify parameters in your GET request such as from and to which will limit the response to wallets created within the defined dates set.

Request

cURL

curl --request GET
    --url https\://api-sandbox.circle.com/v1/wallets
    --header 'accept: application/json'
    --header 'authorization: Bearer ${YOUR\_API\_KEY}'You should receive a response like the one below\.GET Wallets Response{  "data": \[    {      "walletId": "1016889587",      "entityId": "b3e34c09-69c9-4181-a564-46be0a89084a",      "type": "end\_user\_wallet",      "description": "Jamie’s Wallet",      "balances": \[]    },    {      "walletId": "1016889390",      "entityId": "b3e34c09-69c9-4181-a564-46be0a89084a",      "type": "end\_user\_wallet",      "description": "Main Wallet",      "balances": \[]    },    {      "walletId": "1016885777",      "entityId": "b3e34c09-69c9-4181-a564-46be0a89084a",      "type": "end\_user\_wallet",      "description": "Treasury Wallet",      "balances": \[]    },      ]}]}**
##
4. Create an internal transfer
[](#4-create-an-internal-transfer)
Now that you have at least one sub-account within your Circle Mint infrastructure, you can perform an internal (off-chain) transfer.

Request

cURL

curl --request POST
    --url https\://api-sandbox.circle.com/v1/transfers
    --header 'accept: application/json'
    --header 'authorization: Bearer ${YOUR\_API\_KEY}'{  "source": {    "type": "wallet",    "id": "1016889390"  },  "destination": {    "type": "wallet",\
    "id": "1016885777"  },"amount": {    "currency": "USD",    "amount": "3.14"  }}'You should receive a response like the one below.{"id":"0107eea3-d661-45d7-8e69-c662b5ec8bb1","source":{"type":"wallet","id":"1016889390"}"destination":{"type":"wallet","id":"1016885777"}"amount":{"amount":"3.14","currency":"USD"}"status":"pending","createDate":"2023-12-11T22:08:23.752Z"}
🎉 Congratulations! You have successfully created a sub-account and initiated an internal transfer within your Circle Mint account.

Updated 3 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/migrate-from-mumbai-to-amoy-testnet
Title: Migrate from Mumbai to Amoy
Description: undefined
Keywords: undefined

Circle will no longer support Circle Mint sandbox on Polygon PoS Mumbai, effective March 27, 2024. This change will not impact your API integration, as the API parameters will remain unchanged. Rest assured, Circle will preserve the transactional history for all accounts. See the blog post for Upcoming support for Polygon PoS Amoy testnet to learn more about other affected Circle products.

Upcoming availability:

- March 27, 2024: USDC on Polygon PoS Amoy
Note: Launch dates are tentative and subject to change. If you have questions or want to learn more about Circle Mint, join the conversation in our [Discord](https://discord.com/invite/buildoncircle).

Stablecoin	Blockchain	Available	Amoy Testnet Address
USDC	Polygon PoS	No	[0x41e94eb019c0762f9bfcf9fb1e58725bfb0e7582](https://www.oklink.com/amoy/address/0x41e94eb019c0762f9bfcf9fb1e58725bfb0e7582)
Updated about 1 month ago


URL: https://developers.circle.com/circle-mint/docs/quickstart-withdraw-to-bank
Title: Quickstart: Withdraw to Bank
Description: undefined
Keywords: undefined

Sending funds from your Circle Account to an external bank account is one of the most basic primitives (building blocks) enabled by the APIs. This quickstart walks through sending USDC USD externally. You can follow the same steps when sending EURC EUR as well.

##
1. Get an API key
[](#1-get-an-api-key)
Circle's APIs use [API keys](https://developers.circle.com/circle-mint/docs/api-keys) as the mechanism to authenticate client requests. The API key must be set in the Authorization header of the request sent from your backend server. The format of the header is Bearer secret-key-value.

To obtain an API key for the sandbox environment, simply [create an account](https://app.circle.com/get-started/create-account) and generate a new key in settings - it only takes a few seconds.

[GET AN API KEY](https://app-sandbox.circle.com/)

Once you have generated your API key, record it in a secure place.

##
2. Link a bank account
[](#2-link-a-bank-account)
To send account funds externally, you will need a bank account to send to.

Use the [Create A Wire Bank Account](https://developers.circle.com/circle-mint/reference/createbusinesswireaccount) endpoint to link a sample bank account.

NOTE - the parameters listed below are slightly different for US Bank accounts, Non-US bank accounts - IBAN supported and Non-US bank accounts - non-IBAN supported. Please see the above link for details.

Request

cURL
JavaScript

curl --request POST \\
--url https\://api-sandbox.circle.com/v1/businessAccount/banks/wires \\
--header 'accept: application/json' \\
--header 'authorization: Bearer ${YOUR\_API\_KEY}' \\
--header 'content-type: application/json' \\
--data '
{
  "billingDetails": {
    "name": "Satoshi Nakamoto",
    "city": "Boston",
    "country": "US",
    "line1": "100 Money Street",
    "district": "MA",
    "postalCode": "01234"
  },
  "bankAddress": {
    "bankName": "SAN FRANCISCO",
    "city": "SAN FRANCISCO",
    "country": "US",
    "line1": "100 Money Street",
    "district": "CA"
  },
  "idempotencyKey": "ba943ff1-ca16-49b2-ba55-1057e70ca5c7",
  "accountNumber": "12340010",
  "routingNumber": "121000248"
}
'
RESPONSE

JSON

{
  "data": {
    "id": "9d1fa351-b24d-442a-8aa5-e717db1ed636",
    "status": "pending",
    "description": "WELLS FARGO BANK, NA ****0010",
    "trackingRef": "CIR2GKYL4B",
    "fingerprint": "a9a71b77-d83d-4fbc-997f-41a33550c594",
    "virtualAccountEnabled": true,
    "billingDetails": {
      "name": "Satoshi Nakamoto",
      "line1": "100 Money Street",
      "city": "Boston",
      "postalCode": "01234",
      "district": "MA",
      "country": "US"
    },
    "bankAddress": {
      "bankName": "WELLS FARGO BANK, NA",
      "line1": "100 Money Street",
      "city": "SAN FRANCISCO",
      "district": "CA",
      "country": "US"
    },
    "createDate": "2023-11-04T20:02:21.062Z",
    "updateDate": "2023-11-04T20:02:21.062Z"
  }
}
##
2. Fund Your Account
[](#2-fund-your-account)
If you haven't done so already, make sure you [fund your account for testing](https://developers.circle.com/circle-mint/docs/funding-test-wallets). You can do this one of two ways: by using the Circle Payments API to accept a payment, or by receiving an external USDC transfer from a faucet or other source.

##
3. Send Funds
[](#3-send-funds)
##🚧
Sufficient Balance Requirement

You can transfer any amount you want if your account holds sufficient balance to cover the transfer.

To send funds externally, you will use the [create a payout endpoint](https://developers.circle.com/circle-mint/reference/createbusinesspayout).

REQUEST

cURL
JavaScript

curl --request POST \\
--url https\://api-sandbox.circle.com/v1/businessAccount/payouts \\
--header 'accept: application/json' \\
--header 'authorization: Bearer ${YOUR\_API\_KEY}' \\
--header 'content-type: application/json' \\
--data '
{
  "destination": {
    "type": "wire",
    "id": "9d1fa351-b24d-442a-8aa5-e717db1ed636"
  },
  "amount": {
    "currency": "USD",
    "amount": "1.00"
  },
  "idempotencyKey": "ba943ff1-ca16-49b2-ba55-1057e70ca5c7"
}
'
RESPONSE

JSON

{
  "data": {
    "id": "9cf38c76-cac4-40d8-a516-f46e9a610a85",
    "amount": {
      "amount": "1.00",
      "currency": "USD"
    },
    "status": "pending",
    "sourceWalletId": "1016875042",
    "destination": {
      "type": "wire",
      "id": "9d1fa351-b24d-442a-8aa5-e717db1ed636",
      "name": "WELLS FARGO BANK, NA ****0010"
    },
    "createDate": "2023-11-04T20:04:41.669Z",
    "updateDate": "2023-11-04T20:04:41.669Z"
  }
}
##
4. Check the Status of the Transfer
[](#4-check-the-status-of-the-transfer)
You can use the [get a payout endpoint](https://developers.circle.com/circle-mint/reference/getbusinesspayout) to retrieve details about the status of the transaction. You can use it as in the command below.

REQUEST

cURL
JavaScript

curl --request GET \\
--url https\://api-sandbox.circle.com/v1/businessAccount/payouts/9cf38c76-cac4-40d8-a516-f46e9a610a85 \\
--header 'accept: application/json' \\
--header 'authorization: Bearer ${YOUR\_API\_KEY}'
RESPONSE

JSON

{
  "data": {
    "id": "9cf38c76-cac4-40d8-a516-f46e9a610a85",
    "amount": {
      "amount": "1.00",
      "currency": "USD"
    },
    "status": "pending",
    "sourceWalletId": "1016875042",
    "destination": {
      "type": "wire",
      "id": "9d1fa351-b24d-442a-8aa5-e717db1ed636",
      "name": "WELLS FARGO BANK, NA ****0010"
    },
    "createDate": "2023-11-04T20:04:41.669Z",
    "updateDate": "2023-11-04T20:04:41.778Z"
  }
}
##📘
For Circle Mint Singapore Customers

You must verify all recipients of your transfers using the UI in the Circle Console.

🎉 Congratulations. You have successfully sent fiat using Circle's APIs.

##
5. Ready for the next step?
[](#5-ready-for-the-next-step)
After experimenting with our APIs, you’ll want to start building test integrations in sandbox prior to moving into production. Start by [applying for a Circle Mint account](https://www.circle.com/en/circle-mint). We'll be happy to walk you through the next steps.

Updated about 1 month ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/quickstart-withdraw-via-blockchain-wallet
Title: Quickstart: Withdraw via Blockchain Wallet
Description: undefined
Keywords: undefined

Sending funds from your Circle Account to external blockchain wallets is one of the most basic primitives (building blocks) enabled by the APIs. This quickstart walks through sending USDC USD externally. You can follow the same steps when sending EUROC EUR as well.

##
1. Get an API key
[](#1-get-an-api-key)
Circle's APIs use API keys as the mechanism to authenticate client requests. The API key must be set in the Authorization header of the request sent from your backend server. The format of the header is Bearer secret-key-value.
To obtain an API key for the sandbox environment, simply create an account and generate a new key in settings - it only takes a few seconds.

Once you have generated your API key, record it in a secure place.

##
2. Fund Your Account
[](#2-fund-your-account)
If you haven't done so already, make sure you fund your account for testing. You can do this one of two ways: by using the Circle Payments API to accept a payment, or by receiving an external USDC transfer from a faucet or other source.

##
3. Add Recipient Address
[](#3-add-recipient-address)
To send account funds externally, you will need to whitelist a blockchain address to send to (destination).
The sandbox environment is connected to the Ethereum Sepolia testing network, so the destination blockchain address has to be a valid Sepolia address.
Note📘

##📘
Note

USDC is connected to testing networks on other blockchains as well. This guide focuses on Ethereum but works similarly for other blockchains. For information on other testing networks see [Test USDC](https://developers.circle.com/circle-mint/reference/createbusinessrecipientaddress).

##🚧
For Singapore customers

Circle Mint Singapore customers must verify all transfer recipients using the UI in the Circle Console, as transfers from unverified addresses will be held in pending status. Please see this Help Center [article](https://help.circle.com/s/article-page?articleId=ka08b000000R3tPAAS#-32) for details.

REQUEST

cURL
JavaScript

curl --request POST \
     --url https://api-sandbox.circle.com/v1/businessAccount/wallets/addresses/recipient \
     --header 'accept: application/json' \
     --header 'authorization: Bearer ${YOUR_API_KEY}' \
     --header 'content-type: application/json' \
     --data '
{
  "chain": "ETH",
  "idempotencyKey": "2a308497-e66e-4c42-ac1e-7bedab86d958",
  "address": "0x493A9869E3B5f846f72267ab19B76e9bf99d51b1",
  "currency": "USD",
  "description": "https://usdcfaucet.com/"
}
'
RESPONSE

JSON

{
  "data": {
    "id": "cfa01bb0-d166-5506-a48a-56f2beab559f",
    "address": "0x493a9869e3b5f846f72267ab19b76e9bf99d51b1",
    "chain": "ETH",
    "currency": "USD",
    "description": "https://usdcfaucet.com/"
  }
}
##
4. Send Funds
[](#4-send-funds)
To keep things simple, we'll transfer funds to the deposit address of the USDC faucet service on Sepolia. That address is 0x493A9869E3B5f846f72267ab19B76e9bf99d51b1.

##🚧
Balance Requirement

You can transfer any amount you want, provided your account has sufficient balance to cover the transfer.
To send funds externally, you will use the create account transfer endpoint.

##📘
Specify your blockchain

Because Circle's APIs are designed for multiple future chains and currencies, you must specify the currency and chain you want to utilize. You can create a transfer by using the command below.

REQUEST

cURL
JavaScript

curl --request POST \
     --url https://api-sandbox.circle.com/v1/businessAccount/transfers \
     --header 'accept: application/json' \
     --header 'authorization: Bearer ${YOUR_API_KEY}' \
     --header 'content-type: application/json' \
     --data '
{
  "destination": {
    "type": "verified_blockchain",
    "addressId": "cfa01bb0-d166-5506-a48a-56f2beab559f"
  },
  "amount": {
    "currency": "USD",
    "amount": "3.14"
  },
  "idempotencyKey": "6ec3827d-15bb-442e-9d4c-32e73e61cbf4"
}
'
RESPONSE

JSON

{
  "data": {
    "id": "21fd4ec4-bad1-4eb2-9fc5-60320dedc7ea",
    "source": {
      "type": "wallet",
      "id": "1016875042"
    },
    "destination": {
      "type": "blockchain",
      "address": "0x493a9869e3b5f846f72267ab19b76e9bf99d51b1",
      "chain": "ETH"
    },
    "amount": {
      "amount": "3.14",
      "currency": "USD"
    },
    "status": "pending",
    "createDate": "2023-11-15T16:41:12.395Z"
  }
}
##
5. Check the Status of the Transfer
[](#5-check-the-status-of-the-transfer)
You can use the get transfer endpoint to retrieve details about the status of the transaction. You can use it as in the command below.
REQUEST

cURL
JavaScript

curl --request GET \
     --url https://api-sandbox.circle.com/v1/businessAccount/transfers/21fd4ec4-bad1-4eb2-9fc5-60320dedc7ea \
     --header 'accept: application/json' \
     --header 'authorization: Bearer ${YOUR_API_KEY}'
Javascript Request
const options = {
  method: 'GET',
  headers: {
    accept: 'application/json',
    authorization: 'Bearer ${YOUR_API_KEY}'
  }
};
RESPONSE

JSON

{
  "data": {
    "id": "21fd4ec4-bad1-4eb2-9fc5-60320dedc7ea",
    "source": {
      "type": "wallet",
      "id": "1016875042"
    },
    "destination": {
      "type": "blockchain",
      "address": "0x493a9869e3b5f846f72267ab19b76e9bf99d51b1",
      "chain": "ETH"
    },
    "amount": {
      "amount": "3.14",
      "currency": "USD"
    },
    "transactionHash": "0x0654eee4f609f9c35e376cef9455dd9fc1546c482c5c32c8f8d434ead14fcf97",
    "status": "complete",
    "createDate": "2023-11-15T16:41:12.395Z"
  }
}
##👍
Notes about the process

The very first state transition for a transfer sets the status to created and the transactionHash to null. At this point Circle has just started processing the on-chain send.

When Circle broadcasts the transfer a few seconds later, its status will change to running. It will also display a transactionHash you can use to track the transfer on chain, which you can look up on Etherscan's tracker on Sepolia.

Though not yet settled, you can consider the transaction complete because it has been successfully broadcasted to the network. Circle's systems will continue to track the transfer for 30 confirmations, at which point its status will change to complete.

Except for certain cases, you do not have to worry about waiting for the complete state, which is most important when receiving external transfers. You can read more about block confirmations here.

🎉 Congratulations. You have successfully sent cryptocurrency using Circle's APIs.

##
6. Ready for the next step?
[](#6-ready-for-the-next-step)
After experimenting with our APIs, you’ll want to start building test integrations in sandbox prior to moving into production. Start by applying for a Circle Mint account. We'll be happy to walk you through the next steps.

Updated 23 days ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/circle-api-resources
Title: API Resources
Description: undefined
Keywords: undefined

Circle APIs are a set of functions and procedures that allow you to use USDC for payments, payouts, and transfers. Circle's APIs are built around several resources that represent payments, payouts, transfers, and their associated objects.

This page contains information on the following resources:

###
Primary Resources
[](#primary-resources)
Payment Object - a payment from a customer. Can be funded from a card or an incoming wire.
Payout Object - a payout to a customer, vendor, or supplier.
Transfer Object - a transfer of funds from a Circle wallet to a blockchain address, from a blockchain address to a Circle wallet, or between two Circle wallets.

###
Methods and Instruments
[](#methods-and-instruments)
Wire Account Object
Wallet Object - balances managed by Circle

###
Additional Resources
[](#additional-resources)
Cancel Object - an attempt at canceling a payment
Refund Object - an attempt at refunding a payment

###
Nested Resources
[](#nested-resources)
Source and Destination Objects - indicate where the funds are coming from and where they're going.
Money Object - a monetary quantity represented as an amount and a currency
Blockchain Addresses
Merchants

#
Primary Resources
[](#primary-resources-1)
##
Payment Object
[](#payment-object)
A payment object represents a payment from a customer and can be funded from a card or an incoming wire.

###
Example Payment Object
[](#example-payment-object)
Payment

{
  "id": "e665ea6e-3a53-4f93-a85e-45178d48d9ea",
  "type": "payment",
  "merchantId": "c680d087-7b41-40aa-95a2-68febcdddb22",
  "merchantWalletId": "1000002853",
  "amount": {
    "amount": "1.0",
    "currency": "USD"
  },
  "source": {
    "id": "86461e9f-db1a-487f-915b-641138062e7c",
    "type": "card"
  },
  "description": "description",
  "status": "pending",
  "verification": {
    "avs": "not_requested",
    "cvv": "not_requested"
  },
  "cancel": { .. },
  "refunds": [],
  "fees": {
    "amount": "3.0",
    "currency": "USD"
  },
  "trackingRef": "74537600052007895984372",
  "errorCode": "payment_failed",
  "metadata": {
    "email": "satoshi@circle.com",
    "phoneNumber": "+14155555555"
  },
  "riskEvaluation": {
    "decision": "denied",
    "reason": "3000"
  },
  "createDate": "2020-04-10T02:29:53.888Z",
  "updateDate": "2020-04-10T02:32:19.421Z"
}
###
Payment Attributes
[](#payment-attributes)
id string
A UUID for the payment.

type string
The type of payment object. Possible values: payment, refund, cancel.

merchantId string
The UUID of the merchant.

merchantWalletId string
The id of the merchant's wallet.

amount object
A [Money object](#money-object) representing the total payment amount that is charged to the source.

source object
A [Source object](#source-and-destination-objects) representing the payment source.

description string
Description of the payment.

status string
Enumerated status of the payment. pending means the payment is waiting to be processed. confirmed means the payment has been approved by the bank and the merchant can treat it as successful, but settlement funds are not yet available to the merchant. paid means settlement funds have been received and are available to the merchant. failed means something went wrong (most commonly that the payment was denied). Terminal states are paid and failed.

verification object
Status of verification checks. Attributes include avs and cvv.
For avs, the value represents the raw AVS response, expressed as an upper-case letter. not_requested indicates check was not made, and pending is pending/processing.
For cvv, not_requested indicates check was not made. pass indicates value is correct. fail indicates value is incorrect. unavailable indicates card issuer did not do the provided check. pending indicates check is pending/processing.

cancel object
A [Cancel object](#the-cancel-object) if one exists.

refunds [object]
An array of [Refund objects](#the-refund-object) if they exist.

fees [object]
A [Money object](#money-object) representing fees associated with this payment.

trackingRef string
Payment tracking reference.

errorCode string
Indicates the failure reason of a payment. Only present for payments in failed state. See [Entity Errors](/circle-mint/docs/entity-errors) for a full list of possible codes.

metadata object
The email and phoneNumber provided during payment creation.

riskEvaluation object
An object with two attributes, decision and reason.

createDate string
ISO-8601 UTC date/time format.

updateDate string
ISO-8601 UTC date/time format.

##
Payout Object
[](#payout-object)
A payout object represents a payout to a customer, vendor, or supplier.

###
Example Payout Object
[](#example-payout-object)
Payout

{
  "id": "df3b8e5f-9579-4c1f-9fa9-deac7f4be55c",
  "sourceWalletId": "1000038499",
  "destination": {
    "id": "4d260293-d17c-4309-a8da-fa7850f95c10",
    "type": "wire",
  },
  "amount": {
    "amount": "10.0",
    "currency": "USD"
  },
  "fees": {
    "amount": 3.0,
    "currency": "USD"
  },
  "status": "complete",
  "trackingRef": "95476600046382591768456",
  "errorCode": "insufficient_funds",
  "riskEvaluation": {
    "decision": "denied",
    "reason": "3000"
  },
  "return": { ... },
  "createDate": "2020-12-24T11:19:20.561Z",
  "updateDate": "2020-12-24T12:01:00.523Z",
  
}
###
Payout Attributes
[](#payout-attributes)
id string
A UUID for the payout.

sourceWalletId string
The identifier of the source wallet used to fund a payout.

destination object
The [Destination object](#source-and-destination-objects) the payout is being made to.

amount object
A [Money object](#money-object) representing the total amount that is paid to the destination.

toAmount object
A [Money object](#money-object) representing the amount that is paid to the destination currency. Only included for crypto payouts.

fees object
A [Money object](#money-object) representing fees associated with this payment.

status string
Status of the payout. pending indicates that the payout is in process; complete indicates it finished successfully; failed indicates it failed.

trackingRef string
Payout tracking reference. Only included on fiat payouts.

externalRef string
External network identifier which will be present once provided from the applicable network.

errorCode string
Indicates the failure reason of a payout. Only present for payouts in failed state. Possible values are insufficient_funds, transaction_denied, transaction_failed, and transaction_returned.

riskEvaluation object
An object with two attributes, decision and reason.

return object
A Return object if the payout was returned.

createDate string
ISO-8601 UTC date/time format.

updateDate string
ISO-8601 UTC date/time format.

##
Transfer Object
[](#transfer-object)
A transfer object represents a transfer of funds from a Circle wallet to a blockchain address, from a blockchain address to a Circle wallet, or between two Circle wallets.

###
Example Transfer Object
[](#example-transfer-object)
Transfer

{
  "id": "0d46b642-3a5f-4071-a747-4053b7df2f99",
  "source": {
    "type": "blockchain",
    "chain": "ETH",
    "address": "0x8381470ED67C3802402dbbFa0058E8871F017A6F"
  },
  "destination": {
    "type": "wallet",
    "id": "12345",
  },
  "amount": {
    "amount": "3.14",
    "currency": "USD"
  },
  "transactionHash": "0x4cebf8f90c9243a23c77e4ae20df691469e4b933b295a73376292843968f7a63",
  "status": "pending",
  "riskEvaluation" : {
    "decision": "approved",
    "reason": "Circle approved credit card"
  },
  "createDate": "2020-04-10T02:29:53.888Z"
}
###
Transfer Attributes
[](#transfer-attributes)
id string
A UUID for the transfer.

source object
A [Source object](#source-and-destination-objects) representing the source of the transfer.

destination object
A [Destination object](#source-and-destination-objects) representing the destination of the transfer.

amount object
A [Money object](#money-object) representing the amount transferred between source and destination.

transactionHash string
A hash that uniquely identifies an on-chain transaction. This is only available when either source or destination are of type blockchain.

status string
Status of the transfer. Status pending indicates that the transfer is in the process of running; complete indicates it finished successfully; failed indicates it failed.
createDate string
ISO-8601 UTC date/time format.

#
Methods and Instruments
[](#methods-and-instruments-1)
##
Wire Account Object
[](#wire-account-object)
Bank Account (Wire)

{
  "id": "e5e5109d-5a3c-47cd-93ee-9cfe054691bf",
  "type": "wire",
  "status": "complete",
  "description": "WELLS FARGO BANK, NA ****1111",
  "trackingRef": "CIR3EDXL6M",
  "virtualAccountEnabled": false,
  "fingerprint": "6177934f-0830-45ed-beb7-c67d225327a1",
  "billingDetails": {
    "name": "Satoshi Nakamoto",
    "line1": "100 Money Street",
    "line2": "Suite 1",
    "city": "Boston",
    "postalCode": "01234",
    "district": "MA",
    "country": "US"
  },
  "bankAddress": {
    "bankName": "WELLS FARGO BANK, NA",
    "city": "SAN FRANCISCO",
    "district": "CA",
    "country": "US"
  },
  "createDate": "2021-01-12T10:05:54.815Z",
  "updateDate": "2021-01-12T10:05:54.815Z"
}
##
Wallet Object
[](#wallet-object)
Wallets represent balances managed by Circle. A single wallet is designed to hold multiple balances, but only one balance per currency. As the only supported currency is "USD", all wallets today have a single balance.

Unlike other resources in our APIs, wallet IDs are shorter numerical strings and are not represented as longer UUIDs.

Wallet

{
  "walletId": "434000",
  "entityId": "fc988ed5-c129-4f70-a064-e5beb7eb8e32",
  "type": "end_user_wallet",
  "description": "Treasury Wallet",
  "balances": [
    {
      "amount": "3.14",
      "currency": "USD"
    }
  ]
}
#
Additional Resources
[](#additional-resources-1)
Below is a list of resources that relate to an original payment or payout. They represent subsequent events that happen after the initial creation or settlement of the payment/payout.

##
Cancel Object
[](#cancel-object)
A cancel object represents an attempt at canceling a payment. Cancellations apply only to card payments, and its presence doesn't necessarily mean that the cancellation was successful. A successful cancellation has a status of paid.

Cancel

{
  "id": "b783ad5e-1ace-4b41-8c0d-5bce31ae971b",
  "type": "cancel",
  "merchantId": "b3528f9f-e381-46d0-9405-31d709f95360",
  "merchantWalletId": "1000038499",
  "source": {
    "id": "619c7e7a-f406-427f-bd38-e885895f280a",
    "type": "card"
  },
  "description": "Cancel Payment",
  "amount": {
    "amount": "250.00",
    "currency": "USD"
  },
  "fees": {
    "amount": "0.00",
    "currency": "USD"
  },
  "status": "paid",
  "originalPayment": {
    "id": "765fb559-394b-4d21-972c-0423e937b0d3",
    "type": "payment",
    "status": "paid",
    "createDate": "2020-12-17T15:29:33.701Z",
    "updateDate": "2020-12-17T16:13:11.043Z"
  },
  "reason": "requested_by_customer",
  "createDate": "2020-12-22T19:58:20.722Z",
  "updateDate": "2020-12-22T20:02:15.382Z"
}
##
Refund Object
[](#refund-object)
A refund object represents an attempt at refunding a payment. Its presence doesn't mean that the refund was successful. A successful refund has a status of paid.

Refund

{
  "id": "b937c833-b7f3-48fc-bc6f-09d492eb4a47",
  "type": "refund",
  "merchantId": "aef0d056-6119-4fc1-a13a-c7ecd8682999",
  "merchantWalletId": "1000038499",
  "source": {
    "id": "94705f9a-0ae4-4fb1-b77f-6e6042150ce1",
    "type": "card"
  },
  "description": "Refund Payment",
  "amount": {
    "amount": "1.00",
    "currency": "USD"
  },
  "fees": {
    "amount": "0.00",
    "currency": "USD"
  },
  "status": "paid",
  "originalPayment": {
    "id": "5adf260a-c54b-4a4d-a548-4a51ce2280b7",
    "type": "payment",
    "status": "paid",
    "description": "Merchant Payment",
      "amount": {
        "amount": "1.00",
        "currency": "USD"
      },
      "fees": {
        "amount": "0.04",
        "currency": "USD"
      },
    "createDate": "2020-12-17T15:27:17.300Z",
    "updateDate": "2020-12-17T15:40:10.455Z",
    "merchantId": "aef0d056-6119-4fc1-a13a-c7ecd8682999",
      "merchantWalletId": "1000038499",
      "source": {
        "id": "94705f9a-0ae4-4fb1-b77f-6e6042150ce1",
        "type": "card"
      },
      "trackingRef": "11300134136681725782464"
  },
  "reason": "requested_by_customer",
  "createDate": "2021-01-05T10:34:25.797Z",
  "updateDate": "2021-01-05T10:38:16.676Z"
}
#
Nested Resources
[](#nested-resources-1)
Below is a list of resources that are commonly used in other objects.

##
Source and Destination Objects
[](#source-and-destination-objects)
Payments, payouts, and transfers reference source and destination objects, which as the names suggest, tell you where the funds are coming from and where they're going.

Sources and destinations can have the following types:

- card for card payments
- wire for wire payments and payouts
- blockchain for transfers to/from blockchain addresses
- wallet for transfers to/from a Circle wallet
Source and Destination

// "card",  "wire"
// The "id" represents the UUID of the Card or Wire object.
{
  "type": "card",
  "id": "4d260293-d17c-4309-a8da-fa7850f95c10"
}

// "blockchain"
// A "chain" and "address" together represent the blockchain location.
{
  "type": "blockchain",
  "address": "0x8381470ED67C3802402dbbFa0058E8871F017A6F",
  "chain": "ETH"
}

// "wallet"
// The "id" is the id of the Circle wallet.
{
  "type": "wallet",
  "id": "12345"
}
##
Money Object
[](#money-object)
Monetary amounts across our APIs are represented as Money objects, which consist of an amount and a currency. The only supported currency at the moment is USD, and the amount is denominated in dollars as a string with fractional cents. In the example below, the amount being represented is $3.14.

Money

{
  "amount": "3.14",
  "currency": "USD"
}
##
Blockchain Addresses
[](#blockchain-addresses)
Blockchain Address

{
  "address":"0x8381470ED67C3802402dbbFa0058E8871F017A6F",
  "currency":"USD",
  "chain":"ETH"
}
##
Merchants
[](#merchants)
Merchant

{
  "marketplaceId": "1e38dcef-a947-493a-a674-f623e4418ace",
  "merchantId": "fc988ed5-c129-4f70-a064-e5beb7eb8e32",
  "merchantWalletId": "212000",
  "name": "Merchant Inc",
  "createDate": "2020-04-10T02:13:30.000Z"
}
Updated 5 months ago


URL: https://developers.circle.com/circle-mint/docs/travel-rule-on-chain
Title: Travel Rule On-Chain
Description: undefined
Keywords: undefined

Due to FinCEN guidance regarding transfers of $3,000 or more, Circle APIs may require you to share information about your end-user to prevent the transfer from failing. According to FinCEN guidance regarding applicability of the Travel Rule to ERC-20 transfers (including USDC) on Ethereum of $3,000 or more (or matching cryptocurrency), the originating institution must transmit KYC information about the originator of a transaction to the beneficiary institution.

- For more information on these requirements and how data is securely collected and shared, please see our [FAQ](https://support.usdc.circle.com/hc/en-us/articles/6375340001812).
##
Sending Transfers (Non-Business Account)
[](#sending-transfers-non-business-account)
When sending non-business account transfers on the Ethereum or Bitcoin chain of $3,000 in value or more, the originator’s identity will be required. If you are sending a transfer on behalf of your end-user, you need to provide the identity of your end-user. Otherwise, you need to provide your company’s identity.

In technical terms, the originator’s identity is required for POST /v1/transfers when:

- destination.type: "blockchain"
- destination.chain: "ETH" or "BTC"
- amount.amount >= $3,000 in value
##📘
If the POST /v1/transfers request requires an originator's identity and the transfer does not contain an originator's identity, the transfer will fail. You will receive a webhook notification with data.status: fail, riskEvaluation.decision: denied, and riskEvaluation.reason: 3220.

##
Sending Transfers (Business Account)
[](#sending-transfers-business-account)
Although first-party transfers must comply with the Travel Rule, you do not need to provide your company’s identity each time you make a business account transfer POST /v1/businessAccount/transfers. Instead, Circle will use your company’s identity stored on file and provide it to the receiving institution as required.

##
Receiving Transfers
[](#receiving-transfers)
If yours is a certain type of regulated financial institution, you are subject to additional Travel Rule compliance requirements when receiving transfers. In this case, we will provide you with the originator’s identity for webhook notifications of type transfer in addition to GET /v1/transfers and GET /v1/transfers/{id}. However, originator data will not be associated with a transfer until it is marked with a status of complete.

-
At this time, not all sending institutions are participating in the on-chain Travel Rule requirement. Therefore, not all transfers valued at $3,000 or more will have the originating person associated.

-
If you are a financial institution and do not see the originator’s identity, please reach out to a Circle representative.

##📘
Non-bank financial institutions will not see the originator’s identity for received transfers.

##
Identities Schema
[](#identities-schema)
Field	Type	Definition
Identities.type [REQUIRED]	String [enum]: individual, business	individual - A uniquely distinguishable individual.

business - Any entity other than a natural person that can establish a permanent customer relationship with an affected entity or otherwise own property. This can include companies, foundations, anstalt, partnerships, associations and other relevantly similar entities
Identities.name [REQUIRED]	String [max length 1024]	Full name of the originator.
Identities.addresses.line1 [REQUIRED]	String [max length 1024]	Line one of the street address.

Note: If the originator is a business, this is the principal place of business address. If the originator is an individual, this is the current residential address.
identities.addresses.line2	String [max length 1024]	Line two of the street address.
Identities.addresses.city [REQUIRED]	String [max length 1024]	City portion of the address.
identities.addresses.district	String [max length 16]	State / County / Province / Region portion of the address. If the country is US or Canada district is required and should use the two-letter code for the subdivision.
Identities.addresses.country [REQUIRED]	String [max length 2]	Country portion of the address. Formatted as a two-letter country code specified in ISO 3166-1 alpha-2.
identities.addresses.postalCode [REQUIRED]	String [max length 16]	Postal / ZIP code of the address.
##
Example: POST /v1/transfers
[](#example-post-v1transfers)
Request

{
  "idempotencyKey": "ba943ff1-ca16-49b2-ba55-1057e70ca5c7",
  "source": {
    "type": "wallet",
    "id": "12345",
    "identities": [
      {
        "type": "individual",
        "name": "Satoshi Nakamoto",
        "addresses": [
          {
            "line1": "100 Money Street",
            "line2": "Suite 1",
            "city": "Boston",
            "district": "MA",
            "country": "US",
            "postalCode": "01234"
          }
        ]
      }
    ]
  },
  "destination": {
    "type": "blockchain",
    "address": "0x8381470ED67C3802402dbbFa0058E8871F017A6F",
    "chain": "ETH"
  },
  "amount": {
    "amount": "3000.00",
    "currency": "USD"
  }
}
Response

{
  "data": [
    {
      "id": "b36cbf12-6ed1-47ed-9eb9-5874f8991ca8",
      "source": {
        "type": "wallet",
        "id": "12345",
        "identities": [
          {
            "type": "individual",
            "name": "Satoshi Nakamoto",
            "addresses": [
              {
                "line1": "100 Money Street",
                "line2": "Suite 1",
                "city": "Boston",
                "district": "MA",
                "country": "US",
                "postalCode": "01234"
              }
            ]
          }
        ]
      },
      "destination": {
        "type": "blockchain",
        "address": "0x8381470ED67C3802402dbbFa0058E8871F017A6F",
        "chain": "ETH"
      },
      "amount": {
        "amount": "3000.00",
        "currency": "USD"
      },
      "transactionHash": "0x4cebf8f90c9243a23c77e4ae20df691469e4b933b295a73376292843968f7a63",
      "status": "pending",
      "createDate": "2020-04-10T02:13:30.000Z"
    }
  ]
}
##
Example: GET /v1/transfers?returnIdentities=true
[](#example-get-v1transfersreturnidentitiestrue)
returnIdentities[boolean]: Specify if you would like to see identities in the response. Restricts maximum returned items to 5. By default returnIdentities is false, resulting in the response not returning data.source.identities.

Response

{
  "data": [
    {
      "id": "b36cbf12-6ed1-47ed-9eb9-5874f8991ca8",
      "source": {
        "type": "wallet",
        "id": "12345",
        "identities": [
          {
            "type": "individual",
            "name": "Satoshi Nakamoto",
            "addresses": [
              {
                "line1": "100 Money Street",
                "line2": "Suite 1",
                "city": "Boston",
                "district": "MA",
                "country": "US",
                "postalCode": "01234"
              }
            ]
          }
        ]
      },
      "destination": {
        "type": "blockchain",
        "address": "0x8381470ED67C3802402dbbFa0058E8871F017A6F",
        "chain": "ETH"
      },
      "amount": {
        "amount": "3000.00",
        "currency": "USD"
      },
      "transactionHash": "0x4cebf8f90c9243a23c77e4ae20df691469e4b933b295a73376292843968f7a63",
      "status": "pending",
      "createDate": "2020-04-10T02:13:30.000Z"
    }
  ]
}
##
Example: GET /v1/transfers/{id}?returnIdentities=true
[](#example-get-v1transfersidreturnidentitiestrue)
returnIdentities[boolean]: Specify if you would like to see identities in the response. Restricts maximum returned items to 5. By default returnIdentities is false, resulting in the response not returning data.source.identities.

Response

{
  "data": {
    "id": "b36cbf12-6ed1-47ed-9eb9-5874f8991ca8",
    "source": {
      "type": "wallet",
      "id": "12345",
      "identities": [
        {
          "type": "individual",
          "name": "Satoshi Nakamoto",
          "addresses": [
            {
              "line1": "100 Money Street",
              "line2": "Suite 1",
              "city": "Boston",
              "district": "MA",
              "country": "US",
              "postalCode": "01234"
            }
          ]
        }
      ]
    },
    "destination": {
      "type": "blockchain",
      "address": "0x8381470ED67C3802402dbbFa0058E8871F017A6F",
      "chain": "ETH"
    },
    "amount": {
      "amount": "3000.00",
      "currency": "USD"
    },
    "transactionHash": "0x4cebf8f90c9243a23c77e4ae20df691469e4b933b295a73376292843968f7a63",
    "status": "pending",
    "createDate": "2020-04-10T02:13:30.000Z"
  }
}
##
Example: Webhook Notification
[](#example-webhook-notification)
Payload

{
  "clientId": "c60d2d5b-203c-45bb-9f6e-93641d40a599",
  "notificationType": "transfers",
  "transfer": {
    "id": "b36cbf12-6ed1-47ed-9eb9-5874f8991ca8",
    "source": {
      "type": "wallet",
      "id": "12345",
      "identities": [
        {
          "type": "individual",
          "name": "Satoshi Nakamoto",
          "addresses": [
            {
              "line1": "100 Money Street",
              "line2": "Suite 1",
              "city": "Boston",
              "district": "MA",
              "country": "US",
              "postalCode": "01234"
            }
          ]
        }
      ]
    },
    "destination": {
      "type": "blockchain",
      "address": "0x8381470ED67C3802402dbbFa0058E8871F017A6F",
      "chain": "ETH"
    },
    "amount": {
      "amount": "3000.00",
      "currency": "USD"
    },
    "transactionHash": "0x4cebf8f90c9243a23c77e4ae20df691469e4b933b295a73376292843968f7a63",
    "status": "pending",
    "createDate": "2020-04-10T02:13:30.000Z"
  }
}
Updated 7 months ago


URL: https://developers.circle.com/circle-mint/docs/api-keys
Title: API Keys
Description: undefined
Keywords: undefined

API keys are unique data strings used to authenticate a user and enable access to privileged operations on Circle APIs. All Circle APIs use API keys as the mechanism to [authenticate](/circle-mint/docs/authentication) client requests. Your API key should be kept confidential and secure at all times.

- Authentication is required for all API requests; without it, the requests will fail.
- All API requests must be made over HTTPS.
##🚧
Keep Your API Keys Safe

Because our API keys allow access to privileged operations on Circle APIs, you must keep them secure at all times.

- Ensure your API key is always stored securely.
- Never share it or record it in a publicly accessible medium (client-side code, public repositories, etc.).
Caution: If you lose secure control of your API keys, your entity may suffer financial loss.

#
Manage Your API keys
[](#manage-your-api-keys)
Use the Developer Dashboard to access and manage the API keys for your entity. Go to the Circle Mint developer dashboard and click on the API Keys tab to create, view, edit, and revoke API keys. You must be logged into the production or sandbox Developer Dashboard and be an Administrator to access the API keys page:

- Production: [https://app.circle.com/developer](https://app.circle.com/developer)
- Sandbox: [https://app-sandbox.circle.com/developer](https://app-sandbox.circle.com/developer)
Note: By default, Circle disables all product APIs in Production. Please see [Circle APIs: Sandbox to Production Transition Guide](/circle-mint/docs/sandbox-to-production-transition-guide) for more instructions on how to enable access to product APIs.

#
Types of API keys
[](#types-of-api-keys)
Circle API supports two types of API keys:

- Standard keys provide the permissions to use any of Circle APIs for which your entity has subscribed service.
- Restricted keys limit access to the product role you select during key generation.
##🚧
It is possible to create restricted keys in the Developer Dashboard before you gain business access to the endpoints for that product. As such, you may have access to a valid key that is scoped to product APIs your entity is not authorized to use. Requests to API endpoints for which you are not enabled return a 401: Unauthorized HTTP status code. In that event, contact your Circle Support Engineer to verify product access for your entity.

##📘
Restricted key permissions cannot be viewed or edited in the developer dashboard. Also, they cannot be created in the developer dashboard. To learn more about editing or creating restricted keys, please reach out to your Circle representative or [customer-support@circle.com](mailto:customer-support@circle.com).

##📘
All customers that were issued API keys prior to April 25th, 2023 were provided restricted API keys. To find out what permissions your key has, please contact your Circle representative or [customer-support@circle.com](mailto:customer-support@circle.com).

#
Create an API key
[](#create-an-api-key)
Log into Circle Mint → Developers → API keys, then:

- Select "create an API key"
- Enter Name
- Provide IP addresses for IP allowlist [OPTIONAL]
- Select "create API key"
- Copy API key
- Select X
##📘
You can have a maximum of 10 API keys per environment.

#
View API Key Details
[](#view-api-key-details)
Log into Circle Mint → Developers → API keys, then:

- Select the ellipses on the API key you want to view
- Select view details
#
Edit an API key
[](#edit-an-api-key)
Log into Circle Mint → Developers → API keys, then:

- Select the ellipsis on the API key
- Select edit
- Change name
- Change IP Allowlist IPs
- Select save
#
Revoke an API key
[](#revoke-an-api-key)
Log into Circle Mint → Developers → API keys, then:

- Select the ellipsis on the API key
- Select delete
- Type in DELETE
- Select delete button
Updated 7 months ago

-
[Authentication](/circle-mint/docs/authentication)
-
[Sandbox & Production Environments](/circle-mint/docs/circle-apis-production-sandbox-environments)

URL: https://developers.circle.com/circle-mint/docs/crypto-refunds
Title: Quickstart: Crypto Refunds
Description: undefined
Keywords: undefined

Circle’s Crypto Payments API allows you to take payments from your customers on-chain and receive settlement in USDC, BTC and ETH.From time to time, customers may need to refund or reverse the payment back to the sender/buyer after the payment has been made. Crypto refunds enables this refund use case for our customers.Some key points to note about the feature:* Allows for a full or partial refund within 30 days of the creation of payment intent.

-
Provides flexibility to fund the refund from the Circle account using either the settlement currency or payment currency (assuming there is a balance in the Circle account).

-
Refunds are linked to the original intent.

-
Payment intent information and reporting include refund information.**

##
How Crypto Refunds Work
[](#how-crypto-refunds-work)
After successfully processing a crypto payment (Please follow the [Crypto Payment Quickstart guide](https://developers.circle.com/developer/docs/crypto-payments-quickstart) if you haven’t done so yet) and receiving the settlement in your Circle account, you will have the option to initiate a refund/reversal if so needed for the payment intent. These refunds can be initiated using either the refund APIs or the Circle Account UI.

Refunds can be initiated up to 30 days after the payment intent creation for intents that have had a successful payment associated with it. These can be partial or for the full amount and supports multiple refunds within this time period.After the refund has been initiated, the payment intent status will be moved to refunded, and the payment intent will no longer accept any new incoming crypto payments.

##
Crypto Refund API
[](#crypto-refund-api)
Make sure you have a successful crypto payment for the given payment intent.

###
Refund Crypto
[](#refund-crypto)
You will be able to refund crypto payments at the payment intent level by making the following request:

Request POST /v1/paymentIntents/{id}/refund

JSON

{
  "idempotencyKey": "9aed1aab-292a-427f-aae1-e0e358fef1c9",
  "destination": {
    "chain": "ETH",
    "address": "0xcd7475eaed9ee9678cf219cec748e25aba068a69"
  },
  "amount": {
    "currency": "ETH"
  },
  "toAmount": {
    "amount": "0.000001",
    "currency": "ETH"
  }
}
Response

JSON

{
  "id": "d445342e-11c5-3060-9e91-0a93d658c075",
  "type": "refund",
  "status": "pending",
  "amount": {
    "amount": "0.000000001000000000",
    "currency": "ETH"
  },
  "createDate": "2022-12-06T14:55:01.013267Z",
  "updateDate": "2022-12-06T14:55:01.013267Z",
  "merchantId": "a49f9b1d-75e0-44a9-b8d2-4293b3f11ebd",
  "merchantWalletId": "1000563095",
  "paymentIntentId": "5a925c4e-959e-4b75-9558-6f9af3ca52af",
  "settlementAmount": {
    "amount": "0.000000001000000000",
    "currency": "ETH"
  },
  "depositAddress": {
    "chain": "ETH",
    "address": "0xcd7475eaed9ee9678cf219cec748e25aba068a69"
  }
###
List Payment Intents
[](#list-payment-intents)
Returns both crypto payments and crypto refunds associated with the payment intent.

Request GET v1/paymentIntents/{id}

Response

JSON

{
  "id":"5a925c4e-959e-4b75-9558-6f9af3ca52af",
  "amount":{
    "amount":"0.100000000000000000",
    "currency":"ETH"
  }
  "amountPaid":{
    "amount":"0.100000000000000000",
    "currency":"ETH"
  }
  "amountRefunded":{
    "amount":"0.091011010050000000",
    "currency":"ETH"
  }
  "settlementCurrency":"USD",
  "paymentMethods":[
    {
      "type":"blockchain",
      "chain":"ETH",
      "address":"0x6b20d7236bab7b62423e3fcd8530611be1cdea19"
    }
  ]
  "fees":[
    {
      "type":"blockchainLeaseFee",
      "amount":"0.000000000000000000",
      "currency":"ETH"
    }
    {
      "type":"totalPaymentFees",
      "amount":"3.34",
      "currency":"USD"
    }
  ]
  "paymentIds":[
    "ba8502e3-515f-3c3a-a409-8ab99a2e72c7"
  ]
  "refundIds":[
    "a23800d8-369b-3f1a-9ec0-d73f6d439aea",
  ]
  "timeline":[
    {
      "status":"refunded",
      "time":"2022-11-23T17:36:26.781268Z"
    }
    {
      "status":"complete",
      "context":"paid",
      "time":"2022-11-23T13:00:17.980355Z"
    }
    {
      "status":"pending",
      "time":"2022-11-23T12:57:15.726331Z"
    }
    {
      "status":"created",
      "time":"2022-11-23T12:57:12.175413Z"
    }
  ]
  "createDate":"2022-11-23T12:57:12.173979Z",
  "updateDate":"2022-12-06T14:57:18.039362Z",
  "expiresOn":"2022-11-23T13:57:15.557966Z"
}
###
Get payment information
[](#get-payment-information)
Returns payment information based on a Payment ID.

Request GET v1/payments/{id}

Response

JSON

{
  "id":"ba8502e3-515f-3c3a-a409-8ab99a2e72c7",
  "type":"refund",
  "status":"paid",
  "amount":{
    "amount":"0.100000000000000000",
    "currency":"ETH"
  },
  "fees":{
    "amount":"3.34",
    "currency":"USD"
  },
  "createDate":"2022-11-23T12:58:44.543392Z",
  "updateDate":"2022-11-23T13:00:17.772601Z",
  "merchantId":"a49f9b1d-75e0-44a9-b8d2-4293b3f11ebd",
  "merchantWalletId":"1000563095",
  "paymentIntentId":"5a925c4e-959e-4b75-9558-6f9af3ca52af",
  "settlementAmount":{
    "amount":"371.02",
    "currency":"USD"
  },
  "depositAddress":{
    "chain":"ETH",
    "address":"0x6b20d7236bab7b62423e3fcd8530611be1cdea19"
  },
  "transactionHash":"0x830050e5afbcc85e4f3”
}
The get payment endpoint will return all crypto payments and crypto refunds when passing in payment intent id in the query param.

Request GET v1/payments?paymentIntentId={paymentIntentId}

Response

JSON

{
  "id":"ba8502e3-515f-3c3a-a409-8ab99a2e72c7",
  "type":"refund",
  "status":"paid",
  "amount":{
    "amount":"0.100000000000000000",
    "currency":"ETH"
  },
  "fees":{
    "amount":"3.34",
    "currency":"USD"
  },
  "createDate":"2022-11-23T12:58:44.543392Z",
  "updateDate":"2022-11-23T13:00:17.772601Z",
  "merchantId":"a49f9b1d-75e0-44a9-b8d2-4293b3f11ebd",
  "merchantWalletId":"1000563095",
  "paymentIntentId":"5a925c4e-959e-4b75-9558-6f9af3ca52af",
  "settlementAmount":{
    "amount":"371.02",
    "currency":"USD"
  },
  "depositAddress":{
    "chain":"ETH",
    "address":"0x6b20d7236bab7b62423e3fcd8530611be1cdea19"
  },
  "transactionHash":"0x830050e5afbcc85e4f3”
}
{
  "id":"d445342e-11c5-3060-9e91-0a93d658c075",
  "type":"payment",
  "status":"paid",
  "amount":{
    "amount":"0.100000000000000000",
    "currency":"ETH"
  },
  "fees":{
    "amount":"3.34",
    "currency":"USD"
  },
  "createDate":"2022-11-23T12:58:44.543392Z",
  "updateDate":"2022-11-23T13:00:17.772601Z",
  "merchantId":"a49f9b1d-75e0-44a9-b8d2-4293b3f11ebd",
  "merchantWalletId":"1000563095",
  "paymentIntentId":"5a925c4e-959e-4b75-9558-6f9af3ca52af",
  "settlementAmount":{
    "amount":"371.02",
    "currency":"USD"
  },
  "depositAddress":{
    "chain":"ETH",
    "address":"0x6b20d7236bab7b62423e3fcd8530611be1cdea19"
  },
  "transactionHash":"0x76e3a2638e413a718673b695ce4cf5bbe”
}
###
Notes about Refund Limitations
[](#notes-about-refund-limitations)
- Refunds can’t be initiated if there is a pending crypto payment.
- Refunds can only be initiated after a complete crypto payment and within 30 days of the payment intent expiry.
- Once a refund is initiated, the payment intent state changes to “refunded”, and no further payments will be accepted on this payment intent. It is important to ensure that a refunded intent is not used for payments. In case any crypto payments are sent to a refunded intent, these will not get associated with the intent and will need to be investigated and resolved manually through the customer care team. ~~ not be associated with the payment go to unsupported funds.
##
Initiate Refund from Circle Account.
[](#initiate-refund-from-circle-account)
In addition to the APIs, Circle provides merchants with the ability to refund a crypto payment intent using the Circle Account interface.

###
Step 1: Navigate to the Payment Intent Page
[](#step-1-navigate-to-the-payment-intent-page)
Refunds can be initiated from the Payment intent detail page. Click the icon on the top right of the page (three dots) to start the Crypto refund.

**

###
Step 2: Initiating the Refund
[](#step-2-initiating-the-refund)
On the subsequent page, enter the recipient details, amount and balance and currency to fund the refund.



###
Step3: Confirm the Refund
[](#step3-confirm-the-refund)
#
After you have successfully entered the information, review and confirm the details to initiate the refund.
[](#after-you-have-successfully-entered-the-information-review-and-confirm-the-details-to-initiate-the-refund)
Note: Once the refund details have been confirmed and submitted, the refund cannot be canceled.

The refund should be viewable under the payment intent detail page after initiated.

Updated 3 months ago


URL: https://developers.circle.com/circle-mint/docs/swap-usdc-across-blockchains
Title: Quickstart: Swapping USDC Across Blockchains
Description: undefined
Keywords: undefined

Circle APIs enable you to easily transfer USDC funds from one blockchain to another. Circle uses a "private bridge" approach to act as a counter-party to the swap operation. This means that funds being swapped are temporarily kept in Circle's custody. This process is fully automated and involves no human intervention, so you don’t need to manually interact with both blockchains as usual.

In this example, we'll transfer USDC funds from the Ethereum blockchain to the Algorand blockchain. However, it makes no difference which supported chains you decide to use or in which order.

- Do NOT send real / live USDC to addresses generated in the sandbox environment, as your funds might be permanently lost.
#
1. Get a sandbox API key
[](#1-get-a-sandbox-api-key)
If you don't yet have an API key in the sandbox environment, take a few moments to [create a sandbox account](https://app-sandbox.circle.com/). (Learn more about Circle’s sandbox and APIs [here](/circle-mint/docs/getting-started-with-the-circle-apis).


[
Get an API key
](https://app-sandbox.circle.com)
#
2. Receive USDC from the Ethereum blockchain
[](#2-receive-usdc-from-the-ethereum-blockchain)
- You can transfer USDC from an external wallet on any of the [supported chains](/circle-mint/docs/supported-chains-and-currencies) into a Circle wallet you control.
Start by transferring USDC from an external Ethereum wallet.

##
Create a Wallet
[](#create-a-wallet)
First, use the command below to create a new wallet to receive the external funds via the [create wallet endpoint](/circle-mint/reference/createwallet). Be sure to create your own [idempotency key](/circle-mint/docs/a-note-on-idempotent-requests).

Terminal

# Replace ${YOUR_API_KEY} with your API key
curl -H 'Content-type: application/json' \
  -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X POST https://api-sandbox.circle.com/v1/wallets \
  --data '{"idempotencyKey": "4ddef365-f2fc-4a56-95f5-a214c84ba8f4"}'
The response should look like this:

Create wallet response

{
  "data":{
    "walletId":"1000005062",
    "entityId":"7cbdedb1-d526-46b4-af12-4162a002eb9c",
    "type":"end_user_wallet",
    "balances":[]
  }
}
##
Create a Deposit Address
[](#create-a-deposit-address)
- Because wallets are not tied to specific blockchains, you can generate deposit addresses for wallets, allowing you to receive transfers from different chains.
To create a new Ethereum deposit address, use the [generate new address endpoint](/circle-mint/reference/generateaddress). To ensure the API generates a USDC deposit address for the Ethereum blockchain, specify the origincurrency (USD) and chain (ETH) attributes. Change the chain attribute to create deposit addresses on other supported chains .

Create a deposit address for your wallet using the command below.

Terminal

# Replace ${YOUR_API_KEY} with your API key
# Replace ${WALLET_ID} with the wallet id from the previous step
curl -H 'Accept: application/json' \
  -H 'content-type: application/json' \
  -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X POST --url https://api-sandbox.circle.com/v1/wallets/${WALLET_ID}/addresses \
  --data '{"idempotencyKey": "ed9a7b3b-b45f-4262-a8aa-8dfee5a4f31b", "currency": "USD", "chain": "ETH"}'
The response should look like this:

Create address response

{
  "data":{
    "address":"0x618b24211048c6ec8b29e8129c2bbc5cf80c9f0b",
    "currency":"USD",
    "chain":"ETH"
  }
}
##📘
addressTag for Stellar

For blockchains that support destination tags or memos, you'll see an additional addressTag attribute returned with the deposit address. When present, this value is required when depositing USDC.

##
Transfer USDC
[](#transfer-usdc)
Circle's sandbox environment is connected to the Ethereum Sepolia testing network, so you can send USDC from a Sepolia wallet into your Circle hosted wallet.

##❗️
Use supported chains and currencies only

Transferring unsupported currencies using the Accounts API may result in a permanent loss of funds. You can verify the token contract details for supported currencies [here](/circle-mint/docs/supported-chains-and-currencies).

Now transfer the funds from the Ethereum blockchain. Circle's [sandbox environment](/circle-mint/docs/circle-apis-production-sandbox-environments) is connected to the [Ethereum Sepolia testing network](https://developers.circle.com/stablecoins/docs/usdc-on-testing-networks), so you can send USDC from a Sepolia wallet into your Circle hosted wallet.

- Visit [USDC on Testing Networks](https://developers.circle.com/stablecoins/docs/usdc-on-testing-networks) to learn more about support on other blockchains.
If you need USDC to send, you can obtain some USDC on the Sepolia network for testing by using a [USDC faucet](https://faucet.circle.com/). Go to [faucet.circle.com](https://faucet.circle.com/) and make sure you choose the Sepolia button. Paste the deposit (wallet) address you created above and hit Submit.

- Be very careful not to miss any characters in the deposit address.
Monitor the transfer on the blockchain by using the Sepolia Etherscan link provided by the faucet.

Alternately, you can obtain USDC by [funding a test wallet with a payment](/circle-mint/docs/funding-test-wallets) and then transferring USDC out of that wallet.

#
3. Check the Status of the Transfer
[](#3-check-the-status-of-the-transfer)
Use the [retrieve transfers endpoint](/circle-mint/reference/listtransfers) (as in the command below) to find transfers that are routed to to the wallet you created earlier.

Terminal

# Replace ${YOUR_API_KEY} with your API key
# Replace ${WALLET_ID} with the wallet id from the previous step
curl -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X GET https://api-sandbox.circle.com/v1/transfers?destinationWalletId=${WALLET_ID}
The response should look like this:

Get wallet transfers response

{
  "data":[
    {
      "id":"9f6362f4-a3d2-396e-a1eb-b628ec3a29b2",
      "source":{
        "type":"blockchain",
        "chain":"ETH"
      },
      "destination":{
        "type":"wallet",
        "id":"1000005062",
        "address":"0x618b24211048c6ec8b29e8129c2bbc5cf80c9f0b"
      },
      "amount":{
        "amount":"1.00",
        "currency":"USD"
      },
      "transactionHash":"0xbd3085c3faed933f4093a7b93419ca934fa09d62fe62115d4b3f38a4a94e3a89",
      "status":"running",
      "createDate":"2020-09-21T18:50:06.202Z"
    }
  ]
}
You can look up the transactionHash on [Etherscan's tracker on Sepolia](https://sepolia.etherscan.io).

At first, the status is shown as running because the transaction has been recently initiated. In a few minutes, once the required 30 block confirmations have been completed, the status changes to complete. At that point Circle credits the receiving wallet with the value of the transfer.

At first, the status is shown as running because the transaction has been recently initiated. In a few minutes, once the required 30 block confirmations have been completed, thestatus changes to complete. At that point Circle credits the receiving wallet with the value of the transfer.

- Refer to our [confirmations guide](/circle-mint/docs/blockchain-confirmations) to learn about block confirmation requirements for different chains.
You have successfully received USDC from the Ethereum blockchain using the Circle Wallets API.

#
4. Send USDC to the Algorand blockchain
[](#4-send-usdc-to-the-algorand-blockchain)
After the receiving portion of the swap settles, you can initiate the second portion: sending USDC funds to an address on a different blockchain using the [Create a transfer](/circle-mint/reference/createtransfer) endpoint.

- Make sure you have an Algorand blockchain address to send to as the destination.
- Circle's [sandbox environment](/circle-mint/docs/circle-apis-production-sandbox-environments) is connected to the [Algorand TestNet](https://developers.circle.com/stablecoins/docs/usdc-on-test-networks), so the destination blockchain address has to be a valid Algorand TestNet address.
##📘
Algorand Wallet TestNet Account

Download the [Algorand Wallet](https://www.algorand.com/wallet). Under Settings > Node Settings, select TestNet. From the main wallet screen, note your account address.

Your Algorand TestNet wallet address should look like this: BL27TI3CYMFX5URLYNBJJZGV4ABRYJJHBMLRR5OYSVPUVDIUAF66IR4COA.

##🚧
Opt-in to Accept the USDC Asset

Due to [Algorand Standard Assets](https://developer.algorand.org/docs/features/asa/) spam protection, you must opt in to accept new assets such as USDC. To do so, select Add New Asset, then search for "USDC" (the USDC asset ID on Algorand TestNet is [10458941](https://developers.circle.com/stablecoins/docs/usdc-on-test-networks#usdc-on-algorand-testnet)`).

##📘
ALGO Dispenser (Faucet)

To complete the opt-in, you must pay for a transaction. Obtain TestNet ALGO tokens on from the [TestNet ALGO Dispenser (Faucet)](https://bank.testnet.algorand.network/).

To send funds to the external Algorand wallet, use the [create wallet transfer endpoint](/circle-mint/reference/createtransfer). Specify the currency and chain attributes that match the destination blockchain. Create a USDC transfer to an Algorand blockchain address by using the command below.

Terminal

# Replace ${YOUR_API_KEY} with your API key
# Replace ${YOUR_WALLET_ID} with your master wallet id
curl -H 'Accept: application/json' \
  -H 'Content-type: application/json' \
  -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X POST https://api-sandbox.circle.com/v1/transfers \
  --data '{"idempotencyKey": "fbba9d81-72f1-4bf8-97c8-99f1f28995e2", "source": {"type": "wallet", "id": "${YOUR_WALLET_ID}"}, "destination": {"type": "blockchain", "address": "BL27TI3CYMFX5URLYNBJJZGV4ABRYJJHBMLRR5OYSVPUVDIUAF66IR4COA", "chain": "ALGO"}, "amount": {"amount": "1.00", "currency": "USD"}}'
##📘
addressTag for Stellar

When you create a transfer on chains like Stellar that support destination tags or memos, you can use the optionaladdressTag parameter to make sure your send is received correctly.

##📘
Flow Vaults

Prior to sending USDC to a wallet on the Flow network, make sure the wallet address is set up to receive a USDC transfer. In most cases, this involves selecting an option in the wallet’s UI to “add USDC.” See [Flow documentation](https://docs.onflow.org/cadence/tutorial/03-fungible-tokens/#transfer-tokens-to-another-user) for details.

The response should look like this:

Create wallet transfer response

{
  "data":{
    "id":"9dd86dfe-72e8-4182-9573-04196cc7d6cb",
    "source":{
      "type":"wallet",
      "id":"1000005062"
    },
    "destination":{
      "type":"blockchain",
      "address":"BL27TI3CYMFX5URLYNBJJZGV4ABRYJJHBMLRR5OYSVPUVDIUAF66IR4COA",
      "chain":"ALGO"
    },
    "amount":{
      "amount":"1.00",
      "currency":"USD"
    },
    "status":"pending",
    "createDate":"2020-09-21T20:31:39.507Z"
  }
}
#
5. Check the Status of the Transfer
[](#5-check-the-status-of-the-transfer)
Use the [get transfer endpoint](/circle-mint/reference/gettransfer) to retrieve details about the status of the transaction. You can use it as shown in the command below.

Terminal

# Replace ${YOUR_API_KEY} with your API key
# Replace ${TRANSFER_ID} with the id of the transfer you created earlier
curl -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X GET https://api-sandbox.circle.com/v1/transfers/${TRANSFER_ID}
The response should look like this:

Get wallet transfer response

{
  "data":{
    "id":"9dd86dfe-72e8-4182-9573-04196cc7d6cb",
    "source":{
      "type":"wallet",
      "id":"1000005062"
    },
    "destination":{
      "type":"blockchain",
      "address":"BL27TI3CYMFX5URLYNBJJZGV4ABRYJJHBMLRR5OYSVPUVDIUAF66IR4COA",
      "chain":"ALGO"
    },
    "amount":{
      "amount":"1.00",
      "currency":"USD"
    },
    "transactionHash":"PS74XNN7GHNQTVEV52ZMYHR3CWTKLJUFZ5U3DD43P5WO6GO5VDLQ",
    "status":"complete",
    "createDate":"2020-09-23T21:35:03.563Z"
  }
}
Algorand transactions confirm in under 5 seconds, so your transferstatus should quickly change to complete. See [Blockchain Confirmations](/circle-mint/docs/blockchain-confirmations) to find transaction times for all chains.

#
6. Ready for the next step?
[](#6-ready-for-the-next-step)
After experimenting with our APIs, you’ll want to start building test integrations in sandbox prior to moving into production. Start by applying for a [applying for a Circle Mint account](https://www.circle.com/en/circle-mint). We'll be happy to walk you through the next steps.

Updated 4 months ago


URL: https://developers.circle.com/circle-mint/docs/supported-countries
Title: Supported Countries
Description: undefined
Keywords: undefined

##
Wire Transfers for Minting and Redeeming USDC
[](#wire-transfers-for-minting-and-redeeming-usdc)
The Circle Core API supports wire transfers from/to bank accounts domiciled in the following countries:

###
Asia
[](#asia)
Armenia, Azerbaijan, Bahrain, Bangladesh, Bhutan, Bonaire, Sint Eustatius and Saba, Brunei Darussalam, Cambodia, Georgia, Hong Kong, India, Indonesia, Israel, Japan, Jordan, Kuwait, Kyrgyzstan, Malaysia, Mongolia, Oman, Philippines, Republic of Korea, Saudi Arabia, Singapore, Sri Lanka, Taiwan, Tajikistan,Thailand, Timor-Leste, Turkey, United Arab Emirates, Uzbekistan, Vietnam

###
Africa
[](#africa)
Angola, Benin, Botswana, British Indian Ocean Territory, Burkina Faso, Burundi, Cameroon, Chad, Egypt, Ethiopia, The French Southern Territories, The Gambia, Ghana, Kazakhstan, Kenya, Madagascar, Malawi, Mauritius, Mayotte, Morocco, Mozambique, Namibia, Niger, Réunion, Rwanda, Saint Helena, Ascension and Tristan da Cunha, Saint Lucia Senegal, Seychelles, South Africa, Tanzania, Tunisia, Western Sahara, Zambia, Zimbabwe

###
Europe
[](#europe)
Åland Islands, Andorra, Austria, Belgium, Bosnia and Herzegovina, Bulgaria, Croatia, Cyprus, Czechia, Denmark, Estonia, The Faroe Islands, Finland, France, Germany, Gibraltar, Greece, Guernsey, Holy See (Vatican City State), Hungary, Iceland, Ireland, Isle of Man, Italy, Jersey, Latvia, Liechtenstein, Lithuania, Luxembourg, Malta, The Republic of Moldova, Monaco, Montenegro, Republic of North Macedonia, Netherlands, Norway, Poland, Portugal, Romania, San Marino, Serbia, Slovakia, Slovenia, Spain, Svalbard and Jan Mayen, Sweden, Switzerland, United Kingdom

###
North America
[](#north-america)
Anguilla, Antigua and Barbuda, Aruba, The Bahamas, Belize, Bermuda, British Virgin Islands, Canada, Cayman Islands, Costa Rica, Curaçao, Dominicana, The Dominican Republic, El Salvador, Greenland, Grenada, Guadeloupe, Honduras, Martinique, Mexico, Montserrat, Puerto Rico, Saint Barthelemy, Saint Lucia, Saint Martin (French part), Saint Pierre and Miquelon, Saint Vincent and the Grenadines, Sint Maarten (Dutch part), The Turks and Caicos Islands, United States (excluding HI and NY), United States Minor Outlying Islands, U.S. Virgin Islands

###
South America
[](#south-america)
Argentina, Bouvet Island, Brazil, Chile, Ecuador, French Guiana, Guatemala, Guyana, Paraguay, Peru, The Falkland Islands (Malvinas), South Georgia and the South Sandwich Islands, Uruguay

###
Oceania
[](#oceania)
American Samoa, Australia, Christmas Island, The Cocos (Keeling) Islands, The Cook Islands, Federated States of Micronesia, Fiji, French Polynesia, Guam, Heard Island and McDonald Islands, Kiribati, The Marshall Islands, Nauru, New Caledonia, New Zealand, Niue, Norfolk Island, The Northern Mariana Islands, Palau, Pitcairn, Samoa, Tokelau, Tuvalu, Wallis and Futuna

###
Antarctica
[](#antarctica)
Antarctica

##🚧
Please note this list of countries continues to evolve as we onboard additional banking partners, and discrepancies may exist. If you have specific questions please reach out to our Customer Care team who is available 24/7.

##
Crypto Payments & Payouts
[](#crypto-payments--payouts)
Only companies domiciled in the following countries can use our underlying asset exchange services.

##📘
Supported Countries for Exchange Services

Canada and United States of America

Updated 3 months ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/quickstart-deposit-via-blockchain-wallet
Title: Quickstart: Deposit via Blockchain Wallet
Description: undefined
Keywords: undefined

This quickstart walks through sending USDC or EURC on any of the supported blockchain from a wallet to your Circle Mint Account.

#
1. Get an API key
[](#1-get-an-api-key)
Circle's APIs use [API keys](https://developers.circle.com/circle-mint/docs/api-keys) as the mechanism to authenticate client requests. The API key must be set in the Authorization header of the request sent from your backend server. The format of the header is Bearer secret-key-value.

To obtain an API key for the sandbox environment, simply [create an account](https://app.circle.com/get-started/create-account) and generate a new key in settings - it only takes a few seconds.

[GET AN API KEY](https://app-sandbox.circle.com/)

Once you have generated your API key, record it in a secure place.

#
2. Create a deposit address
[](#2-create-a-deposit-address)
To send funds to your Circle Mint Account on-chain, you will first need to generate a depost address. [https://developers.circle.com/circle-mint/reference/createbusinessdepositaddress](https://developers.circle.com/circle-mint/reference/createbusinessdepositaddress)

Request:

cURL

curl --request POST \\

--url https\://api-sandbox.circle.com/v1/businessAccount/wallets/addresses/deposit \\
--header 'accept: application/json' \\
--header 'authorization: Bearer SAND\_API\_KEY:' \\
--header 'content-type: application/json' \\
--data '

{
"currency": "USD",
"chain": "BASE"
}
'
Response

JSON

{
  "data": {
    "id": "d51d72d2-9955-4340-b3fd-2f07a82a1e6c",
    "address": "0xbd01242af414961c25aa72dcae06646fc52e9b92",
    "currency": "USD",
    "chain": "BASE"
  }
}
#
3. List all deposit addresses
[](#3-list-all-deposit-addresses)
Each blockchain will have its own deposit address and it is important to ensure you always use the correct address. You can always make a GET request to see all of your different deposit addresses.

In the following example make substitutions for the following placeholder keys:

- Replace ${YOUR\_API\_KEY} with your API key.
- Replace ${TRANSFER\_ID} with the ID of the transfer you created earlier.
cURL

curl -H "Authorization: Bearer ${YOUR\_API\_KEY}" \\

-X GET https\://api-sandbox.circle.com/v1/businessAccount/wallets/addresses/deposit
The Get Transfer response should look similar to the following:

JSON

{
  "data": [
    {
      "id": "d51d72d2-9955-4340-b3fd-2f07a82a1e6c",
      "address": "0xbd01242af414961c25aa72dcae06646fc52e9b92",
      "currency": "USD",
      "chain": "BASE"
    },
    {
      "id": "50f79198-4af9-466f-a235-c277d1643403",
      "address": "0xbd01242af414961c25aa72dcae06646fc52e9b92",
      "currency": "USD",
      "chain": "ARB"
    },
    {
      "id": "6d2c6c1a-91a0-49f9-b997-ce78fc9cdc3d",
      "address": "0xbd01242af414961c25aa72dcae06646fc52e9b92",
      "currency": "USD",
      "chain": "ETH"
    },
    {
      "id": "4f8489e4-e039-5e2c-ba6f-078c190dbfbc",
      "address": "0xbd01242af414961c25aa72dcae06646fc52e9b92",
      "currency": "USD",
      "chain": "MATIC"
    }
  ]
}
#
4. Deposit to your account
[](#4-deposit-to-your-account)
Blockchain deposits can only be initiated from your respective wallet so this part of the deposit process will take place outside of Circle’s API.

Using the wallet address returned in the previous step, you will send funds from your blockchain wallet to your Circle Mint Account.

[Warning] You must confirm the blockchain network is consistent with the address you requested from Circle and the wallet you’re using to make this transfer, or you could lose funds.

#
5. Check the latest balance
[](#5-check-the-latest-balance)
You can use the [list all balances endpoint](https://developers.circle.com/circle-mint/reference/listbusinessbalances) to retrieve the balance of funds available for use in your account.

In the following example make substitutions for the following placeholder keys:

- Replace ${YOUR\_API\_KEY}with your API key.
- Replace ${TRANSFER\_ID} with the ID of the transfer you created earlier.
cURL

curl -H "Authorization: Bearer ${YOUR\_API\_KEY}" \\

-X GET https\://api-sandbox.circle.com/v1/businessAccount/balances
The response should look similar to the following: this:

JSON

{
  "data": {
    "available": [
      {
        "amount": "17460.00",
        "currency": "USD"
      }
    ],
    "unsettled": []
  }
}
🎉 Congratulations. You have successfully deposited cryptocurrency from your external blockchain wallet to your Circle Mint Account.

#
6. Ready for the next step?
[](#6-ready-for-the-next-step)
After experimenting with our APIs, you’ll be better prepared to start building test integrations in sandbox, prior to moving into production. Start by [applying for a Circle Mint account](https://www.circle.com/en/circle-mint). We'll be happy to walk you through the next steps.

Updated 4 months ago


URL: https://developers.circle.com/circle-mint/docs/your-master-wallet
Title: Your Master Wallet
Description: undefined
Keywords: undefined

#
What is the Master Wallet
[](#what-is-the-master-wallet)
Your [Circle Mint account](https://www.circle.com/en/circle-mint) has a master wallet that is automatically generated for you once you sign up.

This master wallet is the destination for the settlement of payments and the origin for payouts, in case you use our Payments API or Payouts API.

#
Obtaining Your Master Wallet Id
[](#obtaining-your-master-wallet-id)
You can find your master wallet identifier by inspecting the value of property masterWalletId in the response obtained from the [configuration endpoint](/circle-mint/reference/getaccountconfig). Do so by executing the following command.

Retrieve Configuration

# Replace ${YOUR_API_KEY} with your API key
curl -H 'Accept: application/json' \
  -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X GET --url https://api-sandbox.circle.com/v1/configuration
The response from the API call above will look like the below.

Configuration Response

{
  "data":{
    "payments":{
      "masterWalletId":"0000000001"
    }
  }
}
Your masterWalletId value will not change, so it is safe to store it, though you can always retrieve it via the configuration endpoint.

Updated 4 months ago

-
[Send funds externally](/circle-mint/docs/send-funds-externally)
-
[Receive external funds](/circle-mint/docs/receive-external-funds)
-
[Wallets, Addresses and Transfers](/circle-mint/docs/wallets-addresses-and-transfers)
Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/test-card-numbers
Title: Test Card Numbers
Description: undefined
Keywords: undefined

You can use the Circle API [sandbox environment](/circle-mint/docs/circle-apis-production-sandbox-environments) to exercise various types of card payment behaviors, such as authorization failure codes and address verification results.

To get your desired behavior:

- Use one of the test numbers listed below
- Choose a specific payment amount, CVV code, or address value
#
Eligible Card Numbers
[](#eligible-card-numbers)
##🚧
No Real Charges

The [sandbox environment](/circle-mint/docs/circle-apis-production-sandbox-environments) will authorize card payments made with any card number, but it will never actually verify or charge the card.

To make payments on the sandbox environment please use one of the provided test card numbers below, using other card numbers will return an error response.

Card number	Card provider
4007400000000007	Visa
4007410000000006	Visa
4200000000000000	Visa
4757140000000001	Visa
5102420000000006	Mastercard
5173375000000006	Mastercard
5555555555554444	Mastercard
#
Response Codes
[](#response-codes)
To trigger specific response codes, use the corresponding amounts below when creating a payment. Any amount not specified in the table will return Approved (response code 00).

Amount	Response code	Description
5.01	payment_failed	Payment failed due to unspecified error
5.04	card_not_honored	Contact card issuer to query why payment failed
5.05	payment_not_supported_by_issuer	Issuer did not support the payment
5.07	payment_not_funded	Insufficient funds in account to fund payment
5.19	card_invalid	Invalid card number
5.41	card_limit_violated	Exceeded amount or frequency limits
5.43	payment_denied	Payment denied by Circle Risk Service or card processor risk controls
5.51	payment_fraud_detected	Payment suspected of being associated with fraud
5.54	credit_card_not_allowed	Issuer did not support using a credit card for payment
5.57	payment_stopped_by_issuer	A stop has been placed on the payment or card
5.84	card_account_ineligible	Ineligible account associated with card
#
CVV Verification
[](#cvv-verification)
For more information on the card verification value (CVV) check and return values see the [verifying card details guide](/circle-mint/docs/verifying-card-details).

Setting the CVV to 000 or 999 will cause a CVV failure response.

#
AVS Verification
[](#avs-verification)
For more information on the address verification service (AVS) check and the possible return codes see [verifying card details guide](/circle-mint/docs/verifying-card-details).

To trigger a particular AVS check result, set the address line 1 field to Test_X, where X is one of the available AVS return codes. The result defaults to AVS code Y otherwise.

Updated over 2 years ago

Did this page help you?

Yes

No

URL: https://developers.circle.com/circle-mint/docs/wire-virtual-account-number
Title: Wire Virtual Account Number
Description: undefined
Keywords: undefined

Circle’s Virtual Account Number is a unique bank account number assigned to customers. Customers use this number each time they send a wire to Circle, allowing Circle to attribute incoming funds accordingly. Compared to previous methods, this improves the customer experience and reduces incidents of wires returned due to missing reference IDs.

###
Virtual Account Numbers
[](#virtual-account-numbers)
Circle manages a single omnibus account number for receiving all wire payments. The way Circle identifies funds and assigns them to you is through the use of a Virtual Account Number. This unique number enables us to properly attribute those wired funds to you.

Each linked fiat account is assigned a unique Virtual Account Number. Any incoming wire payment funds are attributed to the customer wallet based on the related Virtual Account Number.

###
Benefits of Virtual Account Number
[](#benefits-of-virtual-account-number)
When customers use Virtual Account Number to make a local Wire Payment or Internal SWIFT transfer, they do not have to quote a tracking reference in the payment instruction. This eliminates one of the most common reasons for wire returns (missing or incorrect tracking reference in the incoming wire) and thereby improves the wire payment experience and success rate.

This eliminates one of the most common reasons for wire returns (missing or incorrect tracking reference in the incoming wire) and thereby improves the wire payment experience and success rate.

###
Payments supported by Virtual Account Number
[](#payments-supported-by-virtual-account-number)
All Domestic and International wire transfers (i.e.SWIFT payment) are supported by Virtual Account Number.

###
Affected endpoints
[](#affected-endpoints)
There is no impact to the existing API schemas. Instead we change the underlying field value beneficiaryBank.accountNumber to a Virtual Account Number rather than using Circle’s Omnibus account number. All other information i.e. Beneficiary Bank Name, address, SWIFT code and routing number etc. will remain unchanged.

The beneficiaryBank.accountNumber field is located within the response from a successful GET request for wire deposit instructions:

- GET: /v1/businessAccount/banks/wires/{id}/instructions
Updated 4 months ago


URL: https://developers.circle.com/circle-mint/docs/postman
Title: Postman Suite
Description: undefined
Keywords: undefined

Circle’s Postman collection is a library of templated requests to help you learn about Circle’s APIs. These requests run on Postman, an API platform for learning, building, and using APIs. The Postman Circle Mint workspace includes a collection for each of our products: API Overview, Core Functionality, Crypto Payments, Crypto Payouts, and Accounts API. The folder structure within each collection matches the organization of our [API References](/circle-mint/reference/ping).

#
How To Use It
[](#how-to-use-it)
##
Run in Postman
[](#run-in-postman)
Select one of the six Run in Postman links below. You’ll be asked whether you want to fork the collection to your workspace, view the collection in the public workspace, or import the collection into Postman.

Fork: Creates a copy of the collection while maintaining a link to the parent.
View: Allows you to quickly try out the API without having to import anything into your Postman suite.
Import: Creates a copy of the collection but does not maintain a link to Circle’s copy.

Collection	Run it!
API Overview	[](https://www.google.com/url?q=https://god.gw.postman.com/run-collection/21445022-e6b3ea60-0ff0-4919-9a58-eaf262989f82?action%3Dcollection%252Ffork%26source%3Drip_markdown%26collection-url%3DentityId%253D21445022-e6b3ea60-0ff0-4919-9a58-eaf262989f82%2526entityType%253Dcollection%2526workspaceId%253D791ff53c-d236-499c-89ea-307d24ddd289&sa=D&source=docs&ust=1708733799579019&usg=AOvVaw1J5pjlBnXW56T9Jg1Jem4R)
Core Functionality	[](https://www.google.com/url?q=https://god.gw.postman.com/run-collection/21445022-3e1635b1-7620-4001-998d-3b3aebbfc44f?action%3Dcollection%252Ffork%26source%3Drip_markdown%26collection-url%3DentityId%253D21445022-3e1635b1-7620-4001-998d-3b3aebbfc44f%2526entityType%253Dcollection%2526workspaceId%253D791ff53c-d236-499c-89ea-307d24ddd289&sa=D&source=docs&ust=1708733799581882&usg=AOvVaw2MrbS0b_WrIS2xff7v4UMs)
Crypto Deposits API	[](https://www.google.com/url?q=https://god.gw.postman.com/run-collection/21445022-27cbac71-8b44-4d50-9c83-7e39a90a7325?action%3Dcollection%252Ffork%26source%3Drip_markdown%26collection-url%3DentityId%253D21445022-27cbac71-8b44-4d50-9c83-7e39a90a7325%2526entityType%253Dcollection%2526workspaceId%253D791ff53c-d236-499c-89ea-307d24ddd289&sa=D&source=docs&ust=1708733799584756&usg=AOvVaw1lqKomXCegg8MBf7mppUOm)
Crypto Payouts API	[](https://www.google.com/url?q=https://god.gw.postman.com/run-collection/21445022-53206224-cf33-4bbd-8e8a-09d5a780795a?action%3Dcollection%252Ffork%26source%3Drip_markdown%26collection-url%3DentityId%253D21445022-53206224-cf33-4bbd-8e8a-09d5a780795a%2526entityType%253Dcollection%2526workspaceId%253D791ff53c-d236-499c-89ea-307d24ddd289&sa=D&source=docs&ust=1708733799587291&usg=AOvVaw3tZLn5_kLwqQDkwELUpt3M)
Accounts API	[](https://www.google.com/url?q=https://god.gw.postman.com/run-collection/21445022-5ce03e5e-6dba-4573-b5f3-c24ab757c6a2?action%3Dcollection%252Ffork%26source%3Drip_markdown%26collection-url%3DentityId%253D21445022-5ce03e5e-6dba-4573-b5f3-c24ab757c6a2%2526entityType%253Dcollection%2526workspaceId%253D791ff53c-d236-499c-89ea-307d24ddd289&sa=D&source=docs&ust=1708733799589735&usg=AOvVaw3Rx1JiGk1h1AEB02A--oXr)
##📘
Authorization

To authorize your session authorization, use Circle’s Postman variable apiKey and add your API key to the environment or collection variables. See Postman’s [using variables](https://learning.postman.com/docs/sending-requests/variables/) for details.

Need a Circle sandbox API Key? Sign up for a [Circle account](https://app-sandbox.circle.com/signup)—it only takes a minute or two.



Updated 2 months ago


URL: https://developers.circle.com/circle-mint/docs/testing-connectivity-and-api-keys
Title: Using sandbox to test connectivity and API keys
Description: undefined
Keywords: undefined

Circle’s sandbox environment is a development tool that allows you to safely test prototypes and integration without generating actual financial transactions. Sandbox APIs match those in production, making it easy to move into the production phase.

To ensure success, please note the following:

- Before running these tests, make sure that you have a [sandbox account](https://app-sandbox.circle.com) and an API key.
##
Testing Raw Connectivity
[](#testing-raw-connectivity)
To test raw connectivity, hit the [service health API endpoint](/circle-mint/reference/ping) by running the command below.

cURL
JavaScript

curl -H 'Accept: application/json' \
  -X GET --url https://api-sandbox.circle.com/ping
##👍
Successful Response

If you reached the API endpoint, you’ll see the response
{"message" : "pong"}.

##
Testing Your API Key
[](#testing-your-api-key)
Verify that your API key is set up correctly by running the command below to hit a read endpoint (in this case, the [configuration endpoint](/circle-mint/reference/getaccountconfig)).

cURL
JavaScript

curl -H 'Accept: application/json' \
  -H "Authorization: Bearer ${YOUR_API_KEY}" \
  -X GET --url https://api-sandbox.circle.com/v1/configuration
##👍
Successful Response


{"data":{"payments":{"masterWalletId":"1234567890"}}}
##❗️
Error Response


{"code":401,"message":"Malformed authorization. Are the credentials properly encoded?"}
Updated 5 months ago

Did this page help you?

Yes

No
