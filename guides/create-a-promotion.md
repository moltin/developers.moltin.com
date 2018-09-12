# Create a Promotion

Promotions allow you to provide discounts to customers. By defining a Promotion and a code, you put in place the facility to offer discounts to customers applied directly to their shopping carts.

The promotions you create **require** codes to be attached to them. These codes are subsequently used by customers to apply promotions directly to carts and provide the stipulated discount. It is up to you to decide how you wish to distribute the codes to your customers; however, feel free to check out our [UI example](create-a-promotion.md#apply-the-promotion) below.

**Set up a promotion in two simple steps:**

1. Create a promotion. 
2. Add promotion codes to the promotion ID.

**At the cart level** promotion works in the following way:

1. Products are added to a cart, and if promotion is applicable, the user will input a promotion code to apply discount price.
2. The cart is checked out with the promotion applied.

### Create a Promotion

The promotion is defined by the following:

* **Name and description**. Helps you identify the promotion
* **Start and end date**. Defines when the Promotion starts and finishes. If the dates are not a match, the Promotion response returns a `404 not found` .
* **Enabled**. Allows you to switch the promotion on/off without having to touch the start/end times.
* **Promotion type**. Typically, a percentage taken off the original price or a fixed amount applied instead. To see a full list of supported `promotion_types` visit the [API docs](https://docs.moltin.com/carts-and-checkout/promotions#the-schema-object).
* **Schema**. The value and currency applied to your promotion. This depends on the `promotion_type`, e.g. if `fixed_discount` the value defined will be applied instead of the original price, if `percentage_discount`, the original price will be decreased by the defined percent.

{% tabs %}
{% tab title="cURL" %}
```javascript
curl -X POST https://api.moltin.com/v2/promotions \
     -H "Authorization: Bearer XXXX" \
     -d $'{
    "data": {
        "type": "promotion",
        "id": "c12ddfe7-3478-4822-b339-4a68ee9926a6",
        "name": "Coolpromo",
        "description": "Promotion",
        "enabled": true,
        "promotion_type": "percent_discount",
        "schema": {
            "currencies": [
                {
                    "currency": "USD",
                    "percentage": 10
                },
                {
                    "currency": "GBP",
                    "percentage": 10
                }
            ]
        },
        "start": "2017-01-13T00:00:00Z",
        "end": "2018-01-13T00:00:00Z",
        "created_at": "2018-05-29T20:02:57.238736345Z",
        "updated_at": "2019-05-29T20:02:57.238736906Z"
    }
}'
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
    "data": {
        "type": "promotion",
        "id": "841cd304-fb2a-47c2-9dfd-a4b6976f1aa3",
        "name": "Coolbrandjunepromo",
        "description": "June Promotion",
        "enabled": true,
        "promotion_type": "percent_discount",
        "schema": {
            "currencies": [
                {
                    "currency": "USD",
                    "percentage": 10
                },
                {
                    "currency": "GBP",
                    "percentage": 10
                }
            ]
        },
        "start": "2017-01-13T00:00:00Z",
        "end": "2018-01-13T00:00:00Z",
        "created_at": "2018-05-30T13:07:06.365225801Z",
        "updated_at": "2018-05-30T13:07:06.365226643Z"
    }
}
```
{% endtab %}
{% endtabs %}

### Tie promotion code\(s\) to the Promotion

A promotion is located and applied through a code. Each Promotion can have unlimited number of codes. 

{% hint style="info" %}
The code is the one your customers will enter after they have added the items to their cart.
{% endhint %}

#### How to handle codes

* Use user-friendly code formatting in a scenario where you wish your customers to use the same code for a limited period of time, such as Holiday season, e.g. 20%-OFF.
* Use unique codes in a scenario where you would like to control the number of products covered by the promotion.

{% tabs %}
{% tab title="cURL" %}
```javascript
curl -X POST https://api.moltin.com/v2/promotions/:id/codes \
     -H "Authorization: Bearer XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
      "data":{
        "type":"promotion_codes",
        "codes": [
          {"code":"SAVE10%"},
          {"code":"CoolPromo"}
        ]
      }
    }'
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
    "data": {
        "type": "promotion",
        "id": "9562dd60-78b1-43b0-b7c2-85430dbc3d60",
        "name": "Coolbrandjunepromo",
        "description": "June Promotion",
        "enabled": true,
        "promotion_type": "percent_discount",
        "schema": {
            "currencies": [
                {
                    "currency": "USD",
                    "percentage": 10
                },
                {
                    "currency": "GBP",
                    "percentage": 10
                }
            ]
        },
        "start": "2017-01-13T00:00:00Z",
        "end": "2018-01-13T00:00:00Z",
        "meta": {
            "timestamps": {
                "created_at": "2018-09-04T20:49:36.842Z",
                "updated_at": "2018-09-04T20:49:36.842Z"
            }
        }
    }
}
```
{% endtab %}
{% endtabs %}

### Use a Promotion

Once you have a promotion and code\(s\) applied to it, you can then update your code so it works at the cart level for when you want your customers to apply it to their cart items.

#### Add Item\(s\) to a Cart

The customer must have items already added to their cart before a promotion can be applied.

{% hint style="info" %}
The products added to cart must be in the same currency as the promotion currencies set up.
{% endhint %}

{% tabs %}
{% tab title="cURL" %}
```javascript
curl -X POST https://api.moltin.com/v2/carts/:reference/items \
     -H "Authorization: Bearer XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
      "data": {
          "id": "df32387b-6ce6-4802-9b90-1126a5c5a54f",
          "type": "cart_item",
          "quantity": 1
        }
      }'
```
{% endtab %}

{% tab title="JavaScript SDK" %}
```javascript
const MoltinGateway = require('@moltin/sdk').gateway
​
const Moltin = MoltinGateway({
  client_id: 'X'
})
​
const referenceId = 'XXXX'
const productId = 'XXXX'
const quantity = 1
​
Moltin.Cart(referenceId)
  .AddProduct(productId, quantity)
  .then(cart => {
    // Do something
  })
```
{% endtab %}

{% tab title="Swift SDK" %}
```swift
let moltin = Moltin(withClientID: "<your client ID>")
​
let referenceId = 'XXXX'
let productId = 'XXXX'
let productQty = 'XXXX'
​
​
self.moltin.cart.addProduct(withID: productId , ofQuantity: productQty, toCart: referenceId, completionHandler: { (_) in
​
})
```
{% endtab %}
{% endtabs %}

#### Apply the Promotion

The promotion codes work with the [Cart endpoint.](https://docs.moltin.com/carts-and-checkout/carts/add-promotion-to-cart)  The customer will input one of the promotion codes, and then you will need to make an API call to apply the pre set-up promotion the codes are tied to.

**User Interface example:**

![](../.gitbook/assets/screen-shot-2018-09-04-at-4.55.13-pm.png)

{% tabs %}
{% tab title="cURL" %}
```javascript
curl -X POST "/v2/carts/:cartId/items" \
     -H "Authorization: Bearer XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
       "data": {
         "type": "promotion_item",
         "code": "SAVE10%"
       }
     }'
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
    "data": [
        {
            "id": "c918bc02-3790-4a7f-9959-5fc392bdbe49",
            "type": "cart_item",
            "product_id": "ec7993fb-9fbb-48d0-9798-cc66fe324130",
            "name": "Deck Shoe",
            "description": "Modern boat shoes were invented in 1935 by American Paul A. Sperry",
            "sku": "deck-shoe_53",
            "image": {
                "mime_type": "",
                "file_name": "",
                "href": ""
            },
            "quantity": 1,
            "manage_stock": false,
            "unit_price": {
                "amount": 5891,
                "currency": "USD",
                "includes_tax": true
            },
            "value": {
                "amount": 5891,
                "currency": "USD",
                "includes_tax": true
            },
            "links": {
                "product": "https://api.moltin.com/products/ec7993fb-9fbb-48d0-9798-cc66fe324130"
            },
            "meta": {
                "display_price": {
                    "with_tax": {
                        "unit": {
                            "amount": 5891,
                            "currency": "USD",
                            "formatted": "$58.91"
                        },
                        "value": {
                            "amount": 5891,
                            "currency": "USD",
                            "formatted": "$58.91"
                        }
                    },
                    "without_tax": {
                        "unit": {
                            "amount": 5891,
                            "currency": "USD",
                            "formatted": "$58.91"
                        },
                        "value": {
                            "amount": 5891,
                            "currency": "USD",
                            "formatted": "$58.91"
                        }
                    }
                },
                "timestamps": {
                    "created_at": "2018-09-04T23:45:07Z",
                    "updated_at": "2018-09-04T23:45:07Z"
                }
            }
        },
        {
            "id": "4200bb27-3534-4a75-8a08-0268e665819c",
            "type": "promotion_item",
            "promotion_id": "7005b249-300b-4cf6-964e-e663278af218",
            "name": "Initial Promotion",
            "description": "Promotion",
            "sku": "SAVE10%",
            "image": {
                "mime_type": "",
                "file_name": "",
                "href": ""
            },
            "quantity": 1,
            "manage_stock": false,
            "unit_price": {
                "amount": -900,
                "currency": "USD",
                "includes_tax": false
            },
            "value": {
                "amount": -900,
                "currency": "USD",
                "includes_tax": false
            },
            "links": {},
            "meta": {
                "display_price": {
                    "with_tax": {
                        "unit": {
                            "amount": -900,
                            "currency": "USD",
                            "formatted": "-$9.00"
                        },
                        "value": {
                            "amount": -900,
                            "currency": "USD",
                            "formatted": "-$9.00"
                        }
                    },
                    "without_tax": {
                        "unit": {
                            "amount": -900,
                            "currency": "USD",
                            "formatted": "-$9.00"
                        },
                        "value": {
                            "amount": -900,
                            "currency": "USD",
                            "formatted": "-$9.00"
                        }
                    }
                },
                "timestamps": {
                    "created_at": "2018-09-04T23:45:12Z",
                    "updated_at": "2018-09-04T23:45:12Z"
                }
            }
        }
    ],
    "meta": {
        "display_price": {
            "with_tax": {
                "amount": 4991,
                "currency": "USD",
                "formatted": "$49.91"
            },
            "without_tax": {
                "amount": 4991,
                "currency": "USD",
                "formatted": "$49.91"
            }
        },
        "timestamps": {
            "created_at": "2018-09-04T23:45:07Z",
            "updated_at": "2018-09-04T23:45:12Z"
        },
        "messages": [
            {
                "source": {
                    "type": "promotion_item",
                    "id": "4200bb27-3534-4a75-8a08-0268e665819c"
                },
                "title": "Promotion Added",
                "description": "Promotion has been added to cart."
            }
        ]
    }
}
```
{% endtab %}

{% tab title="Wrong Code Response" %}
```javascript
{
    "errors": [
        {
            "status": 404,
            "title": "Not Found",
            "detail": "No promotion could be found with the given code."
        }
    ]
}
```
{% endtab %}
{% endtabs %}

#### Checkout with updated cart

Once the promotion has been applied to the cart,  the order is ready to be processed.

{% tabs %}
{% tab title="cURL" %}
```javascript
curl -X POST https://api.moltin.com/v2/carts/:reference/checkout \
     -H "Authorization: Bearer XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
       "data": {
         "customer": {
           "email": "john@moltin.com",
           "name": "John Doe"
         },
         "billing_address": {
           "first_name": "John",
           "last_name": "Doe",
           "company_name": "Moltin",
           "line_1": "2nd Floor British India House",
           "line_2": "15 Carliol Square",
           "city": "Newcastle upon Tyne",
           "postcode": "NE1 6UF",
           "county": "Tyne & Wear",
           "country": "UK"
         },
         "shipping_address": {
           "first_name": "John",
           "last_name": "Doe",
           "company_name": "Moltin",
           "phone_number": "(555) 555-1234",
           "line_1": "2nd Floor British India House",
           "line_2": "15 Carliol Square",
           "city": "Newcastle upon Tyne",
           "postcode": "NE1 6UF",
           "county": "Tyne & Wear",
           "country": "UK",
           "instructions": "Leave in porch"
         }
       }
    }'
```
{% endtab %}

{% tab title="JavaScript SDK" %}
```javascript
const MoltinGateway = require('@moltin/sdk').gateway
​
const Moltin = MoltinGateway({
  client_id: 'X'
})
​
const reference = 'XXXX'
const customerId = 'XXXX'
​
const billing = {
  first_name: 'John',
  last_name: 'Doe',
  line_1: '2nd Floor British India House',
  line_2: '15 Carliol Square',
  city: 'Newcastle Upon Tyne',
  postcode: 'NE1 6UF',
  county: 'Tyne & Wear',
  country: 'United Kingdom'
}
​
Moltin.Cart(reference)
  .Checkout(customerId, billing, shipping)
  .then(order => {
    // Do something
  })
```
{% endtab %}
{% endtabs %}

### Next steps

Once a Cart has been converted to an Order using either of the methods above, you will most likely want to [capture payment for order](https://docs.moltin.com/payments/paying-for-an-order).

{% page-ref page="payment/payment-request-api.md" %}

