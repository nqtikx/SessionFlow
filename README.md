# Session Flow API

Session API is an entry point for SDK-oriented exchange flows.
It prepares pre-calculated exchange context (rates, fees, limits), returns `sessionId`, and provides ready-to-open SDK URL parameters.

> BASE_URL https://api.dev.wbdevel.net

## Supported Assets

### Fiat
- `BYN`
- `RUB`
- `USD`
- `EUR`

### Crypto
- `BTC`
- `ETH`
- `TRX`
- `TON`
- `USDT_ERC`
- `USDC_ERC`
- `USDT_TRC`
- `USDT_TON`

## Supported Payment Providers

### Belarus
- `ALFA` (`BYN`, `RUB`, `USD`, `EUR`)
- `ASSIST` (`BYN`, `RUB`, `USD`, `EUR`)
- `STATUSBANK` (`BYN`, `RUB`, `USD`, `EUR`)
- `CA` (`BYN`, `RUB`, `USD`, `EUR`)

### Russia
- `SBER` (`RUB`)
- `CARUSELL` (`RUB`)
- `MTS` (`RUB`)
- `VTB` (`RUB`)
- `CA` (`BYN`, `RUB`, `USD`, `EUR`)

### Tajikistan
- `CORTI_MILLI` (`RUB`)

## 1) Session Endpoints

### Step 1. Create session
Use this endpoint to create a pre-calculated exchange session for SDK flow without creating an order yet.
Use the response `sessionId` and `sdk.url` to start the client flow and track it later.

**POST** `/api/v3/exchange/merchant/session`

**Headers**
`x-api-key: {{x-api-key}}`

**Request**
```json
{
  "fromAsset": "BYN",
  "fromAmount": 100,
  "toAsset": "USDT_TRC",
  "destinationCryptoAddress": "TCT2pKJXo233hrKWQMeCptC8My1KGvtsU4",
  "externalClientId": "externalClientId"
}
```

**Response**
```json
{
    "sessionId": "6fe3c52c-329d-419f-ab95-9c8a24546301",
    "externalClientId": "externalClientId",
    "destinationCryptoAddress": "TCT2pKJXo233hrKWQMeCptC8My1KGvtsU4",
    "comment": null,
    "exchange": {
        "fromAsset": "USD",
        "fromGrossAmount": "20",
        "fromNetAmount": "19.5",
        "toAsset": "TRX",
        "toGrossAmount": "56.472632",
        "toNetAmount": "56.209632",
        "actualRate": "0.3558",
        "exchangeRate": "0.3453",
        "fees": [
            {
                "type": "PERCENT",
                "amount": "-0.5",
                "asset": "USD"
            },
            {
                "type": "CRYPTO_FEE",
                "amount": "-0.263",
                "asset": "TRX"
            }
        ]
    },
    "limit": {
        "min": "5",
        "max": "10000"
    },
    "sdk": {
        "url": "https://sdk.dev.wbdevel.net/v2.0/?mode=LoginMode&merchantId=...&externalClientId=...&sessionId=...&currencyFrom=USD&currencyTo=TRX&disableCurrencyFrom=false&disableCurrencyTo=false&showBackButtonOnHomePage=false&currencyAmount=20&cryptoWallet=..."
    }
}
```

### Headers

<table width="100%">
  <thead>
    <tr>
      <th width="200" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Type</th>
      <th width="100">Required</th>
      <th width="580">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">x-api-key</td>
      <td>string</td>
      <td>Yes</td>
      <td>Authenticates the merchant server-to-server request. Use the API key issued for the merchant and target environment.</td>
    </tr>
  </tbody>
</table>

### Request

<table width="100%">
  <thead>
    <tr>
      <th width="200" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Type</th>
      <th width="100">Required</th>
      <th width="580">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">fromAsset</td>
      <td>string</td>
      <td>Yes</td>
      <td>Source asset code.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">fromAmount</td>
      <td>number</td>
      <td>Conditionally</td>
      <td>Set either fromAmount or toAmount, but not both.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">toAsset</td>
      <td>string</td>
      <td>Yes</td>
      <td>Target asset code.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">toAmount</td>
      <td>number</td>
      <td>Conditionally</td>
      <td>Set either fromAmount or toAmount, but not both.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">paymentMethod</td>
      <td>string</td>
      <td>No</td>
      <td>Payment provider id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">destinationCryptoAddress</td>
      <td>string</td>
      <td>Yes</td>
      <td>Destination wallet address for crypto-out flows (used when output.type is CRYPTO_TRANSFER).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">comment</td>
      <td>string</td>
      <td>No</td>
      <td>Used only for the TON network as a transfer memo for the recipient. For other networks the value is ignored.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">externalClientId</td>
      <td>string</td>
      <td>Yes</td>
      <td>Merchant-side external client identifier.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">redirectUrl</td>
      <td>string</td>
      <td>No</td>
      <td>Redirect URL for SDK flow.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">email</td>
      <td>string</td>
      <td>No</td>
      <td>Optional prefilled email for SDK.</td>
    </tr>
  </tbody>
</table>

### Response

