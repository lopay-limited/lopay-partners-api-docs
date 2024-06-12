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

> Response

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

> Response

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

> Response

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

# Kittens

## Get All Kittens

```shell
curl "http://example.com/api/kittens" \
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```shell
curl "http://example.com/api/kittens/2" \
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```shell
curl "http://example.com/api/kittens/2" \
  -X DELETE \
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

