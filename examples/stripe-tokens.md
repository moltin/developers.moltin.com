---
description: Stripe tokens/sources and manual gateway
---

# Stripe Tokens

### Stripe tokens and sources {#stripe-tokens-and-sources}

First up is an extension to our Stripe payment options. We’ve added in support for payment [tokens](https://stripe.com/docs/api#tokens) and [sources](https://stripe.com/docs/api#sources). This means you can accept credit card payments on your Moltin store through Stripe without the users card details ever having to hit your server.

Using Stripe.js you can generate a single use card token which you then pass to Moltin when paying for the order.

An example using cURL:

```javascript
curl -X "POST" "https://api.moltin.com/v2/orders/{ORDER_ID}/payments" \
     -H "Authorization: Bearer XXXX" \
     -d $'{
  "data": {
    "gateway": "stripe",
    "method": "purchase",
    "payment": "tok_visa"
  }
}'
```

and our JS SDK:

```javascript
Moltin.Orders.Payment("orderId", {
  gateway: "stripe",
  method: "purchase",
  payment: "tok_visa"
}).then(() => {
  // Do something
})
```

### Manual Payments {#manual-payments}

We’ve also added a new gateway which we’ve named the Manual Gateway. This is perfect for stores that would like to use a gateway we haven’t added support for yet, or if you’d like to accept some form of offline payment. The payment process follows the standard authorize/capture process.

First of all, enable the manual gateway in the gateway section of your store dashboard. At the time of checkout and payment, you make an authorization request. We’ll update the payment status to `authorized` and the order status to `processing`. We’ll also create an authorization transaction on this order.

Using cURL:

```javascript
curl -X "POST" "https://api.moltin.com/v2/orders/{ORDER_ID}/payments" \
     -H "Authorization: Bearer XXXX" \
     -d $'{
  "data": {
    "gateway": "manual",
    "method": "authorize",
  }
}'
```

and our JavaScript SDK:

```javascript
Moltin.Orders.Payment('orderId', {
  gateway: "manual",
  method: "authorize"
}).then(() => {
  // Do something
})
```

Then you do whatever you need to do on your end to confirm the payment, this might be sending a request to another payment processor or it might be confirming that you have received an offline payment. Once you’re happy you’ve been paid, you can make a second capture request to mark the order as paid, the transaction ID is the ID of the transaction that was created during the `authorize`request.

```javascript
curl -X "POST" "https://api.moltin.com/v2/orders/{ORDER_ID}/transactions/{TRANSACTION_ID}/capture" \
     -H "Authorization: Bearer XXXX"
```

For security reasons, we only allow `client_credentials` tokens to perform this request. Alternatively, you can click the `Capture` button under any authorized transaction in your store dashboard.