<table width="100%">
  <thead>
    <tr>
      <th width="240" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Type</th>
      <th width="640">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">sessionId</td>
      <td>string</td>
      <td>Session identifier used for tracking and downstream flow.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">externalClientId</td>
      <td>string</td>
      <td>Merchant-side external client identifier from request.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">destinationCryptoAddress</td>
      <td>string</td>
      <td>Destination wallet address for crypto-out flows (used when output.type is CRYPTO_TRANSFER).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">comment</td>
      <td>string | null</td>
      <td>Used only for the TON network as a transfer memo for the recipient. For other networks the value is ignored.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">exchange</td>
      <td>object</td>
      <td>Pre-calculated exchange values block.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">exchange.fromAsset</td>
      <td>string</td>
      <td>Source asset code.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">exchange.fromGrossAmount</td>
      <td>number</td>
      <td>Source amount before fees.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">exchange.fromNetAmount</td>
      <td>number</td>
      <td>Source amount after input-side fees.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">exchange.toAsset</td>
      <td>string</td>
      <td>Target asset code.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">exchange.toGrossAmount</td>
      <td>number</td>
      <td>Target amount before output-side fees.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">exchange.toNetAmount</td>
      <td>number</td>
      <td>Target amount after output-side fees.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">exchange.actualRate</td>
      <td>number</td>
      <td>Final client-facing rate.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">exchange.exchangeRate</td>
      <td>number</td>
      <td>Base/system exchange rate.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">exchange.fees</td>
      <td>array of objects</td>
      <td>Fee breakdown list.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">exchange.fees[].type</td>
      <td>string</td>
      <td>Fee type, e.g. PERCENT, CRYPTO_FEE.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">exchange.fees[].amount</td>
      <td>number</td>
      <td>Fee amount value in the asset specified by exchange.fees[].asset.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">exchange.fees[].asset</td>
      <td>string</td>
      <td>Asset code used for this fee.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">limit</td>
      <td>object</td>
      <td>Limit block for current session pair.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">limit.min</td>
      <td>number</td>
      <td>Minimum allowed amount.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">limit.max</td>
      <td>number</td>
      <td>Maximum allowed amount.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">sdk</td>
      <td>object</td>
      <td>SDK launch parameters block.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">sdk.url</td>
      <td>string</td>
      <td>Ready-to-open SDK URL for current session.</td>
    </tr>
  </tbody>
</table>

### Errors

<table width="100%">
  <thead>
    <tr>
      <th width="240" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Code</th>
      <th width="640">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">400 INVALID_AMOUNTS</td>
      <td>BUSINESS</td>
      <td>Amount is not specified, or both amounts are provided at once.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">400 INVALID_DESTINATION_CRYPTO_ADDRESS</td>
      <td>BUSINESS</td>
      <td>Destination crypto address is invalid.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">400 DESTINATION_ADDRESS_IS_INTERNAL</td>
      <td>BUSINESS</td>
      <td>Destination address belongs to internal address space.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">400 INVALID_PAYMENT_PROVIDER</td>
      <td>BUSINESS</td>
      <td>Payment provider not found or unsupported for current route.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">401 Unauthorized</td>
      <td>HTTP</td>
      <td>x-api-key is missing, invalid, or expired.</td>
    </tr>
  </tbody>
</table>

### Step 2. Get buy limit by fiat asset and provider
Use this endpoint to retrieve min/max fiat amount boundaries for buy flow by asset and payment provider.
Use the response to validate amount input before creating session/order.

**POST** `/api/v2/exchange/merchant/buy-limit`

**Headers**
`x-api-key: {{x-api-key}}`

**Request**
```json
{
  "asset": "RUB",
  "paymentMethod": "CARUSELL"
}
```

**Response**
```json
{
  "asset": { "code": "RUB" },
  "min": 947.37,
  "max": 3735526.32
}
```

### Headers

<table width="100%">
  <thead>
    <tr>
      <th width="200" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Type</th>
      <th width="100">Required</th>
      <th width="580">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">x-api-key</td>
      <td>string</td>
      <td>Yes</td>
      <td>Authenticates the merchant server-to-server request. Use the API key issued for the merchant and target environment.</td>
    </tr>
  </tbody>
</table>

### Request

<table width="100%">
  <thead>
    <tr>
      <th width="200" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Type</th>
      <th width="100">Required</th>
      <th width="580">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">asset</td>
      <td>string</td>
      <td>Yes</td>
      <td>Fiat asset code.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">paymentMethod</td>
      <td>string</td>
      <td>Yes</td>
      <td>Payment provider code.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">clientId</td>
      <td>string</td>
      <td>No</td>
      <td>Merchant client id, if limit is client-context dependent.</td>
    </tr>
  </tbody>
</table>

### Response

<table width="100%">
  <thead>
    <tr>
      <th width="240" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Type</th>
      <th width="640">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">asset</td>
      <td>object</td>
      <td>Asset for which limit values are returned.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">asset.code</td>
      <td>string</td>
      <td>Asset code.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">min</td>
      <td>number</td>
      <td>Minimum allowed amount.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">max</td>
      <td>number</td>
      <td>Maximum allowed amount.</td>
    </tr>
  </tbody>
</table>

### Errors

<table width="100%">
  <thead>
    <tr>
      <th width="240" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Code</th>
      <th width="640">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">400 INVALID_PAYMENT_PROVIDER</td>
      <td>BUSINESS</td>
      <td>Payment provider not found or unsupported.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">401 Unauthorized</td>
      <td>HTTP</td>
      <td>x-api-key is missing, invalid, or expired.</td>
    </tr>
  </tbody>
</table>

## 2) Order Tracking Endpoints

### Step 3. Get current order by client reference
Use this endpoint to retrieve currently active order for client context.
Use the response to restore session/order state in SDK integration.

**GET** `/api/v3/exchange/merchant/order/current`

**Headers**
`x-api-key: {{x-api-key}}`

**Parameters**
`clientId: {{clientId}}`

