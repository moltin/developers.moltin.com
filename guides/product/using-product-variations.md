# Product Variations

Variations allow you to organize your products per certain characteristics, such as color or size. Use variations to manage your stock more efficiently. Variations are reusable, so you could attach the same variation to any number of products.  

For example if you have a t-shirt size variation you can setup all of your sizes so when you create a new t-shirt product, you can link that product to the existing t-shirt size variation. Meaning you won’t have to recreate every size every time you add a new t-shirt.

### 1. Get your access token

You will need to get a [`client_credentials`](https://docs.moltin.com/basics/authentication/client-credential-token) access token to follow along making the API requests outlined below.

```bash
curl -X "POST" "https://api.moltin.com/oauth/access_token" \
     -d "client_id=XXXX" \
     -d "client_secret=XXXX" \
     -d "grant_type=client_credentials"
```

### 2. Create a product variation

A typical example of a product variation is product size. You could foresee needing a small, medium and large variation for a shirt.

{% tabs %}
{% tab title="cURL" %}
```javascript
curl -X POST https://api.moltin.com/v2/variations \
     -H "Authorization: Bearer XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
      "data": {
        "type": "product-variation",
        "name": "Shirt Size"
      }
    }'
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
    "data": {
        "type": "product-variation",
        "id": "9c10417d-e2f8-4fad-9fca-5701c76576d9",
        "name": "Shirt Size"
    }
}
```
{% endtab %}
{% endtabs %}

### 3. Create a product option

Product options are the actual variation options that you want to make available for your project. This is were you'll be able to outline the size you want to offer, i.e. small, medium or large.

Within our ecosystem, a `variation` is simply a container for a number of `options`. 

{% tabs %}
{% tab title="cURL" %}
```javascript
curl -X POST https://api.moltin.com/v2/variations/:variationId/options \
     -H "Authorization: Bearer XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
            "type": "option",
            "name": "Small",
            "description": "Size small"
        }
    }'
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
    "data": {
        "type": "product-variation",
        "id": "ccfb0d2c-8ed5-4dd8-ab41-ba883f4d3fbb",
        "name": "Shirt size",
        "options": [
            {
                "id": "581dd431-38f0-415f-98fc-ec64b5aa7f9d",
                "name": "small",
                "description": "Size small",
                "modifiers": []
            },
            {
                "id": "f4413e4d-8258-4510-87f9-4af0d75bfcb1",
                "name": "medium",
                "description": "Size medium",
                "modifiers": []
            },
            {
                "id": "143814e1-c75f-46d4-8d14-97d15405321f",
                "name": "large",
                "description": "Size large",
                "modifiers": []
            }
        ],
        "relationships": {
            "options": {
                "data":
                    {
                        "type": "option",
                        "id": "581dd431-38f0-415f-98fc-ec64b5aa7f9d"
                    }
                }
              }
 }
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Replace `variationId` with the **ID** that you generated for the particular variation, as shown above \(step 2\). 
{% endhint %}

Repeat the step for every option you want to add. In the example above, we've added small, medium and large options \(see the Response tab for details\).

### 4. Create modifiers for your options

Modifiers determine how the child products built will vary from the base product. The minimum requirement is that each variation option **MUST** have both a `SKU` and a `slug` modifier\(`_append`, `_prepend`,  `_equals`\). This is because these properties must be unique across your product rage.

* For more details, see: [Modifiers](modifiers.md).

{% tabs %}
{% tab title="cURL" %}
```javascript
curl -X POST https://api.moltin.com/v2/variations/:variationId/options/:optionID \
     -H "Authorization: Bearer XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
          "data": {
              "type": "modifier",
              "modifier_type": "slug_append",
              "value": "-small"
  }
} 
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
    "data": {
        "type": "product-variation",
        "id": "ccfb0d2c-8ed5-4dd8-ab41-ba883f4d3fbb",
        "name": "Shirt size",
        "options": [
            {
                "id": "581dd431-38f0-415f-98fc-ec64b5aa7f9d",
                "name": "small",
                "description": "Size small",
                "modifiers": [
                    {
                        "id": "d675108f-3c6e-4c03-bea0-eae03e009214",
                        "type": "slug_append",
                        "value": "-small"
                    },
                    {
                        "id": "48eadcae-dd11-4d40-be23-6e6404a43b3b",
                        "type": "sku_append",
                        "value": "-small"
                    }
                ]
            },
            {
                "id": "f4413e4d-8258-4510-87f9-4af0d75bfcb1",
                "name": "medium",
                "description": "Size medium",
                "modifiers": [
                    {
                        "id": "1118633c-c947-4f91-8b29-d055fac2d8b7",
                        "type": "slug_append",
                        "value": "-medium"
                    },
                    {
                        "id": "84b062ac-436d-4a20-8322-1319d39414b9",
                        "type": "sku_append",
                        "value": "-medium"
                    }
                ]
            },
            {
                "id": "143814e1-c75f-46d4-8d14-97d15405321f",
                "name": "large",
                "description": "Size large",
                "modifiers": [
                    {
                        "id": "b0e2f026-bb1f-4369-86a0-b4f3f367c2b6",
                        "type": "slug_append",
                        "value": "-large"
                    },
                    {
                        "id": "8eabf166-6617-4969-807e-72f4c5a64948",
                        "type": "sku_append",
                        "value": "-large"
                    }
                ]
            }
        ],
        "relationships": {
            "options": {
                "data": [
                    {
                        "type": "option",
                        "id": "581dd431-38f0-415f-98fc-ec64b5aa7f9d"
                    },
                    {
                        "type": "option",
                        "id": "f4413e4d-8258-4510-87f9-4af0d75bfcb1"
                    },
                    {
                        "type": "option",
                        "id": "143814e1-c75f-46d4-8d14-97d15405321f"
                    }
                ]
            }
        }
    }
}
```
{% endtab %}
{% endtabs %}

Repeat the step for every option you want to add a modifier to. In the example above, we've added small, medium and large options and slug and SKU modifiers \(see the Response tab for details\).

### 5. Create the Product Relationships

Now, we need to allocate the variations to the base product. In this example, this would be the shirt.

{% hint style="info" %}
Repeat this for every product you wish to relate to this variation.
{% endhint %}

{% tabs %}
{% tab title="cURL" %}
```javascript
curl -X POST \
  https://api.moltin.com/v2/products/:productId/relationships/variations \
  -H "Authorization: Bearer XXXX" \
  -H 'content-type: application/json' \
  -d '{
     "data": [ {
        "type": "product-variation",
         "id": "{{variationId}}"
      }]
 }'
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
    "type": "product",
    "id": "d8e64f88-7bb4-4095-a7e8-a55d094648bb",
    "name": "SPCA",
    "slug": "shirt",
    "sku": "1",
    "manage_stock": false,
    "description": "A shirt",
    "price": [
        {
            "amount": 500,
            "currency": "USD",
            "includes_tax": true
        }
    ],
    "status": "live",
    "commodity_type": "digital",
    "meta": {
        "timestamps": {
            "created_at": "2018-05-03T19:36:31+00:00",
            "updated_at": "2018-08-06T09:40:46+00:00"
        },
        "stock": {
            "level": 0,
            "availability": "out-stock"
        },
        "variations": {
                "id": "ccfb0d2c-8ed5-4dd8-ab41-ba883f4d3fbb",
                "name": "Shirt size",
                "options": [
                    {
                        "id": "581dd431-38f0-415f-98fc-ec64b5aa7f9d",
                        "name": "small",
                        "description": "Size small"
                    },
                    {
                        "id": "f4413e4d-8258-4510-87f9-4af0d75bfcb1",
                        "name": "medium",
                        "description": "Size medium"
                    },
                    {
                        "id": "143814e1-c75f-46d4-8d14-97d15405321f",
                        "name": "large",
                        "description": "Size large"
                    }
                ]
            }
        ]
    },
    "relationships": {
        "variations": {
            "data": [
                {
                    "type": "product-variation",
                    "id": "0c02ca9f-27be-48a8-89d9-cd7c30699343"
                }]
        }
      }
   }
 }
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Replace `productId` with the **ID** of the base product.  

