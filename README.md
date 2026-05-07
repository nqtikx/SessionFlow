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
| `destinationCryptoAddress` | `string` | Yes | Destination crypto wallet address. The address must belong to the network selected in `toAsset`. |
| `comment` | `string` | No | Optional crypto transfer comment. |
| `externalClientId` | `string` | Yes | Merchant-side external client identifier. |
| `redirectUrl` | `string` | No | Redirect URL for SDK flow. |
| `email` | `string` | No | Optional prefilled email for SDK. |

### Response

| Name | Type | Description |
| --- | --- | --- |
| `sessionId` | `string` | Session identifier used for tracking and downstream flow. |
| `externalClientId` | `string` | Merchant-side external client identifier from request. |
| `destinationCryptoAddress` | `string` | Destination crypto wallet used by session. The address must belong to the network selected in `toAsset`. |
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
| `exchange.fees[].amount` | `number` | Fee amount value in the asset specified by `exchange.fees[].asset`. |
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
  "asset": { "code": "RUB" },
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

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `x-api-key` | `string` | Yes | Authenticates merchant backend request. |

### Params

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `clientId` | `string` | Conditionally | Required when `externalClientId` query param is not provided. |
| `externalClientId` | `string` | Conditionally | Required when `clientId` query param is not provided. |
| `destination` | `string` | No | Optional destination filter. Recommended value: `EXCHANGE`. |

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
| `conditions.fromFeeAmount` | `number` | Source-side fee amount in `conditions.fromAsset` currency. |
| `conditions.toGrossAmount` | `number` | Target amount before target-side fee. |
| `conditions.toNetAmount` | `number` | Target amount after target-side fee. |
| `conditions.toFeeAmount` | `number` | Target-side fee amount in `conditions.toAsset` currency. |
| `conditions.promoCode` | `string \| null` | Promo code used in order conditions. |
| `conditions.rate` | `string` | Rate pair code. |
| `conditions.systemRateValue` | `number` | System/base rate value. |
| `conditions.exchangeRateValue` | `number` | Exchange rate value. |
| `conditions.actualRateValue` | `number` | Actual final rate value. |
| `recalculationReason` | `string \| null` | Recalculation reason enum value, if order was recalculated. |
| `clientId` | `string` | Internal client id. |
| `sessionId` | `string \| null` | Session id linked to this order. |
| `status` | `string` | Current order status. Allowed values: `PROCESSING`, `EXPIRED`, `COMPLETED`, `FAILED`. |
| `failureMessage` | `string \| null` | Technical/business failure reason for failed flow. |
| `completionDate` | `string \| null` | Completion timestamp. |
| `creationDate` | `string` | Creation timestamp. |
| `input` | `object` | Input operation details. |
| `input.type` | `string` | Input operation type. |
| `input.asset` | `string` | Input asset code. |
| `input.amount` | `number` | Input amount. |
| `input.transactionAmount` | `number \| null` | Provider/blockchain transaction amount. |
| `input.feeAmount` | `number \| null` | Input-side operation fee amount in `input.asset` currency. |
| `input.status` | `string \| null` | Operation status. Allowed values: `NEW`, `PROCESSING`, `EXPIRED`, `COMPLETED`, `FAILED`. |
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
| `output.feeAmount` | `number \| null` | Output-side operation fee amount in `output.asset` currency. |
| `output.status` | `string \| null` | Operation status. Allowed values: `NEW`, `PROCESSING`, `EXPIRED`, `COMPLETED`, `FAILED`. |
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

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `x-api-key` | `string` | Yes | Authenticates merchant backend request. |

### Request

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `externalClientId` | `string` | No | External client id filter. |
| `sessionIds` | `array of string` | No | Filter by session ids (`UUID`). |
| `clientIds` | `array of string` | Conditionally | Required when `externalClientId` is not provided (first value is used for merchant access validation). |
| `statuses` | `array of string` | No | Order status filter list. Allowed values: `PROCESSING`, `EXPIRED`, `COMPLETED`, `FAILED`. |
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
| `destinations` | `array of string` | No | Destination enum values. Recommended value for SDK flows: `EXCHANGE`. |

### Response