**Response**
```json
{
    "id": "7f0ec09a-2ea7-410b-bc5c-09e94ec2ccb1",
    "number": 911000004333,
    "conditions": {
        "fromAsset": "BYN",
        "toAsset": "TRX",
        "fromGrossAmount": "50",
        "fromNetAmount": "46.65",
        "fromFeeAmount": "3.35",
        "toGrossAmount": "44.838524",
        "toNetAmount": "44.838524",
        "toFeeAmount": "0",
        "promoCode": null,
        "rate": "TRX/BYN",
        "systemRateValue": "1.0404",
        "exchangeRateValue": "1.0404",
        "actualRateValue": "1.1151"
    },
    "recalculationReason": null,
    "clientId": "3e1469fa-8d35-441c-87b1-a007aeba2562",
    "status": "PROCESSING",
    "failureMessage": null,
    "completionDate": null,
    "creationDate": "2026-05-06T17:12:11+0000",
    "sessionId": null,
    "input": {
        "type": "FIAT_PROVIDER",
        "asset": "BYN",
        "amount": "50",
        "transactionAmount": "50",
        "feeAmount": "3.35",
        "status": "PROCESSING",
        "failureMessage": null,
        "expirationDate": null,
        "provider": "ASSIST",
        "paymentType": "P2P",
        "processingBank": "BELARUSBANK",
        "clientBank": null,
        "fromToken": "fc4b130e-c3bf-4a3d-abe5-9ec5900c9868",
        "toToken": "50d53e2e-f086-472a-9ce2-cdcee43279cb",
        "link": "https://payments.t.paysecure.ru/pay/p2p/cc2mc.cfm...",
        "processorTransactionId": "36b7f4d1d15849deb1002e471fdd219a",
        "post": null,
        "paymentSystem": null,
        "processorTransactionNumber": null
    },
    "output": {
        "type": "INTERNAL_BALANCE",
        "asset": "TRX",
        "amount": "44.838524",
        "transactionAmount": "44.838524",
        "feeAmount": "0",
        "status": "NEW",
        "failureMessage": null,
        "expirationDate": null
    }
}
```

### Headers

<table width="100%">
  <thead>
    <tr>
      <th width="200" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Type</th>
      <th width="100">Required</th>
      <th width="580">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">x-api-key</td>
      <td>string</td>
      <td>Yes</td>
      <td>Authenticates the merchant server-to-server request. Use the API key issued for the merchant and target environment.</td>
    </tr>
  </tbody>
</table>

### Params

<table width="100%">
  <thead>
    <tr>
      <th width="200" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Type</th>
      <th width="100">Required</th>
      <th width="580">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">clientId</td>
      <td>string</td>
      <td>Conditionally</td>
      <td>Required when externalClientId query param is not provided.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">externalClientId</td>
      <td>string</td>
      <td>Conditionally</td>
      <td>Required when clientId query param is not provided.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">destination</td>
      <td>string</td>
      <td>No</td>
      <td>Optional destination filter. Recommended value: EXCHANGE.</td>
    </tr>
  </tbody>
</table>

### Response

<table width="100%">
  <thead>
    <tr>
      <th width="240" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Type</th>
      <th width="640">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">id</td>
      <td>string</td>
      <td>Order id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">number</td>
      <td>number</td>
      <td>Human-readable order number.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">conditions</td>
      <td>object</td>
      <td>Rate block used in order.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">conditions.fromAsset</td>
      <td>string</td>
      <td>Source asset id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">conditions.toAsset</td>
      <td>string</td>
      <td>Target asset id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">conditions.fromGrossAmount</td>
      <td>number</td>
      <td>Source amount before source-side fee.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">conditions.fromNetAmount</td>
      <td>number</td>
      <td>Source amount after source-side fee.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">conditions.fromFeeAmount</td>
      <td>number</td>
      <td>Source-side fee amount in conditions.fromAsset currency.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">conditions.toGrossAmount</td>
      <td>number</td>
      <td>Target amount before target-side fee.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">conditions.toNetAmount</td>
      <td>number</td>
      <td>Target amount after target-side fee.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">conditions.toFeeAmount</td>
      <td>number</td>
      <td>Target-side fee amount in conditions.toAsset currency.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">conditions.promoCode</td>
      <td>string | null</td>
      <td>Promo code used in order conditions.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">conditions.rate</td>
      <td>string</td>
      <td>Rate pair code.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">conditions.systemRateValue</td>
      <td>number</td>
      <td>Base system rate at the moment of quote calculation. Used as a reference value.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">conditions.exchangeRateValue</td>
      <td>number</td>
      <td>Rate used by the exchange engine to calculate the quote.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">conditions.actualRateValue</td>
      <td>number</td>
      <td>Final client-facing rate applied to the quote/order. Show this value to the client.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">recalculationReason</td>
      <td>string | null</td>
      <td>Recalculation reason enum value, if order was recalculated.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">clientId</td>
      <td>string</td>
      <td>Internal client id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">sessionId</td>
      <td>string | null</td>
      <td>Session id linked to this order.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">status</td>
      <td>string</td>
      <td>Current order status. Allowed values: PROCESSING, EXPIRED, COMPLETED, FAILED.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">failureMessage</td>
      <td>string | null</td>
      <td>Technical/business failure reason for failed flow.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">completionDate</td>
      <td>string | null</td>
      <td>Completion timestamp.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">creationDate</td>
      <td>string</td>
      <td>Creation timestamp.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input</td>
      <td>object</td>
      <td>Input operation details.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.type</td>
      <td>string</td>
      <td>Input operation type.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.asset</td>
      <td>string</td>
      <td>Input asset code.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.amount</td>
      <td>number</td>
      <td>Input amount.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.transactionAmount</td>
      <td>number | null</td>
      <td>Provider/blockchain transaction amount.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.feeAmount</td>
      <td>number | null</td>
      <td>Input-side operation fee amount in input.asset currency.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.status</td>
      <td>string | null</td>
      <td>Operation status. Allowed values: NEW, PROCESSING, EXPIRED, COMPLETED, FAILED.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.failureMessage</td>
      <td>string | null</td>
      <td>Operation failure details.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.expirationDate</td>
      <td>string | null</td>
      <td>Operation expiration timestamp.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.provider</td>
      <td>string | null</td>
      <td>Fiat-provider specific field (FIAT_PROVIDER).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.paymentType</td>
      <td>string | null</td>
      <td>Fiat-provider payment type.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.processingBank</td>
      <td>string | null</td>
      <td>Provider processing bank name.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.clientBank</td>
      <td>string | null</td>
      <td>Client bank name.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.fromToken</td>
      <td>string | null</td>
      <td>Source payment token id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.toToken</td>
      <td>string | null</td>
      <td>Destination payment token id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.link</td>
      <td>string | null</td>
      <td>Provider payment/deeplink URL.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.processorTransactionId</td>
      <td>string | null</td>
      <td>Provider transaction id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.post</td>
      <td>string | null</td>
      <td>Masked card/post value from token metadata.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.paymentSystem</td>
      <td>string | null</td>
      <td>Payment system brand.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.processorTransactionNumber</td>
      <td>string | null</td>
      <td>Provider transaction display number.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.fromAddress</td>
      <td>string | null</td>
      <td>Source crypto address (CRYPTO_TRANSFER).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.toAddress</td>
      <td>string | null</td>
      <td>Destination crypto address (CRYPTO_TRANSFER).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.comment</td>
      <td>string | null</td>
      <td>Crypto transfer comment/tag.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">input.hash</td>
      <td>string | null</td>
      <td>Blockchain transaction hash.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output</td>
      <td>object</td>
      <td>Output operation details.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.type</td>
      <td>string</td>
      <td>Output operation type.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.asset</td>
      <td>string</td>
      <td>Output asset code.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.amount</td>
      <td>number</td>
      <td>Output amount.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.transactionAmount</td>
      <td>number | null</td>
      <td>Provider/blockchain transaction amount.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.feeAmount</td>
      <td>number | null</td>
      <td>Output-side operation fee amount in output.asset currency.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.status</td>
      <td>string | null</td>
      <td>Operation status. Allowed values: NEW, PROCESSING, EXPIRED, COMPLETED, FAILED.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.failureMessage</td>
      <td>string | null</td>
      <td>Operation failure details.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.expirationDate</td>
      <td>string | null</td>
      <td>Operation expiration timestamp.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.provider</td>
      <td>string | null</td>
      <td>Fiat-provider specific field (FIAT_PROVIDER).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.paymentType</td>
      <td>string | null</td>
      <td>Fiat-provider payment type.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.processingBank</td>
      <td>string | null</td>
      <td>Provider processing bank name.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.clientBank</td>
      <td>string | null</td>
      <td>Client bank name.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.fromToken</td>
      <td>string | null</td>
      <td>Source payment token id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.toToken</td>
      <td>string | null</td>
      <td>Destination payment token id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.link</td>
      <td>string | null</td>
      <td>Provider payment/deeplink URL.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.processorTransactionId</td>
      <td>string | null</td>
      <td>Provider transaction id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.post</td>
      <td>string | null</td>
      <td>Masked card/post value from token metadata.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.paymentSystem</td>
      <td>string | null</td>
      <td>Payment system brand.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.processorTransactionNumber</td>
      <td>string | null</td>
      <td>Provider transaction display number.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.fromAddress</td>
      <td>string | null</td>
      <td>Source crypto address (CRYPTO_TRANSFER).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.toAddress</td>
      <td>string | null</td>
      <td>Destination crypto address (CRYPTO_TRANSFER).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.comment</td>
      <td>string | null</td>
      <td>Crypto transfer comment/tag.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">output.hash</td>
      <td>string | null</td>
      <td>Blockchain transaction hash.</td>
    </tr>
  </tbody>