Replace `variationId`with the **ID** for the variation you created \(step 2\).
{% endhint %}

###  6. Build Child Variations

Now that the variation is set up and it has products assigned to it, you'll need to trigger the process that will compile all of the variations and create the child variations.

{% tabs %}
{% tab title="cURL" %}
```javascript
curl -X POST https://api.moltin.com/v2/products/:parentId/build/ \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer XXXX"
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
    "data": [
        {
            "type": "product",
            "id": "d74b43b5-e9bb-4bb9-99e8-4a8733316d8c",
            "name": "SPCA",
            "slug": "shirt-small",
            "sku": "1-small",
            "manage_stock": false,
            "description": "A shirt",
            "price": [
                {
                    "currency": "USD",
                    "amount": 500,
                    "includes_tax": true
                }
            ],
            "status": "live",
            "commodity_type": "digital"
        },
        {
            "type": "product",
            "id": "90f064ba-3a14-4bf8-afa7-336ccb991c49",
            "name": "SPCA",
            "slug": "shirt-medium",
            "sku": "1-medium",
            "manage_stock": false,
            "description": "A shirt",
            "price": [
                {
                    "currency": "USD",
                    "amount": 500,
                    "includes_tax": true
                }
            ],
            "status": "live",
            "commodity_type": "digital"
        },
        {
            "type": "product",
            "id": "bafd2ddc-7fab-4a60-ad2a-12fc6b99f8f6",
            "name": "SPCA",
            "slug": "shirt-large",
            "sku": "1-large",
            "manage_stock": false,
            "description": "A shirt",
            "price": [
                {
                    "currency": "USD",
                    "amount": 500,
                    "includes_tax": true
                }
            ],
            "status": "live",
            "commodity_type": "digital"
        }
    ]
}
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Replace `parentId` with the **ID** of the base product.  
{% endhint %}

### Child Variations in use

For adding a product defined by a variation to a cart or checking it out, make sure to use the **child product ID**, not the product ID.  You can view your products variation relationships within the [product object. ](https://docs.moltin.com/catalog/products/get-a-product)

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X GET https://api.moltin.com/v2/products/:id \
     -H "Authorization: Bearer XXXX" \
```
{% endtab %}

{% tab title="Response" %}
```bash
 {
            "type": "product",
            "id": "e6808653-a328-427c-9fcf-c4e0093cbd10",
            "name": "Deck Shoe",
            "slug": "deck-shoe_74",
            "sku": "deck-shoe_74",
            "manage_stock": false,
            "description": "Modern boat shoes were invented in 1935 by American Paul A. Sperry",
            "price": [
                {
                    "amount": 5891,
                    "currency": "USD",
                    "includes_tax": true
                }
            ],
            "status": "live",
            "commodity_type": "physical",
            "meta": {
                "timestamps": {
                    "created_at": "2018-07-23T15:29:41+00:00",
                    "updated_at": "2018-07-23T15:29:41+00:00"
                },
                "stock": {
                    "level": 0,
                    "availability": "out-stock"
                }
            },
            "relationships": {
                "parent": {
                    "data": {
                        "type": "product",
                        "id": "23230da6-5c0e-473e-b4aa-9ff76228df4b"
                    }
                }
            }
        }
```
{% endtab %}
{% endtabs %}

##  {#3-tie-the-variation-to-the-parent-product}



