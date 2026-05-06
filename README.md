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
  "sessionId": "14500600-0631-46c4-9ae1-fab9e4c798f8",
  "externalClientId": "external-client-id-2",
  "destinationCryptoAddress": "TCT2pKJXo233hrKWQMeCptC8My1KGvtsU4",
  "comment": null,
  "exchange": {
    "fromAsset": "USD",
    "fromGrossAmount": 20,
    "fromNetAmount": 19.5,
    "toAsset": "TRX",
    "toGrossAmount": 68.783069,
    "toNetAmount": 68.520069,
    "actualRate": 0.2919,
    "exchangeRate": 0.2835,
    "fees": [
      { "type": "PERCENT", "amount": -0.5, "asset": "USD" },
      { "type": "CRYPTO_FEE", "amount": -0.263, "asset": "TRX" }
    ]
  },
  "limit": {
    "min": 11.67,
    "max": 13333.33
  },
  "sdk": {
    "url": "https://sdk.dev.wbdevel.net/v2.0/?mode=LoginMode&merchantId=...&externalClientId=...&sessionId=..."
  }
}
```

### Headers

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `x-api-key` | `string` | Yes | Authenticates merchant backend request. |

### Request

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `fromAsset` | `string` | Yes | Source asset code. |
| `fromAmount` | `number` | Conditionally | Set either `fromAmount` or `toAmount`, but not both. |
| `toAsset` | `string` | Yes | Target asset code. |
| `toAmount` | `number` | Conditionally | Set either `fromAmount` or `toAmount`, but not both. |
| `paymentMethod` | `string` | No | Payment provider id. |
| `destinationCryptoAddress` | `string` | Yes | Destination crypto wallet address. |
| `comment` | `string` | No | Optional crypto transfer comment. |
| `externalClientId` | `string` | Yes | Merchant-side external client identifier. |
| `redirectUrl` | `string` | No | Redirect URL for SDK flow. |
| `email` | `string` | No | Optional prefilled email for SDK. |

### Response

| Name | Type | Description |
| --- | --- | --- |
| `sessionId` | `string` | Session identifier used for tracking and downstream flow. |
| `externalClientId` | `string` | Merchant-side external client identifier from request. |
| `destinationCryptoAddress` | `string` | Destination crypto wallet used by session. |
| `comment` | `string \| null` | Optional comment from request. |
| `exchange` | `object` | Pre-calculated exchange values block. |
| `exchange.fromAsset` | `string` | Source asset code. |
| `exchange.fromGrossAmount` | `number` | Source amount before fees. |
| `exchange.fromNetAmount` | `number` | Source amount after input-side fees. |
| `exchange.toAsset` | `string` | Target asset code. |
| `exchange.toGrossAmount` | `number` | Target amount before output-side fees. |
| `exchange.toNetAmount` | `number` | Target amount after output-side fees. |
| `exchange.actualRate` | `number` | Final client-facing rate. |
| `exchange.exchangeRate` | `number` | Base/system exchange rate. |
| `exchange.fees` | `array of objects` | Fee breakdown list. |
| `exchange.fees[].type` | `string` | Fee type, e.g. `PERCENT`, `CRYPTO_FEE`. |
| `exchange.fees[].amount` | `number` | Fee amount value. |
| `exchange.fees[].asset` | `string` | Asset code used for this fee. |
| `limit` | `object` | Limit block for current session pair. |
| `limit.min` | `number` | Minimum allowed amount. |
| `limit.max` | `number` | Maximum allowed amount. |
| `sdk` | `object` | SDK launch parameters block. |
| `sdk.url` | `string` | Ready-to-open SDK URL for current session. |

### Errors

| Name | Code | Description |
| --- | --- | --- |
| `400 INVALID_AMOUNTS` | BUSINESS | Amount is not specified, or both amounts are provided at once. |
| `400 INVALID_DESTINATION_CRYPTO_ADDRESS` | BUSINESS | Destination crypto address is invalid. |
| `400 DESTINATION_ADDRESS_IS_INTERNAL` | BUSINESS | Destination address belongs to internal address space. |
| `400 INVALID_PAYMENT_PROVIDER` | BUSINESS | Payment provider not found or unsupported for current route. |
| `401 Unauthorized` | HTTP | `x-api-key` is missing, invalid, or expired. |

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
  "asset": { "id": "RUB", "code": "RUB" },
  "min": 947.37,
  "max": 3735526.32
}
```