</table>

### Errors

<table width="100%">
  <thead>
    <tr>
      <th width="240" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Code</th>
      <th width="640">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">401 Unauthorized</td>
      <td>HTTP</td>
      <td>x-api-key is missing, invalid, or expired.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">400 Invalid external client id</td>
      <td>BUSINESS</td>
      <td>External client id is invalid for merchant scope.</td>
    </tr>
  </tbody>
</table>

### Step 4. Get order history with filters
Use this endpoint to retrieve paginated order history for client/session filters.
Use the response to build order history UI, analytics, and reconciliation flows.

**POST** `/api/v3/exchange/merchant/order/history`

**Headers**
`x-api-key: {{x-api-key}}`

**Request**
```json
{
    "clientIds": [
        "{{clientId}}"
    ],
    "operationTypes": [
        "FIAT_PROVIDER",
        "CRYPTO_TRANSFER"
    ],
    "statuses": [
        "PROCESSING",
        "COMPLETED",
        "FAILED"
    ],
    "assets": [
        "BYN",
        "RUB",
        "USD",
    ],
    "destinations": [
        "EXCHANGE"
    ]
}
```

**Response**
```json
{
  "content": [
        {
            "id": "f8e67902-6dd3-4554-8f50-cd0a5c8a894b",
            "number": 491000004334,
            "conditions": {
                "fromAsset": "BYN",
                "toAsset": "TRX",
                "fromGrossAmount": "50",
                "fromNetAmount": "46.65",
                "fromFeeAmount": "3.35",
                "toGrossAmount": "44.955189",
                "toNetAmount": "44.692189",
                "toFeeAmount": "0.263",
                "promoCode": null,
                "rate": "TRX/BYN",
                "systemRateValue": "1.0377",
                "exchangeRateValue": "1.0377",
                "actualRateValue": "1.1188"
            },
            "recalculationReason": "NONE",
            "clientId": "3e1469fa-8d35-441c-87b1-a007aeba2562",
            "status": "COMPLETED",
            "failureMessage": null,
            "completionDate": "2026-05-06T17:47:28+0000",
            "creationDate": "2026-05-06T17:44:59+0000",
            "sessionId": null,
            "input": {
                "type": "FIAT_PROVIDER",
                "asset": "BYN",
                "amount": "50",
                "transactionAmount": "50",
                "feeAmount": "3.35",
                "status": "COMPLETED",
                "failureMessage": null,
                "expirationDate": null,
                "provider": "ASSIST",
                "paymentType": "P2P",
                "processingBank": "BELARUSBANK",
                "clientBank": null,
                "fromToken": "fc4b130e-c3bf-4a3d-abe5-9ec5900c9868",
                "toToken": "97fe9aa7-7805-438f-8c5e-aea24b4f9dc4",
                "link": "https://payments.t.paysecure.ru/pay/p2p/cc2mc.cfm...",
                "processorTransactionId": "97e3d1f09aed4442a793fb5eace5582b",
                "post": null,
                "paymentSystem": null,
                "processorTransactionNumber": null
            },
            "output": {
                "type": "CRYPTO_TRANSFER",
                "asset": "TRX",
                "amount": "44.955189",
                "transactionAmount": "44.692189",
                "feeAmount": "0.263",
                "status": "COMPLETED",
                "failureMessage": null,
                "expirationDate": null,
                "fromAddress": "TVEwq1PiFDfJKYvWiDFhVXQkzDwqWCyPXV",
                "toAddress": "TCT2pKJXo233hrKWQMeCptC8My1KGvtsU4",
                "comment": null,
                "hash": "8bd6f85d606d9511d28471a91b72dce634b61b51b4bcef45fb5c29f5e4d94875"
            }
        },
  ],
  "pageable": {
        "sort": {
            "unsorted": false,
            "sorted": true,
            "empty": false
        },
        "pageNumber": 0,
        "pageSize": 10,
        "offset": 0,
        "paged": true,
        "unpaged": false
    },
    "totalElements": 50,
    "totalPages": 5,
    "last": false,
    "numberOfElements": 10,
    "size": 10,
    "number": 0,
    "sort": {
        "unsorted": false,
        "sorted": true,
        "empty": false
    },
    "first": true,
    "empty": false
}
```

