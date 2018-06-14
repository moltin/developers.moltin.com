# Shipping Rates

In this guide you will learn how to get shipping rates from [Shippo](https://goshipp.com/) and how you can use these during checkout as custom cart items.

The moltin cart API allows you to add custom items that do not need to belong in your project catalogue or inventory manifest. This gives you the power to calculate any additional costs and add them as cart items, such as shipping and delivery charges.

One of the easiest ways to calculate shipping costs is to use Shippo, an established multi-carrier shipping API. They connect multiple worldwide carriers, rates and automatically generate shipping labels, in a few API calls.

### Setup

This tutorial will assume you are comfortable writing JavaScript and using the moltin [JavaScript SDK](https://github.com/moltin/js-sdk) within your project.

You should head to [Shippo](https://goshippo.com/), signup and get your API `test token` if you haven't already.

Next let's install the `shippo` npm package.

```bash
npm install --save shippo
```

Now inside of your project, you'll want to instantiate the shippo library with your test Shippo API token.

```javascript
const shippo = require('shippo')('<SHIPPO_API_TOKEN>')
```

### Get rates from Shippo

What you do with the rates is up to you. You may wish to present all available rates from Shippo to the user so they can select which they prefer, or you could select a one for them as default.

The checkout is where normally you'd find shipping rates and delivery terms. With that in mind, let's assume your checkout page has already gathered a customer delivery address.

First, let's create an async function that accepts 3 parameters, `address_from`, `address_to` and `parcels`.

```javascript
async function createShipment(address_from, address_to, parcels) {
  const { rates } = await shippo.shipment.create({
    address_from,
    address_to,
    parcels,
    async: false
  })

  return rates
}
```

The `address_from` and `address_to` objects should have the following structure. `country` must be an [`iso2` country code](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2).

```javascript
{
  "name": "...",
  "company": "...",
  "street1": "...",
  "city": "...",
  "state": "...",
  "zip": "...",
  "country": "...",
  "phone": "...",
  "email": "..."
}
```

The 3rd parameter `parcels` is an `array` of parcels with the following structure:

```javascript
{
  "length": "50",
  "width": "100",
  "height": "5",
  "distance_unit": "in",
  "weight": "2",
  "mass_unit": "lb"
}
```

Finally, if you go ahead and run the `createShipment` function, you'll have some `rates` we can use to create a shipping label.

```javascript
const from = {
  //
}

const to = {
  //
}

const parcels = [{
  "length": "25",
  "width": "50",
  "height": "2.5",
  "distance_unit": "in",
  "weight": "5",
  "mass_unit": "lb"
}, {
  "length": "50",
  "width": "100",
  "height": "5",
  "distance_unit": "in",
  "weight": "10",
  "mass_unit": "lb"
}]

const rates = createShipment(from, to, parcels)
```

The response contains helpful attributes like `BEST_VALUE` and `CHEAPEST`. It's at this point you could return the response from `createShipment` to your customers inside the checkout.

Make sure to make note of a rate `object_id` and `amount` so we can use it in the next steps.

### Create a transaction + shipping label

Once you or a customer selects a rate, you can send on the request to create a transaction and shipping label with Shippo. Let's create a function that wraps the `shippo.transaction` method, so we can easily reuse this inside our app.

```javascript
async function createTransaction(rate, label_file_type) {
  return await shippo.transaction.create({
    rate,
    label_file_type,
    async
  })
})

const transaction = createTransaction(object_id, 'PDF')
```

### Add to cart

Now you have created a Shippo transaction, you have all of the information you need to add a custom cart item. If you're allowing customers to select a shipping rate, you'll want to make sure it runs at the same time as `createTransaction`.

Let's go ahead and create a reusable `addCustomCartItem` function.

```javascript
async function addCustomCartItem(cartId, item) {
  return await Moltin.Cart(cartId).AddCustomItem(item)
})

const cartId = 'abc'

const items = addCustomCartItem(cartId, {
  name: 'Delivery charge',
  sku: 'shippo-delivery-charge',
  description: 'Delivery charge',
  quantity: 1,
  price: {
    amount:
  }
})
```

This function will return the moltin cart items. It's at this point you may wish to update your applications checkout total.

