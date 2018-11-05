# Stripe integration

Integrate Stripe with your Moltin's project using the `/gateways/stripe` endpoint.

### Prerequisites <a id="prerequisites"></a>

* Configured products and gateway using Moltin [dashboard](https://dashboard.moltin.com/) or [API](https://docs.moltin.com/).
* [Stripe account](https://dashboard.stripe.com/login) to obtain Stripe API Keys \(aka login\).

### Integration steps summary <a id="integration-steps-summary"></a>

1. Configure products through Moltin API or dashboard.
2. Enable the Stripe payment gateway through Moltin API or dashboard, and provide their API Key \(login\).
3. Use Stripe as a dedicated gateway when making payment calls.

### How Stripe works with Moltin - quick overview <a id="process-workflow"></a>

The client application created with the Moltin API calls the Stripe API and passes to the browser cart details needed for checkout. Stripe validates the purchase data and returns a `token`/`source` Moltin API uses to complete the payment transaction.

### Enable Stripe on Moltin <a id="integrate-with-moltin"></a>

To enable Stripe gateway programatically or through the dashboard, set the gateway as enabled and provide login detail. This will enable Stripe on your project, and tell the API which Stripe key to use. 

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X PUT https://api.moltin.com/v2/gateways/stripe \
     -H "Authorization: Bearer XXX" \
     -H "Content-Type: application/json" \
     -d $'{
         "data": {
            "gateway": "stripe",
            "type": "gateway",
            "login": "sk_test_xxxx",
            "enabled": true
  }
}
```
{% endtab %}

{% tab title="Response" %}
```bash
{
    "data": {
        "enabled": true,
        "login": "sk_test_xxxx",
        "name": "Stripe",
        "slug": "stripe",
        "type": "gateway"
    }
}
```
{% endtab %}
{% endtabs %}

### Integrate with Moltin's checkout flow <a id="integrate-with-moltin"></a>

Use Moltin API to create a checkout flow. See the [JavaScript SDK](../../developer-tools/sdks.md#javascript-sdk) for more API interactions. At minimum, you’ll need ability to:

1. Add items to a cart.
2. Check out a cart.
3. Pay for an order.

#### Add item\(s\) to a cart <a id="configure-the-checkout-flow"></a>

This section will walk you briefly through steps required to add item\(s\) to a cart. The scenario assumes that it's a one singular product added to a new cart. For more details, see [Carts](https://docs.moltin.com/carts-and-checkout/carts) in API Reference. 

You can use any URL safe value you want for your cart IDs. If a cart doesn’t already exist with that ID, it’ll be created the first time you add an item. If this request is successful, you’ll get a `201` response with all of the cart items that exist in the updated cart.

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST https://api.moltin.com/v2/carts/:cartID/items \
     -H "Authorization: Bearer XXX" \
     -H "Content-Type: application/json" \
     -d $'{
         "data": {
            "type": "cart_item",
             "id": "5a13d787-9e81-4aea-91f6-d281a7aef8b7",
             "quantity": 1
  }
}
```
{% endtab %}

{% tab title="Response" %}
```bash
{
    "data": [
        {
            "id": "8838d444-87f4-411f-9d89-9fc809f0b1cb",
            "type": "cart_item",
            "product_id": "5a13d787-9e81-4aea-91f6-d281a7aef8b7",
            "name": "Crown",
            "description": "Abstract, sculptural, refined and edgy with a modern twist. Its symmetrical, spoked structure generates a clever geometric presence, which works well in a contemporary environment.",
            "sku": "CWLP100BLK",
            "image": {
                "mime_type": "image/png",
                "file_name": "lamp7-trans.png",
                "href": "https://s3-eu-west-1.amazonaws.com/bkt-svc-files-cmty-api-moltin-com/e8c53cb0-120d-4ea5-8941-ce74dec06038/7cc08cbb-256e-4271-9b01-d03a9fac9f0a.png"
            },
            "quantity": 1,
            "manage_stock": true,
            "unit_price": {
                "amount": 47500,
                "currency": "USD",
                "includes_tax": true
            },
            "value": {
                "amount": 47500,
                "currency": "USD",
                "includes_tax": true
            },
            "links": {
                "product": "https://api.moltin.com/products/9eda5ba0-4f4a-4074-8547-ccb05d1b5981"
            },
            "meta": {
                "display_price": {
                    "with_tax": {
                        "unit": {
                            "amount": 47500,
                            "currency": "USD",
                            "formatted": "47500"
                        },
                        "value": {
                            "amount": 47500,
                            "currency": "USD",
                            "formatted": "475.00"
                        }
                    },
                    "without_tax": {
                        "unit": {
                            "amount": 47500,
                            "currency": "USD",
                            "formatted": "47500"
                        },
                        "value": {
                            "amount": 47500,
                            "currency": "USD",
                            "formatted": "475.00"
                        }
                    }
                },
                "timestamps": {
                    "created_at": "2018-05-08T10:00:20.171620445Z",
                    "updated_at": "2018-05-08T10:00:20.171620445Z"
                }
            }
        }
    ],
    "meta": {
        "display_price": {
            "with_tax": {
                "amount": 47500,
                "currency": "USD",
                "formatted": "475.00"
            },
            "without_tax": {
                "amount": 47500,
                "currency": "USD",
                "formatted": "475.00"
            }
        },
        "timestamps": {
            "created_at": "2018-05-08T10:00:20.171620445Z",
            "updated_at": "2018-05-08T10:00:20.171620445Z"
        }
    }
}
```
{% endtab %}

{% tab title="JavaScript SDK" %}
```javascript
async function addToCart() {
  try {
    // Get all products in your store
    const products = await Moltin.Products.All();
    const product = products.data[0];
​
    // Add the first product to the cart
    await Moltin.Cart.AddProduct(product.id);
​
    // Get the cart items
    this.cartItems = Moltin.Cart.Items();
  } catch (e) {
    // Handle errors
  };
};
```
{% endtab %}
{% endtabs %}

#### Check out a cart

Checking out a cart converts it into an unpaid order. You don’t have to specify the payment gateway you’d like to use at this point. At minimum, you'll need to provide billing details. Shipping is optional. For more details, see [Checkout](https://docs.moltin.com/carts-and-checkout/checkout) in API Reference.

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST https://api.moltin.com/v2/carts/:cartID/checkout \
     -H "Authorization: Bearer XXX" \
     -H "Content-Type: application/json" \
     -d $'{
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
    }
  }
}
```
{% endtab %}

{% tab title="Response" %}
```bash
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
{% endtab %}
{% endtabs %}