### Headers

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `x-api-key` | `string` | Yes | Authenticates merchant backend request. |

### Request

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `asset` | `string` | Yes | Fiat asset code. |
| `paymentMethod` | `string` | Yes | Payment provider code. |
| `clientId` | `string` | No | Merchant client id, if limit is client-context dependent. |

### Response

| Name | Type | Description |
| --- | --- | --- |
| `asset` | `object` | Asset for which limit values are returned. |
| `asset.code` | `string` | Asset code. |
| `min` | `number` | Minimum allowed amount. |
| `max` | `number` | Maximum allowed amount. |

### Errors

| Name | Code | Description |
| --- | --- | --- |
| `400 INVALID_PAYMENT_PROVIDER` | BUSINESS | Payment provider not found or unsupported. |
| `401 Unauthorized` | HTTP | `x-api-key` is missing, invalid, or expired. |

## 2) Order Tracking Endpoints

### Step 3. Get current order by client reference
Use this endpoint to retrieve currently active order for client context.
Use the response to restore session/order state in SDK integration.

**GET** `/api/v3/exchange/merchant/order/current`

**Headers**
`x-api-key: {{x-api-key}}`

**Response**
```json
{
  "id": "5289b6f0-4945-4b74-b243-4fad013eed50",
  "number": 9210086,
  "conditions": {
    "rate": "TRX/BYN",
    "systemRateValue": 1.2,
    "exchangeRateValue": 1.2,
    "actualRateValue": 1.24409
  },
  "sessionId": "14500600-0631-46c4-9ae1-fab9e4c798f8",
  "status": "COMPLETED",
  "input": {
    "type": "FIAT_PROVIDER",
    "asset": "BYN",
    "amount": 50
  },
  "output": {
    "type": "CRYPTO_TRANSFER",
    "asset": "TRX",
    "amount": 40.190074
  }
}
```

### Headers

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `x-api-key` | `string` | Yes | Authenticates merchant backend request. |

### Params

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `clientId` | `string` | Conditionally | Required when `externalClientId` query param is not provided. |
| `externalClientId` | `string` | Conditionally | Required when `clientId` query param is not provided. |
| `destination` | `string` | No | Destination filter, default value is `EXCHANGE`. |

### Response

