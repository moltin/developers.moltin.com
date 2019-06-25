---
description: >-
  Extend product model to match your catalog data or enable additional
  validation, such as age restriction for movies.
---

# Extend the product resource

{% hint style="danger" %}
Content moved to [Developer Guides](https://www.moltin.com/developer/guides/extend-product-with-flows).
{% endhint %}

### Prerequisites

* Moltin dashboard \(you'll need the Client ID and Client Secret of your store\).
* Basic understanding of object-oriented programming or JavaScript.

### Summary of steps required

* Create a Flow for the endpoint you want to extend: `products`
* Create Fields to add custom data.
* Create a `product` that will include your custom data.

Fetching the extended `product` object will show all custom data added to that customer.

### **Step-by-step walkthrough**

The steps below will show you how to extend a `products` resource with a core Flow. In this scenario we'll discuss how to create the age restriction field for a random DVD product and how to add field validation to it.

### 1. Get your access token

You will need to get a [`client_credentials`](https://docs.moltin.com/basics/authentication/client-credential-token) access token to follow along making the API requests outlined below.

```bash
curl -X POST https://api.moltin.com/oauth/access_token \
     -d "client_id=XXXX" \
     -d "client_secret=XXXX" \
     -d "grant_type=client_credentials"
```

### 2. Create a core Flow

Let's start with creating a new Flow. Note that  `slug` **must** match the plural name of the resource you're extending, i.e. `products`.

{% hint style="info" %}
If you already have a `products` Flow, skip to [Create a Field](./#3-create-a-flow-field). You'll need the ID of the products Flow to continue.
{% endhint %}

```bash
curl -X POST https://api.moltin.com/v2/flows \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "flow",
          "name": "Products",
          "slug": "products",
          "description": "Extension of products",
          "enabled": true
        }
     }'
```

Take note of the Flow's `ID` that is returned. You'll need this to create a Flow field, as described below.

### 3. Create a Field

The Field will be returned when you call the `product` object. In the example below, we will add the age restriction field, so that products can display appropriate MPAA film classification for each DVD product. The validation will only accept valid classification values, such as G, P, PG, etc. that we will list the `options` array.

{% hint style="info" %}
Replace FLOW\_ID with the ID that was generated for the Flow in step 2.
{% endhint %}

```bash
curl -X POST https://api.moltin.com/v2/fields \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{ 
         "data": {
           "type": "field",
           "name": "MPAA classification",
           "slug": "mpaa_classification",
           "field_type": "string",
           "validation_rules": [{
             "type": "enum",
               "options": [
                 "G",
                 "PG",
                 "PG-13",
                 "R",
                 "NC-17"
               ]
            }],
            "description": "MPAA classification",
            "required": false,
            "unique": false,
            "default": 0,
            "enabled": true,
            "order": 1,
            "relationships": {
              "flow": {
                "data": {
                  "type": "flow",
                  "id": "{{FLOW_ID}}"
                }
            }
        }
    }
}'
```

### 4. Create a product with your custom field

Now, we need to start adding data to the product by requesting the `products` endpoint and adding the new key for the custom field in the data object and the value for the MPAA classification. Take a note of the ID returned for your product.

```bash
curl -X POST https://api.moltin.com/v2/products \
    -H "Authorization: XXXX" \
    -H "Content-Type: application/json" \
    -d $'{
       "data": {
         "type": "product",
         "name" : "Parks & Recreation Outtakes (Season 1)",
         "slug": "p-r-outtakes-s1",
         "sku": "PR.O.1",
         "manage_stock": false,
         "description": "LOL moments from Season 1",
         "status" : "live",
         "commodity_type": "physical",
         "price": [{
           "amount": 1299,
           "currency": "USD",
           "includes_tax": true
         }],
         "mpaa_classification": "NC-17"
        }
     }'
```

### 5. Get the product

Now, we've extended the product model and created our first product with the custom field, we can now make a call to get that product with the MPAA classification field. 

{% tabs %}
{% tab title="cURL Request" %}
```bash
curl -X GET https://api.moltin.com/v2/products/{{PRODUCT_ID}} \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json"
```
{% endtab %}

{% tab title="Response" %}
```bash
{
  "data": {
    "type": "product",
    "id": "4cb3861a-f2ea-447a-8e4e-7f07462b0ee1",
    "name": "Parks & Recreation Outtakes (Season 1)",
    "slug": "p-r-outtakes-s1",
    "sku": "PR.O.1",
    "manage_stock": false,
    "description": "LOL moments from Season 1",
    "price": [
      {
        "amount": 1299,
        "currency": "USD",
        "includes_tax": true
      }
    ],
    "status": "live",
    "commodity_type": "physical",
    "meta": {
      "timestamps": {
        "created_at": "2019-05-09T14:57:49+00:00",
        "updated_at": "2019-05-09T14:57:49+00:00"
      },
      "display_price": {
        "with_tax": {
          "amount": 1468,
          "currency": "USD",
          "formatted": "$14.68"
        },
        "without_tax": {
          "amount": 1299,
          "currency": "USD",
          "formatted": "$12.99"
        }
      },
      "stock": {
        "level": 0,
        "availability": "out-stock"
      },
      "relationships": {},
      "tax-code": null,
      "mpaa_classification": "NC-17"
    }
  }
}
```
{% endtab %}
{% endtabs %}

### Update the product

To update the product all you have to do is to pass the field you want to update to the `product` endpoint.

{% tabs %}
{% tab title="cURL Request" %}
```bash
curl -X PUT https://api.moltin.com/v2/products/{{PRODUCT_ID}} \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "product",
          "id": "{{PRODUCT_ID}}",
          "mpaa_classification": "PG-13"
        }
      }'
```
{% endtab %}

{% tab title="Response" %}
```bash
{
  "data": {
    "type": "product",
    "id": "4cb3861a-f2ea-447a-8e4e-7f07462b0ee1",
    "name": "Parks & Recreation Outtakes (Season 1)",
    "slug": "p-r-outtakes-s1",
    "sku": "PR.O.1",
    "manage_stock": false,
    "description": "LOL moments from Season 1",
    "price": [
      {
        "amount": 1299,
        "currency": "USD",
        "includes_tax": true
      }
    ],
    "status": "live",
    "commodity_type": "physical",
    "meta": {
      "timestamps": {
        "created_at": "2019-05-09T14:57:49+00:00",
        "updated_at": "2019-05-09T14:57:49+00:00"
      },
      "display_price": {
        "with_tax": {
          "amount": 1468,
          "currency": "USD",
          "formatted": "$14.68"
        },
        "without_tax": {
          "amount": 1299,
          "currency": "USD",
          "formatted": "$12.99"
        }
      },
      "stock": {
        "level": 0,
        "availability": "out-stock"
      },
      "relationships": {},
      "tax-code": null,
      "mpaa_classification": "PG-13"
    }
  }
}
```
{% endtab %}
{% endtabs %}