| Name | Type | Description |
| --- | --- | --- |
| `content` | `array of objects` | Page content list. |
| `content[].id` | `string` | Order id. |
| `content[].number` | `number` | Order number. |
| `content[].conditions` | `object` | Conditions block. |
| `content[].conditions.fromAsset` | `string` | Source asset id. |
| `content[].conditions.toAsset` | `string` | Target asset id. |
| `content[].conditions.fromGrossAmount` | `number` | Source amount before source-side fee. |
| `content[].conditions.fromNetAmount` | `number` | Source amount after source-side fee. |
| `content[].conditions.fromFeeAmount` | `number` | Source-side fee amount in `content[].conditions.fromAsset` currency. |
| `content[].conditions.toGrossAmount` | `number` | Target amount before target-side fee. |
| `content[].conditions.toNetAmount` | `number` | Target amount after target-side fee. |
| `content[].conditions.toFeeAmount` | `number` | Target-side fee amount in `content[].conditions.toAsset` currency. |
| `content[].conditions.promoCode` | `string \| null` | Promo code used in order conditions. |
| `content[].conditions.rate` | `string` | Rate pair code. |
| `content[].conditions.systemRateValue` | `number` | System/base rate value. |
| `content[].conditions.exchangeRateValue` | `number` | Exchange rate value. |
| `content[].conditions.actualRateValue` | `number` | Actual final rate value. |
| `content[].recalculationReason` | `string \| null` | Recalculation reason enum value. |
| `content[].input` | `object` | Input operation block. |
| `content[].input.type` | `string` | Input operation type. |
| `content[].input.asset` | `string` | Input asset code. |
| `content[].input.amount` | `number` | Input amount. |
| `content[].input.transactionAmount` | `number \| null` | Provider/blockchain transaction amount. |
| `content[].input.feeAmount` | `number \| null` | Input-side operation fee amount in `content[].input.asset` currency. |
| `content[].input.status` | `string \| null` | Operation status. Allowed values: `NEW`, `PROCESSING`, `EXPIRED`, `COMPLETED`, `FAILED`. |
| `content[].input.failureMessage` | `string \| null` | Operation failure details. |
| `content[].input.expirationDate` | `string \| null` | Operation expiration timestamp. |
| `content[].input.provider` | `string \| null` | Fiat-provider specific field (`FIAT_PROVIDER`). |
| `content[].input.paymentType` | `string \| null` | Fiat-provider payment type. |
| `content[].input.processingBank` | `string \| null` | Provider processing bank name. |
| `content[].input.clientBank` | `string \| null` | Client bank name. |
| `content[].input.fromToken` | `string \| null` | Source payment token id. |
| `content[].input.toToken` | `string \| null` | Destination payment token id. |
| `content[].input.link` | `string \| null` | Provider payment/deeplink URL. |
| `content[].input.processorTransactionId` | `string \| null` | Provider transaction id. |
| `content[].input.post` | `string \| null` | Masked card/post value from token metadata. |
| `content[].input.paymentSystem` | `string \| null` | Payment system brand. |
| `content[].input.processorTransactionNumber` | `string \| null` | Provider transaction display number. |
| `content[].input.fromAddress` | `string \| null` | Source crypto address (`CRYPTO_TRANSFER`). |
| `content[].input.toAddress` | `string \| null` | Destination crypto address (`CRYPTO_TRANSFER`). |
| `content[].input.comment` | `string \| null` | Crypto transfer comment/tag. |
| `content[].input.hash` | `string \| null` | Blockchain transaction hash. |
| `content[].output` | `object` | Output operation block. |
| `content[].output.type` | `string` | Output operation type. |
| `content[].output.asset` | `string` | Output asset code. |
| `content[].output.amount` | `number` | Output amount. |
| `content[].output.transactionAmount` | `number \| null` | Provider/blockchain transaction amount. |
| `content[].output.feeAmount` | `number \| null` | Output-side operation fee amount in `content[].output.asset` currency. |
| `content[].output.status` | `string \| null` | Operation status. Allowed values: `NEW`, `PROCESSING`, `EXPIRED`, `COMPLETED`, `FAILED`. |
| `content[].output.failureMessage` | `string \| null` | Operation failure details. |
| `content[].output.expirationDate` | `string \| null` | Operation expiration timestamp. |
| `content[].output.provider` | `string \| null` | Fiat-provider specific field (`FIAT_PROVIDER`). |
| `content[].output.paymentType` | `string \| null` | Fiat-provider payment type. |
| `content[].output.processingBank` | `string \| null` | Provider processing bank name. |
| `content[].output.clientBank` | `string \| null` | Client bank name. |
| `content[].output.fromToken` | `string \| null` | Source payment token id. |
| `content[].output.toToken` | `string \| null` | Destination payment token id. |
| `content[].output.link` | `string \| null` | Provider payment/deeplink URL. |
| `content[].output.processorTransactionId` | `string \| null` | Provider transaction id. |
| `content[].output.post` | `string \| null` | Masked card/post value from token metadata. |
| `content[].output.paymentSystem` | `string \| null` | Payment system brand. |
| `content[].output.processorTransactionNumber` | `string \| null` | Provider transaction display number. |
| `content[].output.fromAddress` | `string \| null` | Source crypto address (`CRYPTO_TRANSFER`). |
| `content[].output.toAddress` | `string \| null` | Destination crypto address (`CRYPTO_TRANSFER`). |
| `content[].output.comment` | `string \| null` | Crypto transfer comment/tag. |
| `content[].output.hash` | `string \| null` | Blockchain transaction hash. |
| `content[].clientId` | `string` | Internal client id. |
| `content[].sessionId` | `string \| null` | Related session id. |
| `content[].status` | `string` | Order status value. Allowed values: `PROCESSING`, `EXPIRED`, `COMPLETED`, `FAILED`. |
| `content[].failureMessage` | `string \| null` | Failure details, if any. |
| `content[].completionDate` | `string \| null` | Completion timestamp. |
| `content[].creationDate` | `string` | Creation timestamp. |
| `pageable` | `object` | Paging metadata block. |
| `pageable.sort` | `object` | Sort metadata for current page request. |
| `pageable.sort.unsorted` | `boolean` | Whether paging sort is unsorted. |
| `pageable.sort.sorted` | `boolean` | Whether paging sort is applied. |
| `pageable.sort.empty` | `boolean` | Whether sort metadata is empty. |
| `pageable.pageNumber` | `number` | Current page number. |
| `pageable.pageSize` | `number` | Current page size. |
| `pageable.offset` | `number` | Current page offset. |
| `pageable.paged` | `boolean` | Indicates paged request mode. |
| `pageable.unpaged` | `boolean` | Indicates unpaged request mode. |
| `totalElements` | `number` | Total matched records. |
| `totalPages` | `number` | Total pages count. |
| `last` | `boolean` | Whether current page is the last page. |
| `numberOfElements` | `number` | Number of elements in current page. |
| `number` | `number` | Current page number. |
| `size` | `number` | Page size. |
| `sort` | `object` | Sort metadata for response page. |
| `sort.unsorted` | `boolean` | Whether page content is unsorted. |
| `sort.sorted` | `boolean` | Whether page content is sorted. |
| `sort.empty` | `boolean` | Whether sort metadata is empty. |
| `first` | `boolean` | Whether current page is the first page. |
| `empty` | `boolean` | Whether page content is empty. |

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

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `x-api-key` | `string` | Yes | Authenticates merchant backend request. |

