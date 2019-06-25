# Currency

{% hint style="danger" %}
Content moved to [Developer Concepts](https://www.moltin.com/developer/concepts/currency).
{% endhint %}

Use currency settings to:

* Set up default currency
* Enable/disable existing currency
* Edit/delete existing currency
* Set up multiple currency types

### Set up currency

To set up a currency other than default, use [Moltin dashboard](https://dashboard.moltin.com/) or [API](https://docs.moltin.com/advanced/currencies).

#### Currency settings

Use the currency settings to manage how the price formatting is localized.

The formatted price can be found outside the regular price object for a product, in the meta object, which also includes timestamps, stock detail, etc.

Depending on localization, a common value for this field might be something like `${price}` , if the currency is to be USD. Canadian French however, uses the symbol as a suffix, so in that case it would be `{price}$`.

### Delete/disable currency

If a currency is no longer needed, you can delete it. Deleting destroys that `currency` object. The currency will be automatically deleted from all products that may have used it. If this was the only currency set up for the product, the product will have no currency, and if used in a request, the default currency will be used instead.

If a currency is no longer needed but you still want to retain it for for archiving purposes, disable it. Disabled currency will still show in the currency header, but it can’t be actively used.

### Edit currency

Edit the currency through [Moltin dashboard](https://dashboard.moltin.com/) or [API](https://docs.moltin.com/advanced/currencies). Editing the existing currency will update all products that have that currency set up.

### Display price vs. order price

When getting a list of orders, every order is displayed in the currency the order was taken in.

### Default currency

Default currency is USD and it’s also enabled by default. You can set up any currency as default, just make sure the currency is enabled either through [Moltin dashboard](https://dashboard.moltin.com/) or [API](https://docs.moltin.com/advanced/currencies). Default currency is used as a fallback currency in a request, if not currency header is provided.

### Multiple currency - key points:

* You can configure several currency types for your project.
* Carts don’t support multiple currency. If you add products with mixed currency, they will automatically fall back and use the default currency that is set up for these currency. Otherwise, `display_price` will be 0.

### Using currencies header with products

Typically, when a request comes into a gateway, the currency settings are attached to the request based on the `x-moltin-currency` header the user passes in, or the project’s default if none is passed. The currency settings are responsible for adapting the price format to the currency in use.

For projects that support multiple currency, use the `x-moltin-currencies` header that contains the formatting detail for all currencies available to the project.

```bash
[
    {
        "code": "GBP",
        "exchange_rate": 1,
        "format": "\u00A3{price}",
        "decimal_point": ".",
        "thousand_separator": ",",
        "decimal_places": 2
    },
    {
        "code": "USD",
        "exchange_rate": 0,
        "format": "${price}",
        "decimal_point": ".",
        "thousand_separator": ",",
        "decimal_places": 2
    }
]

```

This allows you to have a [collection of all currencies](https://docs.moltin.com/advanced/currencies), including ones that are disabled.

### How price is handled for orders

When the request gets to orders, the currency-related information from the header is put into the `meta` object of the response.

#### Price formatting

Price formatting is handled by Moltin. Once a cart converts into an order, the header provides all formatting details available for the currency specified in the order. If the currency doesn’t exist in the project, then `currencies.Get` will return sensible defaults, so the `display_price` values won’t be formatted, but the correct order currency will be displayed.

```
currency := currencies.Get(currencyCode)
```

###  