### Headers

<table width="100%">
  <thead>
    <tr>
      <th width="200" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Type</th>
      <th width="100">Required</th>
      <th width="580">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">x-api-key</td>
      <td>string</td>
      <td>Yes</td>
      <td>Authenticates the merchant server-to-server request. Use the API key issued for the merchant and target environment.</td>
    </tr>
  </tbody>
</table>

### Request

<table width="100%">
  <thead>
    <tr>
      <th width="200" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Type</th>
      <th width="100">Required</th>
      <th width="580">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">externalClientId</td>
      <td>string</td>
      <td>No</td>
      <td>External client id filter.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">sessionIds</td>
      <td>array of string</td>
      <td>No</td>
      <td>Filter by session ids (UUID).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">clientIds</td>
      <td>array of string</td>
      <td>Conditionally</td>
      <td>Required when externalClientId is not provided (first value is used for merchant access validation).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">statuses</td>
      <td>array of string</td>
      <td>No</td>
      <td>Filter by order status. Allowed values: PROCESSING, EXPIRED, COMPLETED, FAILED.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">creationDateFrame</td>
      <td>object</td>
      <td>No</td>
      <td>Creation date range filter.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">numbers</td>
      <td>array of number</td>
      <td>No</td>
      <td>Filter by order numbers.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">orderIds</td>
      <td>array of string</td>
      <td>No</td>
      <td>Filter by order ids (UUID).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">inputAssets</td>
      <td>array of string</td>
      <td>No</td>
      <td>Filter by input asset ids.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">outputAssets</td>
      <td>array of string</td>
      <td>No</td>
      <td>Filter by output asset ids.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">assets</td>
      <td>array of string</td>
      <td>No</td>
      <td>Asset filter applied to either source or destination leg.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">inputOperationTypes</td>
      <td>array of string</td>
      <td>No</td>
      <td>Filter by input operation type enum values.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">outputOperationTypes</td>
      <td>array of string</td>
      <td>No</td>
      <td>Filter by output operation type enum values.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">operationTypes</td>
      <td>array of string</td>
      <td>No</td>
      <td>Filter by any operation type enum values.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">recalculationReasons</td>
      <td>array of string</td>
      <td>No</td>
      <td>Filter by recalculation reason enum values.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">completionDateFrame</td>
      <td>object</td>
      <td>No</td>
      <td>Completion date range filter.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">fiatTransactionProviders</td>
      <td>array of string</td>
      <td>No</td>
      <td>Filter by fiat provider ids.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">cryptoTransactionAddresses</td>
      <td>array of string</td>
      <td>No</td>
      <td>Filter by crypto addresses.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">cryptoTransactionHashes</td>
      <td>array of string</td>
      <td>No</td>
      <td>Filter by transaction hashes.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">merchantIds</td>
      <td>array of string</td>
      <td>No</td>
      <td>Filter by merchant ids (merchant-admin context).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">inputOperationStatuses</td>
      <td>array of object</td>
      <td>No</td>
      <td>Input operation status filters.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">inputOperationStatuses[].status</td>
      <td>string</td>
      <td>No</td>
      <td>Input operation status enum.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">inputOperationStatuses[].processingStatus</td>
      <td>string</td>
      <td>No</td>
      <td>Provider/internal processing status.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">outputOperationStatuses</td>
      <td>array of object</td>
      <td>No</td>
      <td>Output operation status filters.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">outputOperationStatuses[].status</td>
      <td>string</td>
      <td>No</td>
      <td>Output operation status enum.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">outputOperationStatuses[].processingStatus</td>
      <td>string</td>
      <td>No</td>
      <td>Provider/internal processing status.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">operationStatuses</td>
      <td>array of object</td>
      <td>No</td>
      <td>Generic operation status filters.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">operationStatuses[].status</td>
      <td>string</td>
      <td>No</td>
      <td>Operation status enum.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">operationStatuses[].processingStatus</td>
      <td>string</td>
      <td>No</td>
      <td>Provider/internal processing status.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">inputTransactionStatuses</td>
      <td>array of string</td>
      <td>No</td>
      <td>Input transaction status enum values.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">outputTransactionStatuses</td>
      <td>array of string</td>
      <td>No</td>
      <td>Output transaction status enum values.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">transactionStatuses</td>
      <td>array of string</td>
      <td>No</td>
      <td>Generic transaction status enum values.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">operationAccountTypes</td>
      <td>array of string</td>
      <td>No</td>
      <td>Account type enum values.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">replicated</td>
      <td>boolean</td>
      <td>No</td>
      <td>Replication flag filter.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">fiatProcessorTransactionIds</td>
      <td>array of string</td>
      <td>No</td>
      <td>Filter by fiat processor transaction ids.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">arrested</td>
      <td>boolean</td>
      <td>No</td>
      <td>Arrested order flag filter.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">inputAmount</td>
      <td>object</td>
      <td>No</td>
      <td>Input amount range filter.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">inputAmount.from</td>
      <td>number</td>
      <td>No</td>
      <td>Input amount lower bound.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">inputAmount.to</td>
      <td>number</td>
      <td>No</td>
      <td>Input amount upper bound.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">outputAmount</td>
      <td>object</td>
      <td>No</td>
      <td>Output amount range filter.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">outputAmount.from</td>
      <td>number</td>
      <td>No</td>
      <td>Output amount lower bound.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">outputAmount.to</td>
      <td>number</td>
      <td>No</td>
      <td>Output amount upper bound.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">destinations</td>
      <td>array of string</td>
      <td>No</td>
      <td>Destination enum values. Recommended value for SDK flows: EXCHANGE.</td>
    </tr>
  </tbody>
