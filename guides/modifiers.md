# Modifiers

Modifiers help augmenting properties of a variation of a product, price, etc., by creating an array of child products or prices. They help you keep your stock organized. For instance, instead of creating 3 different base products for three different shirt colors, you'll have one base product with 3 color variations. A modifier will then decide how to change a variation \(child\), for instance you can have three different descriptions of the base product split by their distinct colors displayed in your project. 

* See [Product Variations](product/using-product-variations.md) for more details on product-related variations.
* See [API Reference ](https://docs.moltin.com/~/drafts/-LJYudcRbr7F0jcg1KME/primary/catalog/product-variations/modifiers)documentation for more technical overview.

Below, we document the most commonly used scenarios for using variations and modifiers, on products and prices.

### Product Modifiers

Product modifiers create the variation products \(child products\) from the base product by augmenting different properties of that base product. 

Once a modifier type and its value has been specified, these will define how that property changes as the child products are built. The maximum number of child products generated from a base product cannot exceed 200.

{% tabs %}
{% tab title="Request" %}
```bash


```
{% endtab %}

{% tab title="Response" %}
```bash
{
     "data": {
   type": "product-modifier",
   "modifier_type": "description_append",
   "value": VALUE
     }
 }
```
{% endtab %}
{% endtabs %}

### Price Modifiers

Price modifiers help adjust the price of a product. As this kind of modifier deals with prices, the `value` of this modifier must be a collection of currency values, similar to that when specifying a product price. 

While the modifier can have any number of currencies applied to it, only the currencies specified on the actual base product will be subjected to any modifiers. For example, if you have USD and GBP values on a base product, and apply a modifier that alters GBP, AUD and EUR, the ONLY currency value affected will be GBP; the USD value will remain the same, and no other currencies will be set on the variation product.

{% tabs %}
{% tab title="Request" %}
```text

```
{% endtab %}

{% tab title="Response" %}
```text

```
{% endtab %}
{% endtabs %}

### SKU/Slug Builder Modifiers

Each variation must have at minimum `sku` and `slug` modifiers defined. These are mandatory, as they make each variation unique as a value.

The value of the modifier must contain two property-value pairs: `"seek": "XXX"` and `"set" : "YYY"`. In order for this kind of modifier to participate in the variation products building process, the base product should have a SKU/slug set with a place holder like: `{XXX}`. The modifier works by replacing the placeholder with the value you wish to set. You should only specify the contents of the `{ }` in the seek property - the modifier will take care of the rest.

**For example:**

BaseProduct SKU: `BP01-{COLOUR}-{SIZE}` Modifier1:`{"seek":"COLOUR", "set":"BLU"}` Modifier2:`{"seek":"COLOUR", "set":"RED"}`

ModifierA:`{"seek":"SIZE", "set":"LRG"}` ModifierB:`{"seek":"SIZE", "set":"SML"}`

The above modifiers applied via variations for size and colour would produce the following SKUs in the corresponding variation product:

`BP01-BLU-LRG` `BP01-BLU-SML` `BP01-RED-LRG` `BP01-RED-SML`

You could create the same via sku\_append modifier using values like `-RED` and `-LRG`; the advantage of the builder modifiers is that they are agnostic of the order the modifiers are applied.



