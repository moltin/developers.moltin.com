---
description: >-
  Extending the product object is a great way to customize the object to have
  all information you need to display a product.
---

# Extend a Product

## 1. Get your access token

You will need to get a [`client_credentials`](https://docs.moltin.com/basics/authentication/client-credential-token) access token to follow along making the API requests outlined below.

```bash
curl -X "POST" "https://api.moltin.com/oauth/access_token" \
     -d "client_id=XXXX" \
     -d "client_secret=XXXX" \
     -d "grant_type=client_credentials"
```

## 2. Create a new Flow

 Let's extend the moltin products resource by creating a new flow.

{% hint style="info" %}
If you already have a Flow for `products`, skip to [Create a Flow Field](serverless/short-order-id.md#2-create-a-flow-field). You'll need the ID of the products Flow to continue.
{% endhint %}

```bash
curl -X POST https://api.moltin.com/v2/flows \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "flow",
          "name": "products extended",
          "slug": "products",
          "description": "Extend products resource with a custom flow so it can have all of the JSON you want and need.",
          "enabled": true
        }
     }'
```

Take note of the ID that is returned. You'll need this to create a Flow field, as described below.

## 3. Create a Flow field

The Flow field will be returned when you call the products object. In the example below, we will add a review field, so that products can now have a review tied to them.

{% hint style="info" %}
Replace Flow ID with the ID that was generated above.
{% endhint %}

```bash
curl -X POST https://api.moltin.com/v2/fields \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "field",
          "name": "review",
          "slug": "review",
          "description": "This is a review about the product",
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

## 4. Confirm your new field

Fetch some products, and confirm the new field. Note this new field will be available in the dashboard as custom data as well.

```bash
curl -X GET https://api.moltin.com/v2/products \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
```