</table>

### Response

<table width="100%">
  <thead>
    <tr>
      <th width="240" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Type</th>
      <th width="640">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content</td>
      <td>array of objects</td>
      <td>Page content list.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].id</td>
      <td>string</td>
      <td>Order id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].number</td>
      <td>number</td>
      <td>Order number.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].conditions</td>
      <td>object</td>
      <td>Conditions block.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].conditions.fromAsset</td>
      <td>string</td>
      <td>Source asset id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].conditions.toAsset</td>
      <td>string</td>
      <td>Target asset id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].conditions.fromGrossAmount</td>
      <td>number</td>
      <td>Source amount before source-side fee.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].conditions.fromNetAmount</td>
      <td>number</td>
      <td>Source amount after source-side fee.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].conditions.fromFeeAmount</td>
      <td>number</td>
      <td>Source-side fee amount in content[].conditions.fromAsset currency.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].conditions.toGrossAmount</td>
      <td>number</td>
      <td>Target amount before target-side fee.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].conditions.toNetAmount</td>
      <td>number</td>
      <td>Target amount after target-side fee.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].conditions.toFeeAmount</td>
      <td>number</td>
      <td>Target-side fee amount in content[].conditions.toAsset currency.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].conditions.promoCode</td>
      <td>string | null</td>
      <td>Promo code used in order conditions.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].conditions.rate</td>
      <td>string</td>
      <td>Rate pair code.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].conditions.systemRateValue</td>
      <td>number</td>
      <td>System/base rate value.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].conditions.exchangeRateValue</td>
      <td>number</td>
      <td>Exchange rate value.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].conditions.actualRateValue</td>
      <td>number</td>
      <td>Actual final rate value.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].recalculationReason</td>
      <td>string | null</td>
      <td>Recalculation reason enum value.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input</td>
      <td>object</td>
      <td>Input operation block.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.type</td>
      <td>string</td>
      <td>Input operation type.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.asset</td>
      <td>string</td>
      <td>Input asset code.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.amount</td>
      <td>number</td>
      <td>Input amount.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.transactionAmount</td>
      <td>number | null</td>
      <td>Provider/blockchain transaction amount.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.feeAmount</td>
      <td>number | null</td>
      <td>Input-side operation fee amount in content[].input.asset currency.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.status</td>
      <td>string | null</td>
      <td>Operation status. Allowed values: NEW, PROCESSING, EXPIRED, COMPLETED, FAILED.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.failureMessage</td>
      <td>string | null</td>
      <td>Operation failure details.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.expirationDate</td>
      <td>string | null</td>
      <td>Operation expiration timestamp.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.provider</td>
      <td>string | null</td>
      <td>Fiat-provider specific field (FIAT_PROVIDER).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.paymentType</td>
      <td>string | null</td>
      <td>Fiat-provider payment type.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.processingBank</td>
      <td>string | null</td>
      <td>Provider processing bank name.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.clientBank</td>
      <td>string | null</td>
      <td>Client bank name.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.fromToken</td>
      <td>string | null</td>
      <td>Source payment token id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.toToken</td>
      <td>string | null</td>
      <td>Destination payment token id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.link</td>
      <td>string | null</td>
      <td>Provider payment/deeplink URL.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.processorTransactionId</td>
      <td>string | null</td>
      <td>Provider transaction id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.post</td>
      <td>string | null</td>
      <td>Masked card/post value from token metadata.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.paymentSystem</td>
      <td>string | null</td>
      <td>Payment system brand.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.processorTransactionNumber</td>
      <td>string | null</td>
      <td>Provider transaction display number.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.fromAddress</td>
      <td>string | null</td>
      <td>Source crypto address (CRYPTO_TRANSFER).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.toAddress</td>
      <td>string | null</td>
      <td>Destination crypto address (CRYPTO_TRANSFER).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.comment</td>
      <td>string | null</td>
      <td>Crypto transfer comment/tag.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].input.hash</td>
      <td>string | null</td>
      <td>Blockchain transaction hash.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output</td>
      <td>object</td>
      <td>Output operation block.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.type</td>
      <td>string</td>
      <td>Output operation type.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.asset</td>
      <td>string</td>
      <td>Output asset code.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.amount</td>
      <td>number</td>
      <td>Output amount.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.transactionAmount</td>
      <td>number | null</td>
      <td>Provider/blockchain transaction amount.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.feeAmount</td>
      <td>number | null</td>
      <td>Output-side operation fee amount in content[].output.asset currency.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.status</td>
      <td>string | null</td>
      <td>Operation status. Allowed values: NEW, PROCESSING, EXPIRED, COMPLETED, FAILED.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.failureMessage</td>
      <td>string | null</td>
      <td>Operation failure details.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.expirationDate</td>
      <td>string | null</td>
      <td>Operation expiration timestamp.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.provider</td>
      <td>string | null</td>
      <td>Fiat-provider specific field (FIAT_PROVIDER).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.paymentType</td>
      <td>string | null</td>
      <td>Fiat-provider payment type.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.processingBank</td>
      <td>string | null</td>
      <td>Provider processing bank name.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.clientBank</td>
      <td>string | null</td>
      <td>Client bank name.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.fromToken</td>
      <td>string | null</td>
      <td>Source payment token id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.toToken</td>
      <td>string | null</td>
      <td>Destination payment token id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.link</td>
      <td>string | null</td>
      <td>Provider payment/deeplink URL.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.processorTransactionId</td>
      <td>string | null</td>
      <td>Provider transaction id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.post</td>
      <td>string | null</td>
      <td>Masked card/post value from token metadata.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.paymentSystem</td>
      <td>string | null</td>
      <td>Payment system brand.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.processorTransactionNumber</td>
      <td>string | null</td>
      <td>Provider transaction display number.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.fromAddress</td>
      <td>string | null</td>
      <td>Source crypto address (CRYPTO_TRANSFER).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.toAddress</td>
      <td>string | null</td>
      <td>Destination crypto address (CRYPTO_TRANSFER).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.comment</td>
      <td>string | null</td>
      <td>Crypto transfer comment/tag.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].output.hash</td>
      <td>string | null</td>
      <td>Blockchain transaction hash.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].clientId</td>
      <td>string</td>
      <td>Internal client id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].sessionId</td>
      <td>string | null</td>
      <td>Related session id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].status</td>
      <td>string</td>
      <td>Order status value. Allowed values: PROCESSING, EXPIRED, COMPLETED, FAILED.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].failureMessage</td>
      <td>string | null</td>
      <td>Failure details, if any.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].completionDate</td>
      <td>string | null</td>
      <td>Completion timestamp.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">content[].creationDate</td>
      <td>string</td>
      <td>Creation timestamp.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">pageable</td>
      <td>object</td>
      <td>Paging metadata block.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">pageable.sort</td>
      <td>object</td>
      <td>Sort metadata for current page request.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">pageable.sort.unsorted</td>
      <td>boolean</td>
      <td>Whether paging sort is unsorted.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">pageable.sort.sorted</td>
      <td>boolean</td>
      <td>Whether paging sort is applied.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">pageable.sort.empty</td>
      <td>boolean</td>
      <td>Whether sort metadata is empty.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">pageable.pageNumber</td>
      <td>number</td>
      <td>Current page number.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">pageable.pageSize</td>
      <td>number</td>
      <td>Current page size.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">pageable.offset</td>
      <td>number</td>
      <td>Current page offset.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">pageable.paged</td>
      <td>boolean</td>
      <td>Indicates paged request mode.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">pageable.unpaged</td>
      <td>boolean</td>
      <td>Indicates unpaged request mode.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">totalElements</td>
      <td>number</td>
      <td>Total number of matching orders.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">totalPages</td>
      <td>number</td>
      <td>Total number of pages.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">last</td>
      <td>boolean</td>
      <td>true when current page is last page.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">numberOfElements</td>
      <td>number</td>
      <td>Number of elements in current page.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">number</td>
      <td>number</td>
      <td>Current page number.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">size</td>
      <td>number</td>
      <td>Page size.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">sort</td>
      <td>object</td>
      <td>Sort metadata for response page.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">sort.unsorted</td>
      <td>boolean</td>
      <td>Whether page content is unsorted.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">sort.sorted</td>
      <td>boolean</td>
      <td>Whether page content is sorted.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">sort.empty</td>
      <td>boolean</td>
      <td>Whether sort metadata is empty.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">first</td>
      <td>boolean</td>
      <td>true when current page is first page.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">empty</td>
      <td>boolean</td>
      <td>true when content array is empty.</td>
    </tr>
  </tbody>