| Name | Type | Description |
| --- | --- | --- |
| `id` | `string` | Order id. |
| `number` | `number` | Human-readable order number. |
| `conditions` | `object` | Rate block used in order. |
| `conditions.fromAsset` | `string` | Source asset id. |
| `conditions.toAsset` | `string` | Target asset id. |
| `conditions.fromGrossAmount` | `number` | Source amount before source-side fee. |
| `conditions.fromNetAmount` | `number` | Source amount after source-side fee. |
| `conditions.fromFeeAmount` | `number` | Source-side fee amount. |
| `conditions.toGrossAmount` | `number` | Target amount before target-side fee. |
| `conditions.toNetAmount` | `number` | Target amount after target-side fee. |
| `conditions.toFeeAmount` | `number` | Target-side fee amount. |
| `conditions.promoCode` | `string \| null` | Promo code used in order conditions. |
| `conditions.rate` | `string` | Rate pair code. |
| `conditions.systemRateValue` | `number` | System/base rate value. |
| `conditions.exchangeRateValue` | `number` | Exchange rate value. |
| `conditions.actualRateValue` | `number` | Actual final rate value. |
| `recalculationReason` | `string \| null` | Recalculation reason enum value, if order was recalculated. |
| `clientId` | `string` | Internal client id. |
| `sessionId` | `string` | Session id linked to this order. |
| `status` | `string` | Current order status. |
| `failureMessage` | `string \| null` | Technical/business failure reason for failed flow. |
| `completionDate` | `string \| null` | Completion timestamp. |
| `creationDate` | `string` | Creation timestamp. |
| `input` | `object` | Input operation details. |
| `input.type` | `string` | Input operation type. |
| `input.asset` | `string` | Input asset code. |
| `input.amount` | `number` | Input amount. |
| `input.transactionAmount` | `number \| null` | Provider/blockchain transaction amount. |
| `input.feeAmount` | `number \| null` | Input-side operation fee amount. |
| `input.status` | `string \| null` | Operation status. |
| `input.failureMessage` | `string \| null` | Operation failure details. |
| `input.expirationDate` | `string \| null` | Operation expiration timestamp. |
| `input.provider` | `string \| null` | Fiat-provider specific field (`FIAT_PROVIDER`). |
| `input.paymentType` | `string \| null` | Fiat-provider payment type. |
| `input.processingBank` | `string \| null` | Provider processing bank name. |
| `input.clientBank` | `string \| null` | Client bank name. |
| `input.fromToken` | `string \| null` | Source payment token id. |
| `input.toToken` | `string \| null` | Destination payment token id. |
| `input.link` | `string \| null` | Provider payment/deeplink URL. |
| `input.processorTransactionId` | `string \| null` | Provider transaction id. |
| `input.post` | `string \| null` | Masked card/post value from token metadata. |
| `input.paymentSystem` | `string \| null` | Payment system brand. |
| `input.processorTransactionNumber` | `string \| null` | Provider transaction display number. |
| `input.fromAddress` | `string \| null` | Source crypto address (`CRYPTO_TRANSFER`). |
| `input.toAddress` | `string \| null` | Destination crypto address (`CRYPTO_TRANSFER`). |
| `input.comment` | `string \| null` | Crypto transfer comment/tag. |
| `input.hash` | `string \| null` | Blockchain transaction hash. |
| `output` | `object` | Output operation details. |
| `output.type` | `string` | Output operation type. |
| `output.asset` | `string` | Output asset code. |
| `output.amount` | `number` | Output amount. |
| `output.transactionAmount` | `number \| null` | Provider/blockchain transaction amount. |
| `output.feeAmount` | `number \| null` | Output-side operation fee amount. |
| `output.status` | `string \| null` | Operation status. |
| `output.failureMessage` | `string \| null` | Operation failure details. |
| `output.expirationDate` | `string \| null` | Operation expiration timestamp. |
| `output.provider` | `string \| null` | Fiat-provider specific field (`FIAT_PROVIDER`). |
| `output.paymentType` | `string \| null` | Fiat-provider payment type. |
| `output.processingBank` | `string \| null` | Provider processing bank name. |
| `output.clientBank` | `string \| null` | Client bank name. |
| `output.fromToken` | `string \| null` | Source payment token id. |
| `output.toToken` | `string \| null` | Destination payment token id. |
| `output.link` | `string \| null` | Provider payment/deeplink URL. |
| `output.processorTransactionId` | `string \| null` | Provider transaction id. |
| `output.post` | `string \| null` | Masked card/post value from token metadata. |
| `output.paymentSystem` | `string \| null` | Payment system brand. |
| `output.processorTransactionNumber` | `string \| null` | Provider transaction display number. |
| `output.fromAddress` | `string \| null` | Source crypto address (`CRYPTO_TRANSFER`). |
| `output.toAddress` | `string \| null` | Destination crypto address (`CRYPTO_TRANSFER`). |
| `output.comment` | `string \| null` | Crypto transfer comment/tag. |
| `output.hash` | `string \| null` | Blockchain transaction hash. |

### Errors

| Name | Code | Description |
| --- | --- | --- |
| `401 Unauthorized` | HTTP | `x-api-key` is missing, invalid, or expired. |
| `400 Invalid external client id` | BUSINESS | External client id is invalid for merchant scope. |

### Step 4. Get order history with filters
Use this endpoint to retrieve paginated order history for client/session filters.
Use the response to build order history UI, analytics, and reconciliation flows.

**POST** `/api/v3/exchange/merchant/order/history`

**Headers**
`x-api-key: {{x-api-key}}`

**Request**
```json
{
  "externalClientId": "external-client-id-1",
  "sessionIds": ["14500600-0631-46c4-9ae1-fab9e4c798f8"],
  "statuses": ["COMPLETED"]
}
```

**Response**
```json
{
  "content": [
    {
      "id": "5289b6f0-4945-4b74-b243-4fad013eed50",
      "number": 9210086,
      "sessionId": "14500600-0631-46c4-9ae1-fab9e4c798f8",
      "status": "COMPLETED"
    }
  ],
  "totalElements": 1,
  "totalPages": 1,
  "number": 0,
  "size": 10
}
```

### Headers

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `x-api-key` | `string` | Yes | Authenticates merchant backend request. |

