---
title: Lopay Partner API Reference

language_tabs: # must be one of https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers
  - shell

toc_footers:
  - <a target='_blank' href='https://lopay.com'>Learn more about Lopay</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Lopay Partner API
---

# Introduction

The Lopay Partner API enables partners to seamlessly send payment links and collect payments through their applications. This API also provides webhooks to notify partners of various payment-related events, ensuring real-time updates and streamlined operations.

**Key Features**  
- **Send Payment Links:** Generate and send payment links to customers.  
- **Webhooks:** Receive real-time notifications on payment events.  

# Environments

Environment | URL
----------- | ------------------------------
Staging     | ```https://staging.api.lopay.com```
Production  | ```https://api.lopay.com```

# Authentication

The Lopay Partner API uses API keys to allow access to the API. All partners will be provided a unique API key.

Your API key must be included in all API requests to the server in a header that looks like the following:

`x-api-key: api-key`

<aside class="notice">
You must replace <code>api-key</code> with your API key.
</aside>

You must also include an `x-merchant-id` header when acting on behalf of a Lopay account.

# Webhooks
Webhooks are a way for Lopay to notify partners when a significant event has taken place.

## Available events

The table below contains the events that it is possible to subscribe to. If you would like any new events added, please get in touch with your use case and we will usually be able to add support for the new event.

Event                     | Description
------------------------- | ------------------------------
```payment.success```     | Payment was successful.
```payment.failed```      | Payment has failed.
```paymentLink.created``` | Payment link created.
```paymentLink.updated``` | Payment link has been updated.
```paymentLink.revoked``` | Payment link has been revoked.

## Event data structure

All events share the following basic structure:

<aside class="pre">
{
  "id": "cbb90acf-a45d-4b2a-84dd-b6962921d6aa",
  "object": "event",
  "createdAt": "2024-06-12T19:03:04.456Z",
  "type": "payment.success",
  "data": {} // data specific to the event
}
</aside>

### payment.success

<aside class="pre">
{
  "id": "cbb90acf-a45d-4b2a-84dd-b6962921d6aa",
  "object": "event",
  "createdAt": "2024-06-12T19:03:04.456Z",
  "type": "payment.success",
  "data": {
    "paymentLinkId": "2a4ceed-0d10-4fc4-b9b2-f5b4b459dc5c"
    "paymentId": "9b8e457c-f679-4d2f-9551-ee8aaf7760f7"
  }
}
</aside>

### payment.failed

<aside class="pre">
{
  "id": "cbb90acf-a45d-4b2a-84dd-b6962921d6aa",
  "object": "event",
  "createdAt": "2024-06-12T19:03:04.456Z",
  "type": "payment.failed",
  "data": {
    "paymentLinkId": "2a4ceed-0d10-4fc4-b9b2-f5b4b459dc5c"
  }
}
</aside>

### paymentLink.created

<aside class="pre">
{
  "id": "cbb90acf-a45d-4b2a-84dd-b6962921d6aa",
  "object": "event",
  "createdAt": "2024-06-12T19:03:04.456Z",
  "type": "paymentLink.created",
  "data": {
    "paymentLinkId": "2a4ceed-0d10-4fc4-b9b2-f5b4b459dc5c"
  }
}
</aside>

### paymentLink.updated

<aside class="pre">
{
  "id": "cbb90acf-a45d-4b2a-84dd-b6962921d6aa",
  "object": "event",
  "createdAt": "2024-06-12T19:03:04.456Z",
  "type": "paymentLink.updated",
  "data": {
    "paymentLinkId": "2a4ceed-0d10-4fc4-b9b2-f5b4b459dc5c"
  }
}
</aside>

### paymentLink.revoked

<aside class="pre">
{
  "id": "cbb90acf-a45d-4b2a-84dd-b6962921d6aa",
  "object": "event",
  "createdAt": "2024-06-12T19:03:04.456Z",
  "type": "paymentLink.revoked",
  "data": {
    "paymentLinkId": "2a4ceed-0d10-4fc4-b9b2-f5b4b459dc5c"
  }
}
</aside>