</table>

### Errors

<table width="100%">
  <thead>
    <tr>
      <th width="240" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Code</th>
      <th width="640">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">401 Unauthorized</td>
      <td>HTTP</td>
      <td>x-api-key is missing, invalid, or expired.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">400 Client id is required</td>
      <td>BUSINESS</td>
      <td>Neither client id nor external client id is provided.</td>
    </tr>
  </tbody>
</table>

## 3) Provider Discovery Endpoint

### Step 5. Get available payment providers
Use this endpoint to fetch providers and payment system routes for current merchant/client context.
Use the response to select provider and render allowed direction/currency combinations in UI.

**POST** `/api/v2/exchange/merchant/payment/provider`

**Headers**
`x-api-key: {{x-api-key}}`

**Request**
```json
{
    "clientId": "{{clientId}}"
}
```

**Response**
```json
[
  {
        "id": "MTS",
        "name": "MTS",
        "addPaymentMethod": true,
        "config": {
            "paymentSystems": [
                {
                    "paymentSystem": "MIR",
                    "type": "PSP",
                    "directions": [
                        {
                            "direction": "SELL",
                            "currencies": [
                                {
                                    "currency": "RUB",
                                    "countries": [
                                        "Russia"
                                    ]
                                }
                            ]
                        }
                    ]
                }
            ]
        },
        "commissions": [
            {
                "buyCommission": "2,5",
                "sellCommission": "2,0"
            },
            {
                "destination": "EXCHANGE",
                "buyCommission": "2,5"
            },
            {
                "destination": "SDK_EXCHANGE",
                "buyCommission": "2,5",
                "sellCommission": "2,0"
            },
            {
                "destination": "ACCOUNTING",
                "buyCommission": "0"
            },
            {
                "bank": "RF_CARDS",
                "destination": "EXCHANGE",
                "sellCommission": "2,0"
            },
            {
                "bank": "RF_CARDS",
                "destination": "ACCOUNTING",
                "sellCommission": "1,5"
            }
        ]
    }
]
```

