# Short Order ID

When an Order is created, Moltin will assign an ID for that order similar to `394916e8-1d47-44a0-b5d0-a5a61b71bab8`. This is known as a `uuid`.

Quite often you will interact with a customer and using a \`uuid\` to reference orders can be tricky.

One way to solve this issue would be to use the Custom Data API \(aka Flows\) to assign a field for `short_id` on an Order.

## 1. Create a new Flow

With the power of Flows we can extend the Moltin API with our own custom data. Let's extend the orders resource by creating a new flow.

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

Take note of the ID that is returned. You'll need this below.

## 2. Create a Flow Field

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

## 3. Deploy the function

The function is available to clone from [GitHub](https://github.com/moltin-examples/moltin-short-order-id) and designed to deploy to [Zeit Now](https://zeit.co/now). You'll need your Moltin `client_id` and `client_secret` during [setup](https://github.com/moltin-examples/moltin-short-order-id#-setup).

```bash
git clone git@github.com:moltin-examples/moltin-short-order-id.git
cd moltin-short-order-id
npm install
npm i -g now # if not already installed
now
```

{% hint style="warning" %}
Take note of the URL that is returned on successful deployment to Now.
{% endhint %}

## 4. Create a new webhook

With the function deployed we can now tell Moltin to start subscribing to new orders created.

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
            "url": "INSERT_URL_OF_FUNCTION_HERE"
          }
        }
     }'
```

