# Create a Wishlist

You can use the Moltin API to create wishlist functionality for your project. By design, we provide the tools to create extensive eCommerce functionality with the power of flows.

Using Flows to create a wishlist allows you to extend the functionality for your specific project and customize the entire experience for your users.

### Create a custom Flow

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

📝 Make a note of the created **Flow ID** as you'll to replace `WISHLIST_FLOW_ID` below.

### Create a Field

Now you'll need to create a Flow Field so you can keep a record of a customers desired products. _This field should be marked as optional._

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

### Create an empty Entry

Before we can associate products to wishlist entries, we must first create an empty entry that will next be used to relate products to the wishlist entry.

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

### Create a customer wishlist

The Moltin API allows [implicit authentication]() requests to `/flows` so to best protect customer IDs from leaking, you'll want to create a relationship from the customer to the wishlist.

Go ahead and create a Flow for customers if you do not already have one.

```bash
curl -X POST "https://api.moltin.com/v2/flows" \
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

📝 Make a note of the created **Flow ID** as you'll to replace `CUSTOMER_FLOW_ID` below.

### Configure customers and wishlist flow

```bash
curl -X POST "https://api.moltin.com/v2/fields" \
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
              "id": "CUSTOMER_FLOW_ID"
            }
          }
        }
      }
    }'
```

### Relate a customer to wishlist





