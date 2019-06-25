# Apply custom tax items

{% hint style="danger" %}
Content moved to [Developer Concepts](https://www.moltin.com/developer/concepts/how-taxes-work).
{% endhint %}

Add tax to items in your cart to apply relevant taxes for checked out products.  

This guide will show you how to apply a flat rate sales tax to every cart item in your Moltin cart.   For more information on the tax object, see the [API Reference](https://docs.moltin.com/carts-and-checkout/carts/cart-items/tax-items#the-tax-item-object).

{% hint style="info" %}
Moltin lets you apply any tax rates to a cart. How you calculate the rates is up to you, and so this guide assumes that you already know how to calculate tax rates. Otherwise, you'll need to integrate a third party tax calculator. The [Integrate TaxJar with Moltin](taxes-integrate-taxjar-with-moltin.md) guide covers this scenario in details.
{% endhint %}

### Prerequisites

* [Moltin account](https://accounts.moltin.com/)
* Products configured through [Moltin dashboard](https://dashboard.moltin.com/) or through the API with the `includes_tax` flag set to true.

### Key points about taxes and how they work

* **Tax calculation logic.**  The `includes_tax` field on the product can be set to true or false:
  * `true` Moltin will perform a backward calculation of taxes meaning the original product price will not be affected during checkout.
  * `false` Moltin will calculate tax and add it to the original product price.
* **Promotions and Taxes.** How taxes are calculated depends on the [calculation method](../../calculation-methods.md) and may be added before or after promotional savings have been applied.
* **Cart-level promotions.** The way promotional savings are applied to carts depends on the [calculation method](../../calculation-methods.md). They may either be applied as negative cart items or equally distributed across all items in the cart. If there is a remainder after distributing promotional savings across cart items, the promo saving will be rounded up, so that there is no remainder left.

### **Add a product to a cart**

Add a product to a cart. If the product has the `includes_tax` field set to `true`, and you have created a tax item, the cart will apply the calculated tax rate to the product as soon as you add it to the cart.

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST https://api.moltin.com/v2/carts/:reference/items \
     -H "Authorization: Bearer XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
         "data": {
              "type": "cart_item",
              "id": "d4fb11d4-2fd6-43a1-a047-7b869d41cf96",
              "quantity": 4
  }
}
```
{% endtab %}
{% endtabs %}

### **Add a tax item to a cart**

To apply a tax item to a cart item, all you have to do is to add a tax item to a cart. 

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST "https://api.moltin.com/v2/carts/:cartID/items/:itemID/taxes" \      
-H "Authorization: XXXX" \      
-H "Content-Type: application/json" \      
    -d $'{
        "data": {
            "type": "tax_item",
            "name": "Tax Name",
            "jurisdiction": "UK",
            "code": "MYTAX01",
            "rate": 10.2
  }
}' 
```
{% endtab %}

{% tab title="Response" %}
```bash
{
    "data": {
        "type": "tax_item",
        "id": "86ea1135-b3c2-4227-9490-4a89ec4ac873",
        "jurisdiction": "UK",
        "code": "MYTAX01",
        "name": "Tax Name",
        "rate": 10.2
    }
}
```
{% endtab %}
{% endtabs %}

### Get taxed item

Relationships between cart items and ****tax items are created automatically when the product is added to a cart. 

{% hint style="info" %}
Product added to a cart must have the `includes_tax` field set up to true.
{% endhint %}

{% tabs %}
{% tab title="Response" %}
```bash
{
    "data": [
        {
            "id": "31f15e3d-951f-48ba-a1df-e1ab7d827af3",
            "type": "cart_item",
            "product_id": "d4fb11d4-2fd6-43a1-a047-7b869d41cf96",
            "name": "brioche",
            "description": "Category: Sweet. Yoshi-shaped brioche.",
            "sku": "YO-BR-01",
            "image": {
                "mime_type": "image/png",
                "file_name": "yoshi_brioche.png",
                "href": "https://s3-eu-west-1.amazonaws.com/bkt-svc-files-cmty-api-moltin-com/646e910b-b7ad-409e-b1e6-c84ce1aa70cb/f67af44f-383e-40f8-a5e8-3296fe9d473e.png"
            },
            "quantity": 4,
            "manage_stock": false,
            "unit_price": {
                "amount": 499,
                "currency": "USD",
                "includes_tax": true
            },
            "value": {
                "amount": 1996,
                "currency": "USD",
                "includes_tax": true
            },
            "links": {
                "product": "https://api.moltin.com/products/d4fb11d4-2fd6-43a1-a047-7b869d41cf96"
            },
            "relationships": {
                "taxes": {
                    "data": [
                        {
                            "type": "tax_item",
                            "id": "86ea1135-b3c2-4227-9490-4a89ec4ac873"
                        }
                    ]
                }
            },
            "meta": {
                "display_price": {
                    "with_tax": {
                        "unit": {
                            "amount": 504,
                            "currency": "USD",
                            "formatted": "$5.04"
                        },
                        "value": {
                            "amount": 2016,
                            "currency": "USD",
                            "formatted": "$20.16"
                        }
                    },
                    "without_tax": {
                        "unit": {
                            "amount": 45,
                            "currency": "USD",
                            "formatted": "$0.45"
                        },
                        "value": {
                            "amount": 180,
                            "currency": "USD",
                            "formatted": "$1.80"
                        }
                    },
                    "tax": {
                        "unit": {
                            "amount": 459,
                            "currency": "USD",
                            "formatted": "$4.59"
                        },
                        "value": {
                            "amount": 1836,
                            "currency": "USD",
                            "formatted": "$18.36"
                        }
                    }
                },
                "timestamps": {
                    "created_at": "2019-01-28T09:02:38Z",
                    "updated_at": "2019-01-28T10:45:25Z"
                }
            }
        }
    ],
    "meta": {
        "display_price": {
            "with_tax": {
                "amount": 2016,
                "currency": "USD",
                "formatted": "$20.16"
            },
            "without_tax": {
                "amount": 180,
                "currency": "USD",
                "formatted": "$1.80"
            },
            "tax": {
                "amount": 1836,
                "currency": "USD",
                "formatted": "$18.36"
            }
        },
        "timestamps": {
            "created_at": "2019-01-28T09:02:38Z",
            "updated_at": "2019-01-28T10:45:25Z"
        }
    }
}
```
{% endtab %}
{% endtabs %}