## Subscribing to webhook events

Configure a new webhook subscription.

```shell
curl -X "POST "https://staging.api.lopay.com/api/1/partner/webhooks" \
  -H "x-api-key: api-key" \
  -H "content-type: application/json" \
  -d '{
        "events": ["payment.success"],
        "url": "https://your-api-url.com/webhooks/lopay"
      }'
```

> 201 Response

```json
{
  "id": "6b1b34ee-309e-493b-b59c-1d2430611202",
  "url": "https://your-api-url.com/webhooks/lopay",
  "events": ["payment.success"],
  "webhookSecretKey": "54337065d2e6c27e0e586e689d0f17debd22d955"
}
```

### HTTP Request

`POST http://staging.api.lopay.com/api/1/partner/webhooks`

### Request body

Property     | Type     | Description
------------ | -------- | -----------
`events`     | string[] | Array of events to subscribe to.
`url`        | string   | The URL you want us to send events to.


### Response body

Property           | Type     | Description
------------------ | -------- | -----------
`id`               | string   | Subscription ID.
`url`              | string   | The URL you want us to send events to.
`events`           | string[] | List of events you are now subscribed to.
`webhookSecretKey` | string   | Secret key to verify incoming webhook requests.

## Update webhook subscription

```shell
curl -X "POST "https://staging.api.lopay.com/api/1/partner/webhooks/:subscriptionId" \
  -H "x-api-key: api-key" \
  -H "content-type: application/json" \
  -d '{
        "events": ["payment.success"],
        "url": "https://your-api-url.com/webhooks/lopay"
      }'
```

> 200 Response

```json
{
  "id": "6b1b34ee-309e-493b-b59c-1d2430611202",
  "url": "https://your-api-url.com/webhooks/lopay",
  "events": ["payment.success"]
}
```

Update the events or URL of your webhook subscription.

### HTTP Request

`POST http://staging.api.lopay.com/api/1/partner/webhooks/:subscriptionId`

### Request body

Property     | Type     | Description
------------ | -------- | -----------
`events`     | string[] | Array of events to subscribe to.
`url`        | string   | The URL you want us to send events to.


<aside class="warning">Ensure you specify ALL the events you want to subscribe to.</aside>


### Response body

Property           | Type     | Description
------------------ | -------- | -----------
`id`               | string   | Subscription ID.
`url`              | string   | The URL you want us to send events to.
`events`           | string[] | List of events you are now subscribed to.

## Listing all webhook subscriptions

```shell
curl "https://staging.api.lopay.com/api/1/partner/webhooks" \
  -H "x-api-key: api-key"
```

> 200 Response

```json
{
  "webhooks": [
    {
      "id": "6b1b34ee-309e-493b-b59c-1d2430611202",
      "url": "https://your-api-url.com/webhooks/lopay",
      "events": ["payment.success"]
    }
  ]
}
```

### HTTP Request

`GET http://staging.api.lopay.com/api/1/partner/webhooks`

### Response body

Property            | Type     | Description
------------------- | -------- | -----------
`webhooks[].id`     | string   | Subscription ID.
`webhooks[].url`    | string   | The URL you want us to send events to.
`webhooks[].events` | string[] | List of events you are now subscribed to.


## Verifying webhook requests

In each request that Lopay send to a partner endpoint there will be a ```X-Lopay-Webhook-Signature``` header. Partners can use this signature along with the secret key to verify that the request was sent by Lopay, and not by a third party.

Each subscription to a Lopay webhook generates a new secret key and returns it. So, in the case of multiple subscriptions with different endpoints, partners need to use the corresponding secret key to verify requests to each of them.

To verify a request is from Lopay, partners need to compute the hash signature and compare this with the signature from the header to ensure they match. Partners can compute the hash signature by hashing the received payload with the SHA-256 function and the secret key shared previously. The following outlines in detail the steps required to verify the signature:

1. Extract the base64 encoded signature from the X-Lopay-Webhook-Signature header. The header is of the form ```sha256=AKnj34jY...``` so extract accordingly.  
2. Decode the retrieved signature from base64.  
3. Compute the expected signature with the SHA-256 function. Use the secret key (which is base 64 encoded) associated with this endpoint as the key and the request payload string as the message.  
4. Compare your generated hash signature with the decoded one extracted from the header. You may want to use a constant-time string comparison like compare_digest to protect against timing based attacks.  

<aside class="pre">
// Sample TypeScript code to verify signature

import crypto from 'crypto';
import express, { Request, Response } from 'express';
import bodyParser from 'body-parser';

export function calculateSignature(
  secret: string,
  payload: string,
): string {
  return crypto.createHmac('sha256', secret)
               .update(payload, 'utf8')
               .digest('hex');
}

export function verifySignature(
    secret: string,
    payload: string,
    signature: string,
): boolean {
  const calculatedSignature = calculateSignature(secret, payload);
  
  return crypto.timingSafeEqual(
    Buffer.from(calculatedSignature),
    Buffer.from(signature),
  );
}

const app = express();
const port = 3000;

// Replace with your actual Lopay secret
const LOPAY_WEBHOOK_SECRET = '54337065d2e6c27e0e586e689d0f17debd22d955';

// Middleware to parse JSON bodies
app.use(bodyParser.json());

