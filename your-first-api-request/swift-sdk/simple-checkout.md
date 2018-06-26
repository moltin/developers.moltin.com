---
description: A simple checkout flow that can be used as an example for your application
---

# Simple Checkout

### Get products

Now that we’ve authenticated with the Moltin API lets get one of our sample products. Please note you will need to create a product first via the API or store management dashboard.

This API call should contain an array of products. A good place to display this data is on a product or listing page.

```swift
moltin.product.all { (result) in
  switch result {
    case .success(let response):
      print("Got products: \(response.data)")
    case .failure(let error):
      print("Got failure: \(error)")
  }
}
```

### Add to cart

Using a product ID we can insert a quantity of this product into the cart with one simple request. The first parameter passed is the product ID, the second the quantity to be added to the cart. A third parameter can be added for modifiers and variations \(e.g. size and color\) if these are set up.

This response should contain an array of the individual cart item data e.g. title, quantity, price. A good example of integrating this would be to add a simple button or form to the product page that posts the product ID and quantity to this endpoint.

In your AppDelegate set up a cartID which can be used to keep track of items the user adds to his cart.  We recommend you generate a uuid if you do not have a cart id in use already.

```swift
static let cartID = UUID().uuidString
```

```swift
moltin.cart.items(forCartID: AppDelegate.cartID) { (result) in
  switch result {
    case .success(let response):
      print("Got items: \(response.data)")
    case .failure(let error):
      print("Got failure: \(error)")
  }
}
```

### Convert to order

When the customer is ready to checkout we need to convert the cart to an order. This call lets you define the payment gateway and conditional order parameters such as customer, billing and shipping addresses.

This call will return an object containing data for the newly created order.

```swift
let customer = Customer(withEmail: "XXXX", withName: "XXXX")
let billingAddress = Address(withFirstName: "XXXX", withLastName: "XXXX")billingAddress.line1 = "XXXX"billingAddress.county = "XXXX"billingAddress.country = "XXXX"billingAddress.postcode = "XXXX"moltin.cart.checkout(cart: "XXXX",withCustomer: customer,withBillingAddress: billingAddress,withShippingAddress: nil) { (result) in  switch result {    case .success(let order):      print("Created order: \(order)")    case .failure(let error):      print("Could not create order: \(error)")  }}
```

### Process payment

Once we’ve converted a cart into an order we’re now ready to process a payment. In this example, we’ve used the dummy gateway so we just need to provide some card details. The data you need to provide in this step depends on your chosen gateway.

Congratulations, if you made it this far you’ve implemented a simple step by step checkout!

```swift
let paymentMethod: PaymentMethod = StripeCard(withFirstName: "XXXX",
  withLastName: "XXXX",
  withCardNumber: "XXXX",
  withExpiryMonth: "XXXX",
  withCVVNumber: "XXXX")
moltin.cart.pay(forOrderID: "XXXX",
withPaymentMethod: paymentMethod) { (result) in
  switch result {
    case .success(let status):
      print("Paid for order: \(status)")
    case .failure(let error):
      print("Could not pay for order: \(error)")
  }
}
```