#### Make a payment <a id="implement-payment-detail"></a>

Making a payment through Stripe is a two-step process. you'll need to:

1. Collect customer's payment details outside of Moltin and [pass them to Stripe](https://stripe.com/docs/quickstart). Stripe returns a payment `token`.
2. Pass the `token` value in the `payment` field, and complete the payment transaction on Moltin's side. If successful, you'll get a `200` response. See the example below.

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST https://api.moltin.com/v2/orders/:orderId/payments \
     -H "Authorization: Bearer XXXX" \
     -d $'{
        "data": {
          "gateway": "stripe",
          "method": "purchase",
          "payment": "tok_visa",
          "options": {
            "destination": "acct_XXX",
            "receipt_email": "john@example.com"
          }
        }
      }
```
{% endtab %}

{% tab title="JavaScript SDK" %}
```bash
const MoltinGateway = require('@moltin/sdk').gateway
​
const Moltin = MoltinGateway({
  client_id: 'X'
})
​
const id = 'XXXX'
​
const payment = {
  gateway: 'stripe',
  method: 'purchase',
  payment: 'tok_visa'
  options: {
    destination: 'acct_XXX'
  }
}
​
Moltin.Orders.Payment(id, order).then(() => {
  // Do something
})
```
{% endtab %}
{% endtabs %}

Using `token`, as opposed to passing payment details directly through Moltin API, is a secure and preferred method, although it is also possible to provide payment details directly, in which case it is absolutely vital to use the HTTPS protocol.

#### Source vs. token

Rather than requesting a `token` back from Stripe, you can request a `source`, and pass it exactly like a token, through the `payment` field. Tokens are for single use only, and expire within minutes after their creation; whereas sources can be charged directly, or attached to customers for later reuse. For more details, see [Stripe documentation](https://stripe.com/docs/api/sources?lang=curl). 