// Webhook endpoint
app.post('/webhook', (req: Request, res: Response) => {
  const payload = JSON.stringify(req.body);
  const signature = req.headers['X-Lopay-Webhook-Signature'] as string;

  if (!signature) {
    return res.status(400).send('Signature header is missing');
  }

  if (verifySignature(LOPAY_WEBHOOK_SECRET, payload, signature)) {
    console.log('Webhook verified:', req.body);
    // Handle the webhook event
    res.status(200).send('Webhook received');
  } else {
    console.error('Invalid signature');
    res.status(400).send('Invalid signature');
  }
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
</aside>

## Webhook retries
In the event that your endpoint returns a non-200 response, we will retry the deliver 5 additional times through out the day. If after the 5th retry we still receive a non-200 response, we will drop the event.

# Payment links

## Creating a payment link

```shell
curl -X "POST" "https://staging.api.lopay.com/api/1/partner/payment-link" \
  -H "x-api-key: api-key" \
  -H "x-merchant-id: merchant-id" \
  -H "content-type: application/json" \
  -d '{
        "amount": {
          "units": 5000,
          "currencyCode": "GBP"
        },
        "description": "Payment for invoice #1234",
        "dataCollection": ["name", "email"],
        "taxes": [
          {
            "name": "VAT (20%)",
            "inclusive": true,
            "rate": "20.0"
          }
        ]
     }'
```

> Response:

```json
{
  "id": "f2a4ceed-0d10-4fc4-b9b2-f5b4b459dc5c",
  "status": "pending",
  "amount": {
    "units": 5000,
    "currencyCode": "GBP"
  },
  "description": "Payment for invoice #1234",
  "dataCollection": ["name", "email"],
  "taxes": [
    {
      "name": "VAT (20%)",
      "inclusive": true,
      "rate": "20.00"
    }
  ]
}
```

Create a new payment link

### HTTP Request

`POST https://staging.api.lopay.com/api/1/partner/payment-link`

### Request body

Property              | Type     | Description
--------------------- | -------- | -------------------------------------------------
`amount.units`        | integer  | Payment amount in pence (i.e. £50 -> 5000 units)
`amount.currencyCode` | string   | Currency code (i.e. GBP)
`description`         | string   | Payment link description
`dataCollection`      | string[] | Data to collect from customer. Available values: "name", "businessName", "phoneNumber", "email", and "address".
`taxes[].name`        | string   | Name of the tax (i.e. VAT 20%)
`taxes[].inclusive`   | boolean  | Whether the tax is inclusive or not. Will always be `true` for VAT.
`taxes[].rate`        | string   | Tax rate percentage. (i.e. "20.0" -> 20%)

### Response body

Property              | Type     | Description
--------------------- | -------- | --------------------------------------------------
`id`                  | string   | Payment link ID.
`status`              | string   | Payment link status. Available values: "pending", "cancelled", or "completed".
`amount.units`        | integer  | Payment amount in pence (i.e. £50 -> 5000 units).
`amount.currencyCode` | string   | Currency code (i.e. GBP).
`description`         | string   | Payment link description.
`dataCollection`      | string[] | Data to collect from customer. Available values: "name", "businessName", "phoneNumber", "email", and "address".
`taxes[].name`        | string   | Name of the tax (i.e. VAT 20%).
`taxes[].inclusive`   | boolean  | Whether the tax is inclusive or not. Will always be `true` for VAT.
`taxes[].rate`        | string   | Tax rate percentage. (i.e. "20.0" -> 20%).

## Updating a payment link

```shell
curl -X "PUT" "https://staging.api.lopay.com/api/1/partner/payment-link/:paymentLinkId" \
  -H "x-api-key: api-key" \
  -H "x-merchant-id: merchant-id" \
  -H "content-type: application/json" \
  -d '{
        "amount": {
          "units": 5000,
          "currencyCode": "GBP"
        },
        "description": "Payment for invoice #1234",
        "dataCollection": ["name", "email"],
        "taxes": [
          {
            "name": "VAT (20%)",
            "inclusive": true,
            "rate": "20.0"
          }
        ]
     }'
```

> 200 Response:

```json
{
  "id": "f2a4ceed-0d10-4fc4-b9b2-f5b4b459dc5c",
  "status": "pending",
  "amount": {
    "units": 5000,
    "currencyCode": "GBP"
  },
  "description": "Payment for invoice #1234",
  "dataCollection": ["name", "email"],
  "taxes": [
    {
      "name": "VAT (20%)",
      "inclusive": true,
      "rate": "20.0"
    }
  ]
}
```

Update an existing payment link

### HTTP Request

`POST https://staging.api.lopay.com/api/1/partner/payment-link/:paymentLinkId`

### Request body

Property              | Type     | Description
--------------------- | -------- | -------------------------------------------------
`amount.units`        | integer  | Payment amount in pence (i.e. £50 -> 5000 units)
`amount.currencyCode` | string   | Currency code (i.e. GBP)
`description`         | string   | Payment link description

### Response body

Property              | Type     | Description
--------------------- | -------- | --------------------------------------------------
`id`                  | string   | Payment link ID
`status`              | string   | Payment link status.
`amount.units`        | integer  | Payment amount in pence (i.e. £50 -> 5000 units)
`amount.currencyCode` | string   | Currency code (i.e. GBP)
`description`         | string   | Payment link description
`dataCollection`      | string[] | Data to collect from customer.
`taxes[].name`        | string   | Name of the tax (i.e. VAT 20%).
`taxes[].inclusive`   | boolean  | Whether the tax is inclusive or not.
`taxes[].rate`        | string   | Tax rate percentage. (i.e. "20.0" -> 20%)

## Sending a payment link

```shell
curl -X "POST" "https://staging.api.lopay.com/api/1/partner/payment-link/:paymentLinkId/send" \
  -H "x-api-key: api-key" \
  -H "x-merchant-id: merchant-id" \
  -H "content-type: application/json" \
  -d '{
        "sms": {
          "recipient": "+447595510472"
        },
        "email": {
          "recipient": "peter.parker@gmail.com"
        }
     }'
```

> Response:

```json
{
  "sms": {
    "status": "success"
  },
  "email": {
    "status": "success"
  }
}
```

Send the payment link via email and/or SMS.

### HTTP Request

`POST https://staging.api.lopay.com/api/1/partner/payment-link/:paymentLinkId/send`

### Request body

Property              | Type     | Description
--------------------- | -------- | -------------------------------------------------
`sms.recipient`       | string   | Optional. Receipient's phone number in E.164 format.
`email.recipient`     | string   | Optional. Receipient's email address.

<aside class="notice">
You must specify at least one or the other.
</aside>


### Response body

Property              | Type     | Description
--------------------- | -------- | --------------------------------------------------
`sms.status`          | string   | Optional. Will be either "success" or "failed".
`email.status`        | string   | Optional. Will be either "success" or "failed".


## Revoke a payment link

```shell
curl -X "DELETE" "https://staging.api.lopay.com/api/1/partner/payment-link/:paymentLinkId/send" \
  -H "x-api-key: api-key" \
  -H "x-merchant-id: merchant-id" \
```

> 204 Response:

```
<< empty response >>
```

Disable a payment link. The payment link's status will be set to ```cancelled``` and will no longer be active.

### HTTP Request

`DELETE https://staging.api.lopay.com/api/1/partner/payment-link/:paymentLinkId`

### Response body

No body


## List payment links

```shell
curl "https://staging.api.lopay.com/api/1/partner/payment-link" \
  -H "x-api-key: api-key" \
  -H "x-merchant-id: merchant-id" \
```

> 200 Response:

```json
{
  "data": [
    {
      "id": "f2a4ceed-0d10-4fc4-b9b2-f5b4b459dc5c",
      "amount": {
        "units": 5000,
        "currencyCode": "GBP"
      },
      "description": "Payment for invoice #1234"
    },
    {
      "id": "53121e81-43f3-4e94-a34b-8a3d3f216b92",
      "amount": {
        "units": 1000,
        "currencyCode": "GBP"
      },
      "description": "Payment for invoice #5555"
    }
  ],
  "paging": {
    "pageNumber": 1,
    "pageSize": 20,
    "pageTotal": 1,
    "count": 1
  }
}
```

List all payment links.

### HTTP request

`GET https://staging.api.lopay.com/api/1/partner/payment-link`

### Query parameters

Property              | Type     | Description
--------------------- | -------- | ---------------------------------------------------------
`pageSize`            | number   | Optional - default 20. Number of payment links to return
`pageNumber`          | number   | Optional - default 1. Receipient's email address.
`status`              | string   | Optional. Filter by payment link status. Available values: "pending", "cancelled", or "completed".
 
### Response body

Property                     | Type     | Description
---------------------------- | -------- | --------------------------------------------------
`data[].id`                  | string   | Payment link ID.
`data[].status`              | string   | Payment link status.
`data[].amount.units`        | string   | Payment link amount in pence.
`data[].amount.currencyCode` | string   | Payment link amount in currency.
`data[].description`         | string   | Payment link description.
`paging.pageNumber`          | number   | Current page of the response.
`paging.pageSize`            | number   | Number of payment links to return in response.
`paging.pageTotal`           | number   | Number of total available pages of payment links.
`paging.count`               | number   | Total number of payment links.

## Get payment link

```shell
curl "https://staging.api.lopay.com/api/1/partner/payment-link/:paymentLinkId" \
  -H "x-api-key: api-key" \
  -H "x-merchant-id: merchant-id" \
```

> 200 Response:

```json
{
  "id": "f2a4ceed-0d10-4fc4-b9b2-f5b4b459dc5c",
  "status": "completed",
  "amount": {
    "units": 5000,
    "currencyCode": "GBP"
  },
  "description": "Payment for invoice #1234",
  "dataCollection": ["name", "email"],
  "taxes": [
    {
      "name": "VAT (20%)",
      "inclusive": true,
      "rate": "20.0"
    }
  ],
  "payments": [
    {
      "id": "9b8e457c-f679-4d2f-9551-ee8aaf7760f7",
      "tenderType": "card",
      "card": {
        "type": "visa",
        "country": "GB",
        "lastFour": "4242"
      },
      "customer": [
        {
          "type": "name",
          "value": "Peter"
        },
        {
          "type": "email",
          "value": "peter.parker@gmail.com"
        }
      ],
      "taxes": [
        {
          "name": "VAT (20%)",
          "inclusive": true,
          "rate": "20.0"
        }
      ],
      "subtotal": {
        "units": 5000,
        "currencyCode": "GBP"
      },
      "tip": {
        "units": 1000,
        "currencyCode": "GBP"
      },
      "total": {
        "units": 6000,
        "currencyCode": "GBP"
      },
      "fee": {
        "gross": {
          "units": 138,
          "currencyCode": "GBP"
        },
        "discount": {
          "units": 0,
          "currencyCode": "GBP"
        },
        "net": {
          "units": 138,
          "currencyCode": "GBP"
        }
      },
      "net": {
        "units": 5862,
        "currencyCode": "GBP"
      },
      "refunds": [],
      "createdAt": "2024-05-29T00:10:51.699Z",
      "updatedAt": "2024-05-29T00:10:51.699Z"
    }
  ]
}
```

Get payment link details

### HTTP request

`GET https://staging.api.lopay.com/api/1/partner/payment-link/:paymentLinkId`

### Response body

Property                               | Type       | Description
-------------------------------------- | ---------- | -------------------------------------
`id`                                   | string     | Payment link ID.
`status`                               | string     | Payment link status.
`amount.units`                         | string     | Payment link amount in pence.
`amount.currencyCode`                  | string     | Payment link amount in currency.
`description`                          | string     | Payment link description.
`dataCollection`                       | string[]   | Data to collect from customer.
`taxes[].name`                         | string     | Name of the tax (i.e. VAT 20%).
`taxes[].inclusive`                    | boolean    | Whether the tax is inclusive or not. VAT will always be ```true```.
`taxes[].rate`                         | string     | Tax rate percentage (i.e. "20.0").
`payments[].id`                        | string     | Payment ID.
`payments[].tenderType`                | string     | Payment method used. "card" or "bankTransfer"
`payments[].card.type`                 | string     | Optional. Card type "visa", "mastercard", "amex", "diners", "discover, or "jcb".
`payments[].card.country`              | string     | Optional. Country where card was issued.
`payments[].card.lastFour`             | string     | Optional. Last four digits of card number.
`payments[].customer[].type`           | string     | Type of customer information. "name", "businessName", "phoneNumber", "email", "addressLine1", "addressLine2", "addressTown", "addressState", or "addressPostCode".
`payments[].customer[].value`          | string     | Customer detail value.
`payments[].taxes[].name`              | string     | Name of the tax (i.e. VAT 20%).
`payments[].taxes[].inclusive`         | string     | Whether the tax is inclusive or not. VAT will always be ```true```.
`payments[].taxes[].rate`              | string     | Tax rate percentage (i.e. "20.0").
`payments[].subtotal.units`            | string     | Amount of payment (excluding tip + exclusive taxes) in pence.
`payments[].subtotal.currencyCode`     | string     |
`payments[].tip.units`                 | string     | Tip amount in pence.
`payments[].tip.currencyCode`          | string     |
`payments[].total.units`               | string     | Total payment amount (including tip + exclusive taxes).
`payments[].total.currencyCode`        | string     |
`payments[].fee.gross.units`           | string     | Gross fee total in pence.
`payments[].fee.gross.currencyCode`    | string     |
`payments[].fee.discount.units`        | string     | Any fee discounts applied in pence.
`payments[].fee.discount.currencyCode` | string     |
`payments[].fee.net.units`             | string     | Gross fee - fee discount (i.e. fees actually paid) in pence.
`payments[].fee.net.currencyCode`      | string     | 
`payments[].net.units`                 | string     | Net amount of payment received in pence.
`payments[].net.currencyCode`          | string     |
`payments[].refunds[]`                 | PaymentDto | Same as this payment structure.
`payments[].createdAt`                 | string     | ISO 8601 date string (i.e. 2024-05-29T00:10:51.699Z) when payment was taken.
`payments[].updatedAt`                 | string     | ISO 8601 date string (i.e. 2024-05-29T00:10:51.699Z) when payment was last updated.

<aside class="notice">
Currently, there will only be at most one payment object in the <code>payments</code> field.
</aside>
