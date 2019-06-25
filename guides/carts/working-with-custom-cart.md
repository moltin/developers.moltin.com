# Working with Custom Cart

{% hint style="danger" %}
Content moved to [Developer Concepts](https://www.moltin.com/developer/concepts/when-to-use-a-custom-cart).
{% endhint %}

Custom carts allow you to process an order for any type of item you wish to sell. The item does not need to exists in Moltin, which means custom cart items can be used for all sort of use cases.

**Places in the user flow you would use Custom cart**

1. To store a product that does not live in Moltin for a user to buy.
2. To adjust a product price the user is going to buy.
3. To add an incremental fee to the user, that is not a product. 

We will walk you through the three common ways custom carts are leveraged. There is a playground site set up that you can use to follow along and experiment with your own specific use cases.

Playground to follow along: [https://moltin-cart-example.herokuapp.com/index.html](https://moltin-cart-example.herokuapp.com/index.html)

Code to follow along: [https://gist.github.com/gje4/3a42d1984dcf4f5bed77d24a21a5d9b3](https://gist.github.com/gje4/165d0570a3e4d4fea56a1acc34991c31)

### Scenario 1 Allow a user to purchase an item that doesn't exist in Moltin

**Setup**:  A made-up product that does not exist in Moltin. This means it will not have a product id.

**Scenario**: A user is purchasing an item that was custom made on your site.

{% tabs %}
{% tab title="Example Item" %}
| Field name | Type | Description |
| :--- | :--- | :--- |
| `name` | `string` | The name of the product. |
| `sku` | `string` | The unique SKU of the product. |
| `description` | `string` | The description of the product. |
| `quantity` | `integer` | The quantity of the product. Should default to 1. |
| `price.amount` | `integer` | The price of the product. |
{% endtab %}

{% tab title="Moltin Object" %}
```javascript
}
  type: "custom_item",
  name: "Black t-shirt with dragon",
  sku: "123029123",
  description: "This is a custom made t-shirt",
  quantity: 1,
  price: {
        "amount": 10000
  }
}
```
{% endtab %}
{% endtabs %}

**Example code:**

{% tabs %}
{% tab title="Custom cart" %}
```javascript
  const item = {
    //Type will alyways be custom_item
    type: "custom_item",
    name: productName.value,
    sku: productSku.value,
    description: productDesc.value,
    quantity: productQty.value,
    price: {
      amount: productAmount.value
    }
  }

    Moltin.Cart(customCartId)
      .AddCustomItem(item)
      .then(cart => {
        console.log(JSON.stringify(cart))
    })
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
    "data": [
        {
            "id": "827a448e-c87a-480b-a7f3-d725a5c5507f",
            "type": "custom_item",
            "name": "Black t-shirt with dragon",
            "description": "This is a custom made t-shirt",
            "sku": "123029123",
            "image": {
                "mime_type": "",
                "file_name": "",
                "href": ""
            },
            "quantity": 1,
            "manage_stock": false,
            "unit_price": {
                "amount": 10000,
                "currency": "USD",
                "includes_tax": true
            },
            "value": {
                "amount": 10000,
                "currency": "USD",
                "includes_tax": true
            },
            "links": {},
            "meta": {
                "display_price": {
                    "with_tax": {
                        "unit": {
                            "amount": 10000,
                            "currency": "USD",
                            "formatted": "$100.00"
                        },
                        "value": {
                            "amount": 10000,
                            "currency": "USD",
                            "formatted": "$100.00"
                        }
                    },
                    "without_tax": {
                        "unit": {
                            "amount": 10000,
                            "currency": "USD",
                            "formatted": "$100.00"
                        },
                        "value": {
                            "amount": 10000,
                            "currency": "USD",
                            "formatted": "$100.00"
                        }
                    }
                },
                "timestamps": {
                    "created_at": "2018-10-12T14:53:52Z",
                    "updated_at": "2018-10-12T14:53:52Z"
                }
            }
        }
    ],
    "meta": {
        "display_price": {
            "with_tax": {
                "amount": 10000,
                "currency": "USD",
                "formatted": "$100.00"
            },
            "without_tax": {
                "amount": 10000,
                "currency": "USD",
                "formatted": "$100.00"
            }
        },
        "timestamps": {
            "created_at": "2018-10-12T14:53:52Z",
            "updated_at": "2018-10-12T14:53:52Z"
        }
    }
}
```
{% endtab %}
{% endtabs %}

### Scenario 2 Add shipping fees to a product

**Setup**: A user may want to buy a product from a different country which requires an additional shipping fee. In this example, we will apply a flat fee. This is meant to give you a basic example of how this could work with your own internal shipping logic or a third party shipping API.

**Scenario**: A user based in Newcastle, UK wishes to buy a shirt from Boston, US. This is going to cost more than what was originally accounted for in shipping fees.

{% tabs %}
{% tab title="Example Item" %}
| Field name | Type | Description |
| :--- | :--- | :--- |
| `name` | `string` | The name of the product. |
| `sku` | `string` | The unique SKU of the product. |
| `description` | `string` | The description of the product. |
| `quantity` | `integer` | The quantity of the product. Should default to 1. |
| `price.amount` | `integer` | The price of the product. |
{% endtab %}

{% tab title="Moltin Object" %}
```javascript
{
	type: "custom_item",
	name: "Black t-shirt with dragon",
	sku: "123029123",
	description: "This is a custom made t-shirt",
	quantity: 1,
	price: {
		amount: 10000
	}
}
```
{% endtab %}
{% endtabs %}

 **Example code:**

{% tabs %}
{% tab title="Custom cart" %}
```javascript
const shippingCountry = "UK"
let item
if (shippingCountry != "US") {
	item = {
		type: "custom_item",
		name: "Black t-shirt with dragon",
		sku: "123029123",
		description: "This is a custom made t-shirt",
		quantity: 1,
		price: {
			amount: 10000 + 500
		}
	}
}
Moltin.Cart(exampleCart2).AddCustomItem(item).then(cart => {
	console.log(JSON.stringify(cart))
})
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
	"data": [{
		"id": "fefed55b-6f67-4c5c-9253-c091043c3983",
		"type": "custom_item",
		"name": "Black t-shirt with dragon",
		"description": "This is a custom made t-shirt",
		"sku": "123029123",
		"image": {
			"mime_type": "",
			"file_name": "",
			"href": ""
		},
		"quantity": 1,
		"manage_stock": false,
		"unit_price": {
			"amount": 10500,
			"currency": "USD",
			"includes_tax": true
		},
		"value": {
			"amount": 10500,
			"currency": "USD",
			"includes_tax": true
		},
		"links": {},
		"meta": {
			"display_price": {
				"with_tax": {
					"unit": {
						"amount": 10500,
						"currency": "USD",
						"formatted": "$105.00"
					},
					"value": {
						"amount": 10500,
						"currency": "USD",
						"formatted": "$105.00"
					}
				},
				"without_tax": {
					"unit": {
						"amount": 10500,
						"currency": "USD",
						"formatted": "$105.00"
					},
					"value": {
						"amount": 10500,
						"currency": "USD",
						"formatted": "$105.00"
					}
				}
			},
			"timestamps": {
				"created_at": "2018-10-12T16:14:05Z",
				"updated_at": "2018-10-12T16:14:05Z"
			}
		}
	}],
	"meta": {
		"display_price": {
			"with_tax": {
				"amount": 10500,
				"currency": "USD",
				"formatted": "$105.00"
			},
			"without_tax": {
				"amount": 10500,
				"currency": "USD",
				"formatted": "$105.00"
			}
		},
		"timestamps": {
			"created_at": "2018-10-12T16:14:05Z",
			"updated_at": "2018-10-12T16:14:05Z"
		}
	}
}
```
{% endtab %}
{% endtabs %}

### Scenario 3 Add tax to a cart

**Setup**: You may want to collect a set amount of tax after you find out where the user is shipping the product to. You are able to add a tax amount as an item to the user's order to account for the tax you need to collect. This is a basic example of how this could work with your own internal tax logic or a third party API.

**Scenario**: A user based in Boston wants to buy a bottle of wine from your California vineyard. You need to collect 7.25% tax on the order.

{% tabs %}
{% tab title="Example Item" %}
| Field name | Type | Description |
| :--- | :--- | :--- |
| `name` | `string` | The name of the product. |
| `sku` | `string` | The unique SKU of the product. |
| `description` | `string` | The description of the product. |
| `quantity` | `integer` | The quantity of the product. Should default to 1. |
| `price.amount` | `integer` | The price of the product. |
{% endtab %}

{% tab title="Moltin Object" %}
```javascript
{
	type: "custom_item",
	name: "CA TAX",
	sku: "9238091",
	description: "TAX from CA to ship out of state.",
	quantity: 1,
	price: {
		amount: 210000 * .0725
	}
}
```
{% endtab %}
{% endtabs %}

 **Example code:**

{% tabs %}
{% tab title="Custom cart" %}
```javascript
const shippingCountry = "NY"
let item
if (shippingState != "CA") {
	item = {
		//Type will alyways be custom_item
		type: "custom_item",
		name: "CA TAX",
		sku: "9238091",
		description: "TAX from CA to ship out of state.",
		quantity: 1,
		price: {
			amount: 210000 * .0725
		}
	}
}
Moltin.Cart(exampleCart3)
	.AddCustomItem(item)
	.then(cart => {
		document.example3Frame.document.body.innerHTML = JSON.stringify(cart)
		console.log(JSON.stringify(cart))
	})
}
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
  "data": [
    {
      "id": "9c76f62a-edc2-4330-b3ca-49f83415d493",
      "type": "custom_item",
      "name": "CA Tax",
      "description": "TAX from CA to ship out of state.",
      "sku": "9238091",
      "image": {
        "mime_type": "",
        "file_name": "",
        "href": ""
      },
      "quantity": 1,
      "manage_stock": false,
      "unit_price": {
        "amount": 15224,
        "currency": "USD",
        "includes_tax": true
      },
      "value": {
        "amount": 15224,
        "currency": "USD",
        "includes_tax": true
      },
      "links": {},
      "meta": {
        "display_price": {
          "with_tax": {
            "unit": {
              "amount": 15224,
              "currency": "USD",
              "formatted": "$152.24"
            },
            "value": {
              "amount": 15224,
              "currency": "USD",
              "formatted": "$152.24"
            }
          },
          "without_tax": {
            "unit": {
              "amount": 15224,
              "currency": "USD",
              "formatted": "$152.24"
            },
            "value": {
              "amount": 15224,
              "currency": "USD",
              "formatted": "$152.24"
            }
          }
        },
        "timestamps": {
          "created_at": "2018-10-12T19:33:51Z",
          "updated_at": "2018-10-12T19:33:51Z"
        }
      }
    }
  ],
  "meta": {
    "display_price": {
      "with_tax": {
        "amount": 15224,
        "currency": "USD",
        "formatted": "$152.24"
      },
      "without_tax": {
        "amount": 15224,
        "currency": "USD",
        "formatted": "$152.24"
      }
    },
    "timestamps": {
      "created_at": "2018-10-12T19:33:51Z",
      "updated_at": "2018-10-12T19:33:51Z"
    }
  }
}
```
{% endtab %}
{% endtabs %}

### Summary

The cart service flexibility is a powerful service, and allows you to enable all sorts of commerce. The use cases are endless for the custom cart, and can be used to solve any roadblocks you might have when interacting with customers.  

### Where to go from here

These basic examples are meant to give you an idea of how you could use a custom cart. A logical next step would be to integrate your own or third party logic with the custom cart. An example of third party API to get tax rates or arrange shipping would be TaxJar or Shippo.