### Request

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `externalClientId` | `string` | No | External client id filter. |
| `sessionIds` | `array of string` | No | Filter by session ids (`UUID`). |
| `clientIds` | `array of string` | Conditionally | Used to resolve merchant client when `externalClientId` is not provided (first value is used). |
| `clientId` | `string` | Conditionally | Alternative client filter. |
| `statuses` | `array of string` | No | Order status filter list. |
| `creationDateFrame` | `object` | No | Creation date range filter object. |
| `numbers` | `array of number` | No | Filter by order numbers. |
| `orderIds` | `array of string` | No | Filter by order ids (`UUID`). |
| `inputAssets` | `array of string` | No | Filter by input asset ids. |
| `outputAssets` | `array of string` | No | Filter by output asset ids. |
| `assets` | `array of string` | No | Filter by any operation asset ids. |
| `inputOperationTypes` | `array of string` | No | Filter by input operation type enum values. |
| `outputOperationTypes` | `array of string` | No | Filter by output operation type enum values. |
| `operationTypes` | `array of string` | No | Filter by any operation type enum values. |
| `recalculationReasons` | `array of string` | No | Filter by recalculation reason enum values. |
| `completionDateFrame` | `object` | No | Completion date range filter object. |
| `fiatTransactionProviders` | `array of string` | No | Filter by fiat provider ids. |
| `cryptoTransactionAddresses` | `array of string` | No | Filter by crypto addresses. |
| `cryptoTransactionHashes` | `array of string` | No | Filter by transaction hashes. |
| `merchantIds` | `array of string` | No | Filter by merchant ids (merchant-admin context). |
| `inputOperationStatuses` | `array of object` | No | Input operation status filters. |
| `inputOperationStatuses[].status` | `string` | No | Input operation status enum. |
| `inputOperationStatuses[].processingStatus` | `string` | No | Provider/internal processing status. |
| `outputOperationStatuses` | `array of object` | No | Output operation status filters. |
| `outputOperationStatuses[].status` | `string` | No | Output operation status enum. |
| `outputOperationStatuses[].processingStatus` | `string` | No | Provider/internal processing status. |
| `operationStatuses` | `array of object` | No | Generic operation status filters. |
| `operationStatuses[].status` | `string` | No | Operation status enum. |
| `operationStatuses[].processingStatus` | `string` | No | Provider/internal processing status. |
| `inputTransactionStatuses` | `array of string` | No | Input transaction status enum values. |
| `outputTransactionStatuses` | `array of string` | No | Output transaction status enum values. |
| `transactionStatuses` | `array of string` | No | Generic transaction status enum values. |
| `operationAccountTypes` | `array of string` | No | Account type enum values. |
| `replicated` | `boolean` | No | Replication flag filter. |
| `fiatProcessorTransactionIds` | `array of string` | No | Filter by fiat processor transaction ids. |
| `arrested` | `boolean` | No | Arrested order flag filter. |
| `inputAmount` | `object` | No | Input amount range filter. |
| `inputAmount.from` | `number` | No | Input amount lower bound. |
| `inputAmount.to` | `number` | No | Input amount upper bound. |
| `outputAmount` | `object` | No | Output amount range filter. |
| `outputAmount.from` | `number` | No | Output amount lower bound. |
| `outputAmount.to` | `number` | No | Output amount upper bound. |
| `destinations` | `array of string` | No | Destination enum values. |

### Response

| Name | Type | Description |
| --- | --- | --- |
| `content` | `array of objects` | Page content list. |
| `content[].id` | `string` | Order id. |
| `content[].number` | `number` | Order number. |
| `content[].conditions` | `object` | Conditions block (same structure as Step 3 response). |
| `content[].recalculationReason` | `string \| null` | Recalculation reason enum value. |
| `content[].input` | `object` | Input operation block (same structure as Step 3 response). |
| `content[].output` | `object` | Output operation block (same structure as Step 3 response). |
| `content[].clientId` | `string` | Internal client id. |
| `content[].sessionId` | `string` | Related session id. |
| `content[].status` | `string` | Order status value. |
| `content[].failureMessage` | `string \| null` | Failure details, if any. |
| `content[].completionDate` | `string \| null` | Completion timestamp. |
| `content[].creationDate` | `string` | Creation timestamp. |
| `totalElements` | `number` | Total matched records. |
| `totalPages` | `number` | Total pages count. |
| `number` | `number` | Current page number. |
| `size` | `number` | Page size. |

### Errors