### Headers

<table width="100%">
  <thead>
    <tr>
      <th width="200" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Type</th>
      <th width="100">Required</th>
      <th width="580">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">x-api-key</td>
      <td>string</td>
      <td>Yes</td>
      <td>Authenticates the merchant server-to-server request. Use the API key issued for the merchant and target environment.</td>
    </tr>
  </tbody>
</table>

### Request

<table width="100%">
  <thead>
    <tr>
      <th width="200" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Type</th>
      <th width="100">Required</th>
      <th width="580">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">clientId</td>
      <td>string</td>
      <td>No</td>
      <td>Merchant client id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">fiatAsset</td>
      <td>string</td>
      <td>No</td>
      <td>Fiat asset filter.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">orderType</td>
      <td>string</td>
      <td>No</td>
      <td>Direction filter (BUY/SELL).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">destination</td>
      <td>string</td>
      <td>No</td>
      <td>Optional destination filter. Recommended value: EXCHANGE.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">providers</td>
      <td>array of string</td>
      <td>No</td>
      <td>Explicit provider filter list.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">isCrypto</td>
      <td>boolean</td>
      <td>No</td>
      <td>Crypto-method filter.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">countryGroup</td>
      <td>array of string</td>
      <td>No</td>
      <td>Country-group filter.</td>
    </tr>
  </tbody>
</table>

### Response

<table width="100%">
  <thead>
    <tr>
      <th width="240" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Type</th>
      <th width="640">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">id</td>
      <td>string</td>
      <td>Provider id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">name</td>
      <td>string</td>
      <td>Provider display name.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">addPaymentMethod</td>
      <td>boolean</td>
      <td>Whether provider supports payment-method binding.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">config</td>
      <td>object</td>
      <td>Provider routing configuration.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">config.paymentSystems</td>
      <td>array of objects</td>
      <td>Payment systems list.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">config.paymentSystems[].paymentSystem</td>
      <td>string</td>
      <td>Payment system name.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">config.paymentSystems[].type</td>
      <td>string</td>
      <td>Provider channel type.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">config.paymentSystems[].directions</td>
      <td>array of objects</td>
      <td>Supported directions.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">config.paymentSystems[].directions[].direction</td>
      <td>string</td>
      <td>Direction value.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">config.paymentSystems[].directions[].currencies</td>
      <td>array of objects</td>
      <td>Supported currencies.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">config.paymentSystems[].directions[].currencies[].currency</td>
      <td>string</td>
      <td>Currency code.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">config.paymentSystems[].directions[].currencies[].banks</td>
      <td>array of string</td>
      <td>Allowed banks for the currency, if configured.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">config.paymentSystems[].directions[].currencies[].countries</td>
      <td>array of string</td>
      <td>Optional country restrictions.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">commissions</td>
      <td>array of objects</td>
      <td>Commission settings.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">commissions[].bank</td>
      <td>string | null</td>
      <td>Optional bank scope for commission rule.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">commissions[].destination</td>
      <td>string | null</td>
      <td>Optional destination scope for commission rule. Recommended value for SDK flow: EXCHANGE.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">commissions[].buyCommission</td>
      <td>string</td>
      <td>Buy-side commission percentage (or percentage range) in string format defined by provider config.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">commissions[].sellCommission</td>
      <td>string</td>
      <td>Sell-side commission percentage (or percentage range) in string format defined by provider config.</td>
    </tr>
  </tbody>
</table>

### Errors

<table width="100%">
  <thead>
    <tr>
      <th width="240" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Code</th>
      <th width="640">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">401 Unauthorized</td>
      <td>HTTP</td>
      <td>x-api-key is missing, invalid, or expired.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">400 Invalid external user id</td>
      <td>BUSINESS</td>
      <td>External client id validation failed in merchant scope.</td>
    </tr>
  </tbody>
</table>

## 4) Webhooks

Possible session-related order events:
- `order.processing`
- `order.completed`
- `order.expired`
- `order.failed`

Example:
```json
{
  "id": "webhook-id",
  "type": "order.processing",
  "createdAt": "2024-05-23T08:44:58+0000",
  "sessionId": "3c0130a4-06f2-4d18-bf39-27153caff6f5",
  "orderId": "e57e77fc-802c-4c0d-8c7c-08806c159725",
  "externalClientId": "external-client-id-5"
}
```

### Response

<table width="100%">
  <thead>
    <tr>
      <th width="240" style="word-break: break-word; white-space: normal;">Name</th>
      <th width="120">Type</th>
      <th width="640">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="word-break: break-word; white-space: normal;">id</td>
      <td>string</td>
      <td>Webhook event id.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">type</td>
      <td>string</td>
      <td>Event type (order.processing, order.completed, order.expired, order.failed).</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">createdAt</td>
      <td>string</td>
      <td>Event creation timestamp.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">sessionId</td>
      <td>string</td>
      <td>Session id related to the order event.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">orderId</td>
      <td>string</td>
      <td>Order id related to the event.</td>
    </tr>
    <tr>
      <td style="word-break: break-word; white-space: normal;">externalClientId</td>
      <td>string</td>
      <td>Merchant external client identifier from order context.</td>
    </tr>
  </tbody>
</table>

## 5) Quote/Rate Notes

Use these fields in UI:
- Show `actualRate` (or `actualRateValue`) to the client as the final client-facing rate.
- Treat `exchangeRate` / `exchangeRateValue` as engine/reference rate used in calculation.
- Fee values are returned with explicit asset fields; always interpret fee amount in its corresponding asset currency.
