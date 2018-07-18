# Short Order ID

When an Order is created, Moltin will assign an ID for that order similar to `394916e8-1d47-44a0-b5d0-a5a61b71bab8`. This is known as a `uuid`.

Quite often you will interact with a customer, and using a long \`uuid\` to reference orders can be tricky.

One way to solve this issue would be to use the Custom Data API \(aka Flows\) to assign a field for `short_id` on an Order.

## 1. Get your access token

You will need to get a [`client_credentials`](https://docs.moltin.com/basics/authentication/client-credential-token) access token to follow along making the API requests outlined below.

```bash
curl -X "POST" "https://api.moltin.com/oauth/access_token" \
     -d "client_id=XXXX" \
     -d "client_secret=XXXX" \
     -d "grant_type=client_credentials"
```

## 2. Create a new Flow

With the power of Flows, we can extend the Moltin API with our own custom data. Let's extend the orders resource by creating a new flow.

{% hint style="info" %}
If you already have a Flow for `orders`, skip to [Create a Flow Field](short-order-id.md#2-create-a-flow-field). You'll need the ID of the orders Flow to continue.
{% endhint %}

```bash
curl -X POST https://api.moltin.com/v2/flows \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "flow",
          "name": "orders",
          "slug": "orders",
          "description": "Extend orders resource with a custom flow.",
          "enabled": true
        }
     }'
```

Take note of the ID that is returned. You'll need this when creating a Flow field, as described below.

## 3. Create a Flow Field

```bash
curl -X POST https://api.moltin.com/v2/fields \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "field",
          "name": "Short ID (managed by webhook)",
          "slug": "short_id",
          "description": "This is a short ID managed by a webhook.",
          "unique": true,
          "field_type": "string",
          "required": false,
          "enabled": true,
          "relationships": {
            "flow": {
              "type": "flow",
              "id": "FLOW_ID"
            }
          }
        }
     }'
   
```

## 4. Deploy the function

Using the [Now CLI](https://zeit.co/now) you can deploy directly from [GitHub](https://github.com/moltin-examples/short-order-id) and provide your store `client_id` and `client_secret`. You'll also want to create and set `MOLTIN_WEBHOOK_SECRET` to a secure random token to protect unauthorized requests.

You'll need the following ENV variables to successfully deploy the function:

* MOLTIN\_CLIENT\_ID
* MOLTIN\_CLIENT\_SECRET
* MOLTIN\_WEBHOOK\_SECRET

```bash
npm i -g now # unless installed already
now moltin-examples/short-order-id
```

{% hint style="warning" %}
Take note of the URL that is returned on successful deployment to Now.
{% endhint %}

## 5. Create a new webhook

With the function deployed, we can now tell Moltin to start subscribing to new orders created. You'll also need to provide `MOLTIN_WEBHOOK_SECRET`, as defined above.

```bash
curl -X POST https://api.moltin.com/v2/integrations \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "integration",
          "name": "Short order ID",
          "description": "Automatically create a short order ID",
          "enabled": true,
          "observes": [
            "order.created"
          ],
          "integration_type": "webhook",
          "configuration": {
            "url": "INSERT_URL_OF_FUNCTION_HERE",
            "secret_key": "MOLTIN_WEBHOOK_SECRET_VALUE_HERE"
          }
        }
     }'
```