| Name | Code | Description |
| --- | --- | --- |
| `401 Unauthorized` | HTTP | `x-api-key` is missing, invalid, or expired. |
| `400 Client id is required` | BUSINESS | Neither client id nor external client id is provided. |

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
  "fiatAsset": "RUB",
  "orderType": "BUY",
  "destination": "CARD"
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
                  "countries": ["Russia"]
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
      }
    ]
  }
]
```

### Headers

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `x-api-key` | `string` | Yes | Authenticates merchant backend request. |

### Request

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `clientId` | `string` | No | Merchant client id. |
| `fiatAsset` | `string` | No | Fiat asset filter. |
| `orderType` | `string` | No | Direction filter (`BUY`/`SELL`). |
| `destination` | `string` | No | Merchant destination filter. |
| `providers` | `array of string` | No | Explicit provider filter list. |
| `isCrypto` | `boolean` | No | Crypto-method filter. |
| `countryGroup` | `array of string` | No | Country-group filter. |

### Response

| Name | Type | Description |
| --- | --- | --- |
| `id` | `string` | Provider id. |
| `name` | `string` | Provider display name. |
| `addPaymentMethod` | `boolean` | Whether provider supports payment-method binding. |
| `config` | `object` | Provider routing configuration. |
| `config.paymentSystems` | `array of objects` | Payment systems list. |
| `config.paymentSystems[].paymentSystem` | `string` | Payment system name. |
| `config.paymentSystems[].type` | `string` | Provider channel type. |
| `config.paymentSystems[].directions` | `array of objects` | Supported directions. |
| `config.paymentSystems[].directions[].direction` | `string` | Direction value. |
| `config.paymentSystems[].directions[].currencies` | `array of objects` | Supported currencies. |
| `config.paymentSystems[].directions[].currencies[].currency` | `string` | Currency code. |
| `config.paymentSystems[].directions[].currencies[].countries` | `array of string` | Optional country restrictions. |
| `commissions` | `array of objects` | Commission settings. |
| `commissions[].buyCommission` | `string` | Buy-side commission value/range. |
| `commissions[].sellCommission` | `string` | Sell-side commission value/range. |

### Errors

| Name | Code | Description |
| --- | --- | --- |
| `401 Unauthorized` | HTTP | `x-api-key` is missing, invalid, or expired. |
| `400 Invalid external user id` | BUSINESS | External client id validation failed in merchant scope. |

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

| Name | Type | Description |
| --- | --- | --- |
| `id` | `string` | Webhook event id. |
| `type` | `string` | Event type (`order.processing`, `order.completed`, `order.expired`, `order.failed`). |
| `createdAt` | `string` | Event creation timestamp. |
| `sessionId` | `string` | Session id related to the order event. |
| `orderId` | `string` | Order id related to the event. |
| `externalClientId` | `string` | Merchant external client identifier from order context. |

## 5) Quote/Rate Notes

How these values are calculated:
- `plainRate` = base market rate (bid/ask) before final quote adjustments.
- `rate` (final client rate):
  - for SELL: `rate = toAmount / fromGrossAmount`
  - for BUY: `rate = fromGrossAmount / toAmount`
- percentage fee for any step: `fee = amount * percent / 100`
- reverse fee (when target amount is fixed): `fee = amount / (1 - percent) - amount`
- total fee by side:
  - `fromFeeAmount = fromPaymentFeeAmount + fromExchangeFeeAmount`
  - `toFeeAmount = toPaymentFeeAmount + toExchangeFeeAmount`
  - if input is FIAT: `fee.total = fromFeeAmount`
  - if input is CRYPTO: `fee.total = toFeeAmount`
- network fee in response:
  - if input is CRYPTO: `fee.network = fromPaymentFeeAmount`
  - if input is FIAT: `fee.network = toPaymentFeeAmount`
- `fee.service = null` (service part is not returned separately in this response format).

Variables:
- `fromGrossAmount`: how much the client gives before deductions.
- `fromAmount`: amount left from input side after input-side fees.
- `toAmount`: final amount the client receives after output-side fees.
- `amount`: current amount used in fee formula on current step.
- `percent`: fee percent configured for rule (e.g. `2.5` means `2.5%`).
- `fromPaymentFeeAmount` / `toPaymentFeeAmount`: payment/transfer fee part.
- `fromExchangeFeeAmount` / `toExchangeFeeAmount`: exchange service fee part.