### Request

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `clientId` | `string` | No | Merchant client id. |
| `fiatAsset` | `string` | No | Fiat asset filter. |
| `orderType` | `string` | No | Direction filter (`BUY`/`SELL`). |
| `destination` | `string` | No | Optional destination filter. Recommended value: `EXCHANGE`. |
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
| `config.paymentSystems[].directions[].currencies[].banks` | `array of string` | Allowed banks for the currency, if configured. |
| `config.paymentSystems[].directions[].currencies[].countries` | `array of string` | Optional country restrictions. |
| `commissions` | `array of objects` | Commission settings. |
| `commissions[].bank` | `string \| null` | Optional bank scope for commission rule. |
| `commissions[].destination` | `string \| null` | Optional destination scope for commission rule. Recommended value for SDK flow: `EXCHANGE`. |
| `commissions[].buyCommission` | `string` | Buy-side commission percentage (or percentage range) in string format defined by provider config. |
| `commissions[].sellCommission` | `string` | Sell-side commission percentage (or percentage range) in string format defined by provider config. |

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

Use these fields in UI:
- Show `actualRate` (or `actualRateValue`) to the client as the final client-facing rate.
- Treat `exchangeRate` / `exchangeRateValue` as engine/reference rate used in calculation.
- Fee values are returned with explicit asset fields; always interpret fee amount in its corresponding asset currency.
