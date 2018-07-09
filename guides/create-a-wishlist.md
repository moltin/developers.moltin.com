# Create a Wishlist

You can use the Moltin API to extend your stores functionality by providing users the ability to save products to a wishlist.

## 1. Get your access token

You will need to get a [`client_credentials`](https://docs.moltin.com/basics/authentication/client-credential-token) access token to follow along making the API requests outlined below.

```bash
curl -X "POST" "https://api.moltin.com/oauth/access_token" \
     -d "client_id=XXXX" \
     -d "client_secret=XXXX" \
     -d "grant_type=client_credentials"
```

## 2. Create a new custom Flow

Using the Moltin API, go ahead and create a flow for "**Wishlist**".

```bash
curl -X POST "https://api.moltin.com/v2/flows" \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
      "data": {
        "type": "flow",
        "name": "Wishlist",
        "slug": "wishlist",
        "description": "Allow customers to store products they want to purchase at a later date",
        "enabled": true
      }
    }'
```

{% hint style="warning" %}
Make sure to take note of the Flow ID returned, you'll need this below as `WISHLIST_FLOW_ID`.
{% endhint %}

## 3. Create a new Field

In this step we will create a field for \`products\`. This field will store the customers desired products.

```bash
curl -X POST "https://api.moltin.com/v2/fields" \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
      "data": {
        "type": "field",
        "name": "Products",
        "slug": "products",
        "field_type": "relationship",
        "validation_rules": [{
          "type": "one-to-many",
          "to": "product"
        }],
        "description": "Wishlist Products",
        "unique": false,
        "required": false,
        "enabled": true,
        "relationships": {
          "flow": {
            "data": {
              "type": "flow",
              "id": "WISHLIST_FLOW_ID"
            }
          }
        }
      }
    }'
```

## 4. Create a Flow Entry

With our custom Flow configured, we next create an empty Entry so we can associate products.

```bash
curl -X POST "https://api.moltin.com/v2/flows/wishlist/entries" \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
      "data": {
        "type": "entry"
      }
    }'
```

## 5. Add Product to Wishlist

With our custom Flow entry, we can now associate products with a wishlist entry.

{% hint style="info" %}
You'll need `ENTRY_ID` and `PRODUCT_FIELD_SLUG` from above to complete this request.
{% endhint %}

```bash
curl -X POST https://api.moltin.com/v2/flows/wishlist/entries/{ENTRY_ID}/relationships/{PRODUCT_FIELD_SLUG} \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
      "data": [
        {
            "type": "product",
            "id": "ba9ba29d-06da-4ba9-9e2e-f0e776703324"
        }
      ]
    }'
```

## 6. Get all Wishlists

```bash
curl https://api.moltin.com/v2/flows/wishlist \
     -H "Authorization: Bearer XXXX"
```

The response will contain the associated products \*\*per entry\*\* like below.

```javascript
{
  "data": {
    "id": "{ENTRY_ID}",
    "type": "entry",
    "relationships": {
      "products": {
        "data": [
          {
            "type": "product",
            "id": "ba9ba29d-06da-4ba9-9e2e-f0e776703324"
          }
        ]
      }
    }
  }
}
```

Due to the `implicit` grant type, it is important we connect wishlist and customers. We'll do that next by extending the customer resource.

## 7. Create a Customer Flow

We now need to associate a wishlist to a customer.

{% hint style="info" %}
If you already have a Flow for `customers`, skip this step.
{% endhint %}

```bash
curl -X POST https://api.moltin.com/v2/flows \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
      "data": {
        "type": "flow",
        "name": "Customers",
        "slug": "customers",
        "description": "Extends the default customer object",
        "enabled": true
      }
    }'
```

## 8. Configure relationship

Now let’s create the relationships field that will link a customer to a wishlist \(note that we’re going to create a 'one to many' relationship because we might want customers to create multiple wishlists\):

```bash
curl -X POST https://api.moltin.com/v2/fields \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
      "data": {
        "type": "field",
        "name": "Wishlists",
        "slug": "wishlists",
        "field_type": "relationship",
        "validation_rules": [{
          "type": "one-to-many",
          "to": "product"
        }],
        "description": "Customers wishlists",
        "unique": false,
        "enabled": true,
        "relationships": {
          "flow": {
            "data": {
              "type": "flow",
              "id": “{CUSTOMER_FLOW_ID}"
            }
          }
        }
      }
    }'
```

We now have the ability to send updates to customers, including their relationships:

```bash
curl -X POST https://api.moltin.com/v2/customers/{CUSTOMER_ID}/relationships/wishlists \
    -H "Authorization: Bearer XXXX" \
    -d $'{
     "data": [{
        "type": "wishlist",
        "id": "{WISHLIST_ENTRY_ID}"
      }]
    }'
```

## 9. Get customer wishlists

We’ve saved the wishlist to a customer, so when we make API calls to get that customer we will now see the wishlist\(s\) directly on the customer data object. Don't forget to set `?include=wishlists` to get the wishlist objects.

```bash
curl -X GET https://api.moltin.com/v2/customers/{CUSTOMER_ID}?include=wishlists \
     -H "Authorization: Bearer XXXX"
```

```bash
{
  "data": {
    "id": "c8c1c511-beef-4812-9b7a-9f92c587217c",
    "type": "customer",
    "name": "Ron Swanson",
    "email": "ron@swanson.com",
    "password": true,
    "relationships": [
      ("wishlists": [
        {
          "type": "entry",
          "id": "{WISHLIST_ENTRY_ID}"
        }
      ])
    ],
    "included": {
      "wishlists": [
        {
          "id": "{WISHLIST_ENTRY_ID}",
          "type": "entry",
          "relationships": {
            "products": {
              "data": [
                {
                  "type": "product",
                  "id": "ba9ba29d-06da-4ba9-9e2e-f0e776703324"
                },
                {
                  "type": "product",
                  "id": "394916e8-1d47-44a0-b5d0-a5a61b71bab8"
                }
              ]
            }
          }
        }
      ]
    }
  }
}
```





