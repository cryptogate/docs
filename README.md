# API

To use Cryptogate API you need to create account and use API key. You can find it on your profile page. Then send it as `X-Api-Key` header with every request.

The base URL for all requests is `https://cryptogate.io/api/v1/`.

Cryptogate API will respond with JSON payload. It expects parameters to be passed in JSON with the correct Content-Type: `application/json` being set.

## Creating invoice

### httpie example

```bash
http POST https://cryptogate.io/api/v1/invoices X-Api-Key:YOUR_KEY amount=1.0009 currency=emc
```

### curl example

```bash
curl -H "Accept: application/json" -H "Content-Type: application/json" -H "X-Api-Key: YOUR_KEY" -X POST -d '{"amount":1.0009,"currency":"emc"}' https://cryptogate.io/api/v1/invoices
```

### Response example

```json
{
    "address": "EVNUXVwHr15U5dYBLxn7cFY6BoV2LUAYpx",
    "amount": 1.0009,
    "amount_received": 0.0,
    "callback_params": null,
    "callback_url": null,
    "confirmations_needed": 1,
    "created_at": "2016-05-24T07:40:37.592Z",
    "currency": "emc",
    "description": null,
    "hashed_id": "7z6fx3",
    "merchant_name": null,
    "merchant_order_id": null,
    "status": "pending",
    "success_url": null,
    "txid": [],
    "uri": "emercoin:EVNUXVwHr15U5dYBLxn7cFY6BoV2LUAYpx?amount=1.0009&label=7z6fx3",
    "url": "https://cryptogate.io/invoices/7z6fx3",
    "validation_hash": "68428f36781eeec834851298f7ee606244820500"
}
```

### Available parameters

| Name                 | Type    | Description                                                                |
|----------------------|---------|----------------------------------------------------------------------------|
| amount               | Decimal | _Required_ Requested amount to be credited upon payment                    |
| currency             | String  | _Required_ Which currency to use. Options: `btc`, `ltc`, `emc` and `dash`  |
| confirmations_needed | Integer | How many confirmations needed before confirming payment                    |
| merchant_order_id    | String  | Order ID, purchase ID etc.                                                 |
| merchant_name        | String  | Your service name, shop title etc.                                         |
| description          | String  | Purchase description                                                       |
| callback_url         | String  | URL to which a callback should be sent when the invoice is paid            |
| callback_params      | Array   | Additional parameters to include in callback                               |
| success_url          | String  | URL to redirect customer after payment completes                           |

### Callback validation

A `validation_hash` parameter is added to all callback requests. Its purpose is to authenticate the call from Cryptogate to the callback URL. This signature must be properly checked by the receiving server in order to ensure that the request is legitimate and hasn't been tampered with.

The signature is computed by concatenating the API key with an invoice `hashed_id`, amount multiplyed by 10,000 and currency and applying a SHA1 hash function to it. You must use the following pattern: `"#{api_key}_#{hashed_id}_#{amount}_#{currency}"`

So if your API key is *supersecret*, hashed_id is *1q2w3e*, amount is *1.2345* and currency is *dash*, string before hashing should look like this:

```bash
supersecret_1q2w3e_12345_currency
```

Then hash it with SHA1 function and you will get validation hash.
