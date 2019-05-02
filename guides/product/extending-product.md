---
description: >-
  You can extend any resource to enable pre-defined information for display
  and/or to power custom business logic elsewhere in your application that
  requires this additional data to function.
---

# Extend a resource with Flows

### Prerequisites

* Moltin dashboard \(you'll need Client ID and Client Secret of your store\).
* Basic understanding of object-oriented programming or JavaScript.

### Summary of steps required

* Create a Flow for the endpoint you want to extend, e.g. products, orders, etc.
* Create Fields to add custom data.
* Create Entries to store data passed to your fields.

### Step-by-step walkthrough

The steps below will show you how to extend an existing resource with a core Flow, so it can have all of the JSON you want and need. 

### 1. Get your access token

You will need to get a [`client_credentials`](https://docs.moltin.com/basics/authentication/client-credential-token) access token to follow along making the API requests outlined below.

```bash
curl -X POST https://api.moltin.com/oauth/access_token \
     -d "client_id=XXXX" \
     -d "client_secret=XXXX" \
     -d "grant_type=client_credentials"
```

### 2. Create a core Flow

Let's start with creating a new Flow. Note that  `slug` **must** match the plural name of the resource you're extending, e.g. `products`.

{% hint style="info" %}
If you already have a Flow for your existing resource, skip to [Create a Flow Field](extending-product.md#3-create-a-flow-field). You'll need the ID of the products Flow to continue.
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
          "description": "flow description",
          "enabled": true
        }
     }'
```

Take note of the Flow's `id` that is returned. You'll need this to create a Flow field, as described below.

### 3. Create a Field

The Field will be returned when you call the object you've extended. 

{% hint style="info" %}
Replace Flow\_ID with the ID that was generated for the Flow \(see step 2\).
{% endhint %}

```bash
curl -X POST https://api.moltin.com/v2/fields \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     {
  "data": {
    "type": "field",
    "name": "Field Name",
    "slug": "field-name",
    "field_type": "integer",
    "description": "field description",
    "required": false,
    "unique": false,
    "default": 0,
    "enabled": true,
    "order": 1,
    "relationships": {
        "flow": {
            "data": {
                "type": "flow",
                "id": "Flow_ID"
            }
        }
    }
  }
}
```

### 4. Create an Entry

The Entry will store data passed to your field. Create an Entry for every Field added. Replace `FIELD_SLUG` with the actual `slug` you used for the Field you wish to create an Entry for.

```bash
curl -X POST https://api.moltin.com/v2/flows/:flowSlug/entries \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
       "data": {
         "type": "entry",
         "{FIELD_SLUG}": "a value",
       }
     }'
```

### Verify your new field

To verify whether your existing resource has been expanded with new fields, all you need to do is call \(GET\) this resource. The fields will also be available in the [dashboard](https://dashboard.moltin.com/app) under custom data for each resource you have there.

```bash
curl -X GET https://api.moltin.com/v2/products \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
```







