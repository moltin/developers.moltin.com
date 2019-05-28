# Payment Gateway integration

Integrate any third party gateway with your Moltin's project using the `/gateways/[gateway]` endpoint.

### Prerequisites <a id="prerequisites"></a>

* Configured products and gateway using Moltin [dashboard](https://dashboard.moltin.com/) or [API](https://docs.moltin.com/).
* Third party gateway account \(typically to generate the API Keys\).

### Integration steps summary <a id="integration-steps-summary"></a>

1. Configure products through Moltin API or dashboard.
2. Enable the third party payment gateway through Moltin API or dashboard, and provide the API Key.
3. Use the enabled gateway as a dedicated gateway when making payment calls.

{% hint style="info" %}
You can configure and enable multiple gateways. It's the `orders/:orderId/payments` endpoint that decides which gateway will be used for a transaction.
{% endhint %}

### Enable a gateway on Moltin <a id="integrate-with-moltin"></a>

To enable a third party gateway programatically or through the dashboard, set the gateway as enabled and provide login detail. This will enable the gateway on your project, and tell the API which API key to use. 

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

Use Moltin API to create a checkout flow. See the [JavaScript SDK]() for more API interactions. At minimum, you’ll need ability to:

1. Add items to a cart.
2. Check out a cart.
3. Pay for an order. That's where you'll use a gateway.

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

### Make a payment - generic overview

Payment transactions are processed similarly for all gateways. The typical workflow includes three main steps detailed below. For a specific 3rd party related details, see the [detailed walkthrough](payment-gateway-integration.md#third-party-payment-gateways-detailed-walkthrough) below.

1. **A payment transaction is created and an attempt to pay is made.** The transaction is forwarded to the third party gateway specified by the customer \(this of course depends on how many active gateways you have configured\).
2. **The payment is processed by the third party gateway.** Payment transactions are always processed outside of Moltin for security reasons. Typically, you'd use a token rather than pass the card details directly. If, however, you wish to pass the cart details directly to the third party provider, Moltin enforces the use of the secure HTTPS protocol.
3. **If the transaction is successful, a** `200 OK` **response is returned.** Use the response \(success or failure\) to update the transaction, which will then in turn automatically update the order and payment statuses.

### Pay by card - generic overview

Paying by card is the most typical scenario that looks the same on any gateway. You'd use a payments endpoint to send the card details to the gateway you've specified in the request.

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST https://api.moltin.com/v2/orders/:order_id/payments \
     -H "Authorization: Bearer XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
      "data": {
        "gateway": "[gateway]",
        "method": "purchase",
        "first_name": "John",
        "last_name": "Smith",
        "number": "4242424242424242",
        "month": "10",
        "year": "2021",
        "verification_value": "123"
      }
    }
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
const orderId = 'XXXX'
​
Moltin.Orders.Payment(orderId, {
  gateway: '[gateway]',
  method: 'purchase',
  first_name: 'John',
  last_name: 'Smith',
  number: '4242424242424242',
  month: '10',
  year: '2021',
  verification_value: '123'
}).then(() => {
  // Do something
})
```
{% endtab %}
{% endtabs %}

### Third party payment gateways - detailed walkthrough 

Below, we document third party integrations Moltin natively supports, and supported payment methods. If you wish to use a provider that is not listed here, don't worry, you can still integrate it with Moltin using a [custom payment gateway](implement-own-payment-gateway.md).

{% tabs %}
{% tab title="Manual Gateway" %}
#### Manual \(custom\) gateway

The manual gateway allows you to record authorized and captured payments from third party payment gateways or through your own logic or business processes. See the [Custom Payment Gateways](implement-own-payment-gateway.md) for a comprehensive overview.
{% endtab %}

{% tab title="Stripe" %}
#### How Stripe works with Moltin - quick overview

The client application created with the Moltin API calls the Stripe API and passes to the browser cart details needed for checkout. Stripe validates the purchase data and returns a `token`/`source` Moltin API uses to complete the payment transaction.

#### Supported payment types:

* card payment
* token/source payment

#### Stripe integration

Making a payment through Stripe is a two-step process. you'll need to:

1. Collect customer's payment details outside of Moltin and [pass them to Stripe](https://stripe.com/docs/quickstart). Stripe returns a payment `token`.
2. Pass the `token` value in the `payment` field, and complete the payment transaction on Moltin's side. If successful, you'll get a `200` response. For more details, see the [API Reference](https://docs.moltin.com/payments/paying-for-an-order/stripe-payments).

**cURL**

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

**JavaScript SDK**

```javascript
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

Using `token`, as opposed to passing payment details directly through Moltin API, is a secure and preferred method, although it is also possible to provide payment details directly, in which case it is absolutely vital to use the HTTPS protocol.

#### Source vs. token

Rather than requesting a `token` back from Stripe, you can request a `source`, and pass it exactly like a token, through the `payment` field. Tokens are for single use only, and expire within minutes after their creation; whereas sources can be charged directly, or attached to customers for later reuse. For more details, see [Stripe documentation](https://stripe.com/docs/api/sources?lang=curl). 
{% endtab %}

{% tab title="CardConnect" %}
#### How CardConnect works with Moltin - quick overview

The client application created with the Moltin API calls the [CardConnect API](https://developer.cardconnect.com/cardconnect-api) and passes to the browser cart details needed for checkout. The transaction is then verified and processed outside of Moltin, and if successful brings back an empty `200 OK` response.

#### Supported payment types:

* card payment

For more details, see the [API Reference](https://docs.moltin.com/payments/paying-for-an-order/stripe-payments).
{% endtab %}

{% tab title="Adyen" %}
#### How Adyen works with Moltin - quick overview

The client application created with the Moltin API calls the [Adyen API](https://docs.adyen.com/developers) and passes to the browser cart details needed for checkout. The transaction is then securely transmitted to and from the Adyen payments platform, and if successful brings back the `200 OK` response.

#### Supported payment types:

* card payments

For more details, see the [API Reference](https://docs.moltin.com/payments/paying-for-an-order/adyen-payments).
{% endtab %}

{% tab title="Braintree" %}
#### How Braintree works with Moltin - quick overview

The client application created with the Moltin API calls the [Braintree API](https://developers.braintreepayments.com/) and passes to the browser cart details needed for checkout. The transaction is then securely transmitted to and from the Braintree payments platform, and if successful brings back the `200 OK` response. 

#### Supported payment types:

1. Card payment
2. Customer ID payment
3. Token payment
4. Nonce payment

For more details, see the [API Reference](https://docs.moltin.com/payments/paying-for-an-order/braintree-payments).

#### \(1.\) Braintree Card payment

See [Pay by card](payment-gateway-integration.md#pay-by-card) for details.

#### \(2.\) Customer ID payment

This method allows you to bill a specific Braintree customer. Braintree will be the default billing method in the customer's account.

#### cURL

```bash
curl -X POST https://api.moltin.com/v2/orders/:order_id/payments \
     -H "Authorization: Bearer XXXX" \
     -d $'{
  "data": {
    "gateway": "braintree",
    "method": "purchase",
    "payment": BRAINTREE_CUSTOMER_ID
  }
}'
```

#### JavaScript SDK

```javascript
Moltin.Orders.Payment('orderId', {
  gateway: "braintree",
  method: "purchase",
  payment: BRAINTREE_CUSTOMER_ID
}).then(() => {
  // Do something
});
```

#### \(3.\) Braintree token payment

 Pay for an order with a specific Braintree Payment Method Token. This is similar to the Customer ID payment type, but you can define a number of payment methods to use as Tokens.

#### cURL

```bash
curl -X POST https://api.moltin.com/v2/orders/:order_id/payments \
     -H "Authorization: Bearer XXXX" \
     -d $'{
  "data": {
    "gateway": "braintree",
    "method": "purchase",
    "payment": BRAINTREE_CUSTOMER_ID,
    "options": {
        "payment_method_token": BRAINTREE_PAYMENT_METHOD_TOKEN
    }
  }
}'
```

#### JavaScript SDK

```javascript
Moltin.Orders.Payment('orderId', {
  gateway: "braintree",
  method: "purchase",
  payment: BRAINTREE_CUSTOMER_ID,
  options: {
      payment_method_token: BRAINTREE_PAYMENT_METHOD_TOKEN
  }
}).then(() => {
  // Do something
});
```

**\(4.\) Braintree nonce payment**

Pay for an order with a previously created Braintree Payment Method Nonce. Use this method for a secure, one-time-use reference to transmit payment information when you wish your server to communicate sensitive payment information to Braintree without ever touching the raw data.

#### cURL

```bash
curl -X POST https://api.moltin.com/v2/orders/{ORDER_ID}/payments \
     -H "Authorization: Bearer XXXX" \
     -d $'{
  "data": {
    "gateway": "braintree",
    "method": "purchase",
    "payment": BRAINTREE_PAYMENT_NONCE,
    "options": {
        "payment_method_nonce": true
    }
  }
}'
```

#### JavaScript SDK

```javascript
Moltin.Orders.Payment('orderId', {
  gateway: "braintree",
  method: "purchase",
  payment: BRAINTREE_PAYMENT_NONCE,
  options: {
      payment_method_nonce: true
  }
}).then(() => {
  // Do something
});
```
{% endtab %}
{% endtabs %}



