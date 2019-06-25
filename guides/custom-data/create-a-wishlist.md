---
description: >-
  You can use the Moltin API to extend your stores functionality by providing
  users the ability to save products to a wishlist.
---

# Create a Wishlist

{% hint style="danger" %}
Content moved to [Developer Guides](https://www.moltin.com/developer/guides/create-a-wishlist-with-flows). 
{% endhint %}

### Prerequisites

* Moltin dashboard \(you'll need Client ID and Client Secret of your store\).
* Good understanding of object-oriented programming or JavaScript.

### Summary of steps required:

* Create a Flow that will contain your wishlist object.
* Create `products` Field to store customer's desired products.
* Create empty Entries to associate with products.
* Create a relationship between an Entry and a product. This will be created every time a customer adds a product to a wishlist.
* Associate a wishlist with a customer by adding a `customers` Flow and creating a `one-to-many` relationship between a customer and a wishlist.

### 1. Get your access token

You will need to get a [`client_credentials`](https://docs.moltin.com/basics/authentication/client-credential-token) access token to follow along making the API requests outlined below.

```bash
curl -X "POST" "https://api.moltin.com/oauth/access_token" \
     -d "client_id=XXXX" \
     -d "client_secret=XXXX" \
     -d "grant_type=client_credentials"
```

### 2. Create a new custom Flow

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

### 3. Tell a Wishlist it will have Products \(create a new Field\)

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
              "id": "{{WISHLIST_FLOW_ID}}"
            }
          }
        }
      }
    }'
```

### 4. Create an empty Wishlist \(flow entry\)

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

### 5. Add Product to Wishlist

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

### 6. Get all Wishlists

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

### 7. Extend the Customers Entity \(create a customer flow\)

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

### 8. Link a customer to a wishlist \(configure relationship\)

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

### 9. Get a customers wishlists

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

### Adding a new wishlist

To summarise the steps above,  in order to add a new wishlist with new products and a customer who owns it, you should do the following:

* Create a new empty entry in the wishlist flow \(Step 4\)
* Add products to the new entry \(Step 5\)
* Associate the entry with a customer \(Step 8\)

### Deleting a wishlist

In order to delete the wishlist, you just need its entry ID, which you can get by following step 9. Once you have the ID, you can call the following:

{% code-tabs %}
{% code-tabs-item title="Delete Wishlist Entry" %}
```bash
curl -X DELETE https://api.moltin.com/v2/flows/wishlist/entries/{ENTRY_ID} \
     -H "Authorization: XXXX"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

After you have done this, you should also delete the relationship which exists on the customer, by following the delete method [outlined here](https://docs.moltin.com/api/advanced/custom-data/entry-relationships).

### Updating a wishlist

Should your customer wish to add another product to their wishlist, you can follow step 5, which as per the [docs](https://docs.moltin.com/api/advanced/custom-data/entry-relationships), will append a new relationship without affecting the existing related products.

Should your customer wish to remove a product from their wishlist, you should follow the delete method, again [outlined here](https://docs.moltin.com/api/advanced/custom-data/entry-relationships).



