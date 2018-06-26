---
description: A simple checkout flow that can be used as an example for your application
---

# Simple Checkout

### Get products

Now that we’ve authenticated with the Moltin API lets get one of our sample products. Please note you will need to create a product first via the API or store management dashboard.

This API call should contain an array of products. A good place to display this data is on a product or listing page.

```php
$products = $moltin->products->all()->data();
```

### Add to cart

```php
$moltin->cart()->addProduct($productID, 3);
```

### Get cart contents

Now that there is at least one product in the cart let’s get the full cart contents.

This response should contain an array of all cart items and cart totals. A good place to show this data would be on the cart or orders page, or even a widget in the header of your website.

```php
$items = $moltin->cart()->items();
```

### Convert to order

When the customer is ready to checkout we need to convert the cart to an order. This call lets you define the payment gateway and conditional order parameters such as customer, billing and shipping addresses.

This call will return an object containing data for the newly created order.  


```php
$order = $moltin->cart()->checkout(
  [
    'name'  => 'Jon Doe',
    'email' => 'jon.doe@gmail.com'
  ],
  [
    'first_name' => 'Jon',
    'last_name' => 'Doe',
    'line_1' => '123 Sunny Street',
    'line_2' => 'Sunnycreek',
    'county' => 'California',
    'postcode' => 'CA94040',
    'country' => 'US'
  ]
);
```

### Process payment

Once we’ve converted a cart into an order we’re now ready to process a payment. In this example, we’ve used the dummy gateway so we just need to provide some card details. The data you need to provide in this step depends on your chosen gateway.

Congratulations, if you made it this far you’ve implemented a simple step by step checkout!

```php
$payment = $order->pay('stripe', 'purchase', [
  'first_name' => 'Jon',
  'last_name' => 'Doe',
  'number' => '4242424242424242',
  'month' => 02,
  'year' => 2020,
  'verification_value' => 213
]);
```

