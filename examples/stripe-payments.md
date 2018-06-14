---
description: Taking payments for orders with Stripe
---

# Stripe Payments

### Setting up the payment gateway {#setting-up-the-payment-gateway}

Before we can accept any payments in your store, we need to register with a merchant. There are hundreds of payment merchants out there and the best one for you will depend on how many orders you expect to receive, the value of those orders, the countries your customers are buying from and a whole host of other variables. If you’re unsure which merchant to go for, Stripe is always a good choice. We’ve done our best at Moltin to ensure it is as painless as possible to switch between providers so most of the time, this will be a breeze if you do decide to change!

Now that we’ve decided to use Stripe, we need some API keys. To get them, if you haven’t already, you need to [register with Stripe](https://dashboard.stripe.com/register). Once you’ve logged into your account, you need to [get your keys](https://dashboard.stripe.com/account/apikeys) \(we’re only interested in the private one this time\). Next, we’ll enable that gateway on your store.

To enable Stripe, we need to make a `PUT` request to `https://api.moltin.com/v2/gateways/stripe` with the following data where `login` is your secret key.

```javascript
{
  "data": {
    "type": "gateway",
    "enabled": true,
    "login": "sk_test_XXX"
  }
}
```



This will enable Stripe on your store and tell the API which Stripe key to use. I’d recommend using your test key until you’re sure everything is working and then switch over to your live key when your store goes live.

The process for paying for an order in version 2 is:

1. Add an item to your cart
2. Checkout
3. Make a payment

### Add an item to your cart {#add-an-item-to-your-cart}

First of all, we’ll need something in our cart to pay for. Adding something to your cart is as simple as `POST`ing to `https://api.moltin.com/v2/carts/:cartID/items`:

```javascript
{
  "data": {
    "type": "cart_item",
    "id": "5a13d787-9e81-4aea-91f6-d281a7aef8b7",
    "quantity": 1
  }
}
```

You can use any URL safe value you want for your cart IDs. If a cart doesn’t already exist with that ID, it’ll be created the first time you add an item. If this request is successful, you’ll get a `201` response with all of the cart items that exist in the updated cart \(not just the new one!\).

```javascript
{
  "data": [
    {
      "id": "ff1f6bb4-4e2a-44c3-9db6-62a5350a7ad8",
      "type": "cart_item",
      "product_id": "2b215a59-03d5-47bc-b631-2c0c26b14a0c",
      "name": "Canon 111C012BA SX610 HS PowerShot Point and Shoot Digital Camera",
      "description": "A camera",
      "sku": "111C012BA",
      "quantity": 1,
      "unit_price": {
        "amount": 13270,
        "currency": "GBP",
        "includes_tax": true
      },
      "value": {
        "amount": 13270,
        "currency": "GBP",
        "includes_tax": true
      },
      "links": {
        "product": "https://api.moltin.com/products/2b215a59-03d5-47bc-b631-2c0c26b14a0c"
      },
      "meta": {
        "display_price": {
          "with_tax": {
            "unit": {
              "amount": 13270,
              "currency": "GBP",
              "formatted": "£132.70"
            },
            "value": {
              "amount": 13270,
              "currency": "GBP",
              "formatted": "£132.70"
            }
          },
          "without_tax": {
            "unit": {
              "amount": 13270,
              "currency": "GBP",
              "formatted": "£132.70"
            },
            "value": {
              "amount": 13270,
              "currency": "GBP",
              "formatted": "£132.70"
            }
          }
        },
        "timestamps": {
          "created_at": "2017-02-10T13:37:44.119Z",
          "updated_at": "2017-02-10T13:37:44.119Z"
        }
      }
    }
  ],
  "meta": {
    "display_price": {
      "with_tax": {
        "amount": 13270,
        "currency": "GBP",
        "formatted": "£132.70"
      },
      "without_tax": {
        "amount": 13270,
        "currency": "GBP",
        "formatted": "£132.70"
      }
    },
    "timestamps": {
      "created_at": "2017-04-01T10:45:00Z",
      "updated_at": "2017-04-01T10:45:00Z"
    }
  }
}
```

We’ve also recently added [custom cart items](https://moltin.com/blog/2017/05/introducing-custom-line-items/) to the platform — these are arbitrary item lines that don’t exist as products in our store, they’re great for things that you only compute when checking out such as free items, tax, and delivery costs.

You can use the following request to add a custom item to your cart by `POST`ing the same `https://api.moltin.com/v2/carts/:cartID/items` endpoint.

```javascript
{
  "data": {
    "type": "custom_item",
    "name": "Custom Item",
    "sku": "my-custom-item",
    "description": "A custom item",
    "quantity": 1,
    "price": {
      "amount": 100
    }
  }
}
```

### Checkout {#checkout}

Once we have something in our cart we can convert it to an unpaid order and, unlike before we updated the process, you don’t have to specify the payment gateway you’d like to use at this point. You do however need to specify things like the customer email and billing & shipping addresses. Once we’ve checked out, we’ll be given a new order. Checking out multiple times will generate multiple new orders. So if a user modifies their cart, you’re free to check out again.

To check out `POST` the following request to `https://api.moltin.com/v2/carts/:cartID/checkout`:

```javascript
{
  "data": {
    "customer": {
      "name": "Billy",
      "email": "billy@billy.com"
    },
    "billing_address": {
      "first_name": "Jack",
      "last_name": "Macdowall",
      "company_name": "Macdowalls",
      "line_1": "1225 Invention Avenue",
      "line_2": "Birmingham",
      "postcode": "B21 9AF",
      "county": "West Midlands",
      "country": "US",
      "city": "billing city"
    },
    "shipping_address": {
      "first_name": "Otis",
      "last_name": "Sedmak",
      "company_name": "Sedmak & Co.",
      "line_1": "1251, Rexmere Ave",
      "line_2": "Farmingville, Suffolk",
      "postcode": "11738",
      "county": "Farmingville, Suffolk",
      "country": "US",
      "instructions": "Leave in porch",
      "city": "shipping city"
    }
  }
}
```

Again, we’ll get a `201` if all goes well \(or a suitable error and a 4xx response code if it doesn’t\) with the newly created order:

```javascript
{
  "data": {
    "type": "order",
    "id": "fe7a4df4-25b5-4117-81b9-c6831a669a5a",
    "status": "incomplete",
    "payment": "unpaid",
    "shipping": "not_shipped",
    "customer": {
      "name": "Billy",
      "email": "billy@billy.com"
    },
    "shipping_address": {
      "first_name": "Otis",
      "last_name": "Sedmak",
      "company_name": "Sedmak & Co.",
      "line_1": "1251, Rexmere Ave",
      "line_2": "Farmingville, Suffolk",
      "city": "shipping city",
      "postcode": "11738",
      "county": "Farmingville, Suffolk",
      "country": "US",
      "instructions": "Leave in porch"
    },
    "billing_address": {
      "first_name": "Jack",
      "last_name": "Macdowall",
      "company_name": "Macdowalls",
      "line_1": "1225 Invention Avenue",
      "line_2": "Birmingham",
      "city": "billing city",
      "postcode": "B21 9AF",
      "county": "West Midlands",
      "country": "US"
    },
    "links": {},
    "meta": {
      "display_price": {
        "with_tax": {
          "amount": 13270,
          "currency": "GBP",
          "formatted": "£132.70"
        },
        "without_tax": {
          "amount": 13270,
          "currency": "GBP",
          "formatted": "£132.70"
        }
      },
      "timestamps": {
        "created_at": "2017-04-15T10:02:26.010224773Z",
        "updated_at": "2017-04-15T10:02:26.010225206Z"
      }
    },
    "relationships": {
      "items": {
        "data": [
          {
            "type": "item",
            "id": "cebf7230-ede2-4214-8d57-b29fe6b5ae5c"
          }
        ]
      }
    }
  }
}
```

### Make a payment {#make-a-payment}

Now we just need to pay for the order we’ve just created. To do this simply send over a `POST` to `https://api.moltin.com/v2/orders/:orderID/payments` with the following body:

```javascript
{
  "data": {
    "gateway": "stripe",
    "method": "purchase",
    "first_name": "John",
    "last_name": "Smith",
    "number": "4242424242424242",
    "month": "08",
    "year": "2019",
    "verification_value": "123"
  }
}
```

In this request, we’re telling the payments service to contact Stripe with the provided card details. If everything goes well we’ll get a `200` response. If something fails, we’ll get an error message with the relevant HTTP response code. If something fails at the payment gateway, we’ll always forward any error messages on to you.

### Switching Provider {#switching-provider}

Now we have Stripe working well, but our client comes back and says they’ve decided to switch their payments to Braintree. Luckily we’re covered. We can switch between many providers as easily as configuring the new gateway and updating the slug in our payment request.

To configure Braintree send a `PUT` to `https://api.moltin.com/v2/gateways/braintree` with the data \(replacing `MERCHANT_ID`, `PRIVATE_KEY`, `PUBLIC_KEY` with your correct values from Braintree\):

```javascript
{
  "data": {
    "type": "gateway",
    "enabled": true,
    "environment": "sandbox",
    "merchant_id": "MERCHANT_ID",
    "private_key": "PRIVATE_KEY",
    "public_key": "PUBLIC_KEY"
  }
}
```

You’ll notice the configuration options are different for Braintree. While we’ve tried to keep everything as uniform as possible, configuration options vary a lot between providers. You can always do a `GET` to `https://api.moltin.com/v2/gateways/:slug` where the slug is the provider you want to use to get the required options for that provider.

Once we’ve configured the gateway, we just need to switch the slug in our `POST` request to `https://api.moltin.com/v2/orders/:orderID/payments` and we’re done!

```javascript
{
  "data": {
    "gateway": "braintree",
    "method": "purchase",
    "first_name": "John",
    "last_name": "Smith",
    "number": "4111111111111111",
    "month": "08",
    "year": "2019",
    "verification_value": "737"
  }
}
```

It is worth noting that some gateways use a whole different process to accept payments. PayPal, for example, requires a redirect to their site where the card details are entered, so these requests wouldn’t work in that situation.

