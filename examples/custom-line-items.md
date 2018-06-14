---
description: >-
  Custom line items allow a user to customize an item when adding it to the
  cart, or add other custom information to the line item in an order.
---

# Custom Line Items



With custom line items you can now add arbitrary items to a cart that haven’t been created as a product inside Moltin, giving you the flexibility to including additional costs on your orders.

One of our goals is to break down the links between individual services, so these can be used as micro-services where possible and complement your existing technology stack. In this case, we’re unlinking the requirement to use Moltin products inside of Moltin carts. A good example use case would be if you’re already using an existing PIM system and you’d like to start selling online. With custom line items, you can either sync your PIM products to Moltin products or directly use the PIM as a data source for cart items, skipping the Moltin product requirement completely.

This opens up the ability to do a number of other use cases too, including:

* Calculating things on a per order basis \(like shipping and taxes\)
* Dynamically create a special bundle price for a combination of products
* Calculate product prices by a custom value \(e.g. dimensions\)
* Using real-time prices from other systems or include additional surcharges that are calculated at checkout.

### Adding a custom item to a cart {#adding-a-custom-item-to-a-cart}

The structure of a custom item is just like a product but it only exists within the cart and not in the wider store \(because these are usually only used to edit that one individual order\).

When creating a custom item, you need to add some additional information to the normal product, including a new SKU and a description.

For example, if we were adding a custom price adjustment, it would look like the JSON below:

```javascript
{
  "data": {
    "type": "custom_item",
    "name": "Tax",
    "sku": "tax-calc",
    "description": "Custom tax calculation for this order",
    "quantity": 1,
    "price": {
      "amount": 12
    }
  }
}
```

You can then `POST` this JSON to the same `https://api.moltin.com/v2/carts/{reference}/items`endpoint as the standard product, which using cURL will look like this:

```javascript
curl -X POST https://api.moltin.com/v2/carts/supercart/items \
  -H "Authorization: Bearer XXXX" \
  -H "Content-Type: application/json" \
  -d '{"data":{"type":"custom_item","name":"Tax","sku":"tax-calc","description":"Custom tax calculation for this order","quantity":1,"price":{"amount":12}}}'
```

Provided everything is correct and you received a `200 OK` response, you’ll have something similar to what’s below in the body. In the event of an error, information on how to resolve it will be available alongside it.

```javascript
{
  "data": [
    {
      "id": "bfc39e26-02f1-4b4e-a098-72a5d8152cfc",
      "type": "custom_item",
      "name": "Tax",
      "description": "Custom tax calculation for this order",
      "sku": "tax-calc",
      "quantity": 1,
      "unit_price": {
        "amount": 12,
        "currency": "USD",
        "includes_tax": true
      },
      "value": {
        "amount": 12,
        "currency": "USD",
        "includes_tax": true
      },
      "links": {},
      "meta": {
        "display_price": {
          "with_tax": {
            "unit": {
              "amount": 12,
              "currency": "USD",
              "formatted": "$12.00"
            },
            "value": {
              "amount": 12,
              "currency": "USD",
              "formatted": "$12.00"
            }
          },
          "without_tax": {
            "unit": {
              "amount": 12,
              "currency": "USD",
              "formatted": "$12.00"
            },
            "value": {
              "amount": 12,
              "currency": "USD",
              "formatted": "$12.00"
            }
          }
        },
        "timestamps": {
          "created_at": "2017-04-09T09:49:57.511779781Z",
          "updated_at": "2017-04-09T09:49:57.511779781Z"
        }
      }
    }
  ],
  "meta": {
    "display_price": {
      "with_tax": {
        "amount": 12,
        "currency": "USD",
        "formatted": "$12.00"
      },
      "without_tax": {
        "amount": 12,
        "currency": "USD",
        "formatted": "$12.00"
      }
    },
    "timestamps": {
      "created_at": "0001-01-01T00:00:00Z",
      "updated_at": "0001-01-01T00:00:00Z"
    }
  }
```

And, with that `POST` request, the custom item has now been added to the cart and is available for use when turning the cart into an order! Read the [full documentation](https://docs.moltin.com/) around carts and custom items on our docs platform.

### Using custom line items to add tax options {#using-custom-line-items-to-add-tax-options}

Whilst we build out more features, we’ve created a workaround for tax, using TaxJar and custom line items, which you can [read about here](https://moltin.com/blog/2017/03/taxjar-moltin-winning/).

