# Extend the order and cart resources

Extend the order resource to add shipping provider and a consignment number.![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

### Prerequisites

* Moltin account \(you'll need Client ID and Client Secret of your store\).
* Basic understanding of object-oriented programming or JavaScript.

### Summary of steps required

**Create a custom Flow for shipping providers:**

1. Create shipping provider flow
2. Create a name and cost fields

**Extend orders Flow:**

1. Create consignment id field
2. Create shipping provider relationship field that links to the orders flow.

**Create shipping providers:**

1. Create entries that will store your shipping providers.

**Extend a cart:**

1. Extend carts flow
2. Create shipping provider relationship field that will link to the carts flow

**Create an order:**

1. Get a cart
2. Add an item to a cart
3. Add a custom item that contains shipping charges to a cart
4. Checkout your cart
5. Create shipping provider relationship field that links to the orders flow
6. Update the order to add shipping details

### Step-by-step walkthrough

The steps below will show you how to:

1. Create a custom Flow to store shipping providers.
2. Extend the existing resource `carts`, so that you can charge your customer for shipping.
3. Extend an existing resource `orders`, so it displays shipping provider details and the consignment number for each order charged with a shipping fee.

#### 1. Get your access token

You will need to get a `client_credentials` access token to follow along making the API requests outlined below.

```bash
curl -X POST https://api.moltin.com/oauth/access_token \
     -d "client_id=XXXX" \
     -d "client_secret=XXXX" \
     -d "grant_type=client_credentials"
```

#### 2. Create a custom Flow \(shipping provider\)

We'll start with creating a custom Flow that will store our shipping providers.

```bash
curl -X POST https://api.moltin.com/v2/flows \ 
     -H "Authorization:XXXX" \ 
     -H "Content-Type:application/json" \ 
     -d $'{  
        "data":{  
          "type":"flow",
          "name":"shipping_provider",
          "slug":"shipping_provider",
          "description":"Creates a new object",
          "enabled":true
        }
     }'
```

### 3. Create Fields for Shipping Provider Flow

The Fields will be returned when you call the object you've created. Replace `FLOW_ID` with the ID that was generated for the Flow \(see step 2\). In this scenario we will create two fields: **Name** for storing each shipping providers name and **Cost** to store the shipping charges. Code example below shows you how to add the **Name** field. Repeat the request for the Cost field, changing the details accordingly.

```bash
curl -X POST https://api.moltin.com/v2/fields \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
  	"data": {
    	  "type": "field",
    	  "name": "Name",
    	  "slug": "name",
    	  "field_type": "string",
    	  "description": "Shipping providers name",
    	  "required": false,
    	  "unique": false,
    	  "default": 0,
    	  "enabled": true,
    	  "order": 1,
    	  "relationships": {
             "flow": {
               "data": {
                 "type": "flow",
                 "id": "{{SHIPPING_PROVIDER_FLOW_ID}}"
            }
        }
     }
  }
}'
```

#### 4. Create a core Flow for orders

Create a new core Flow that will extend your `orders` resource. Note that `slug` must match the plural name of the resource you're extending, i.e. `orders`.

**If you already have a Flow for your existing resource, skip this step. You'll need the ID of the `orders` Flow to continue.**

```bash
curl -X POST https://api.moltin.com/v2/flows \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "flow",
          "name": "orders",
          "slug": "orders",
          "description": "flow description",
          "enabled": true
        }
     }'
```

Take note of the Flow's id that is returned. You'll need this to create a Flow field.

#### 5. Create a Consignment ID Field

The Field will be returned when you call the object you've extended. Replace `FLOW_ID` with the ID that was generated for the Flow \(see step 2\). We will create the **Consignment ID** field that will store and display the consignment number for each order. This scenario assumes that every order can only have one shipping provider associated with it. To cater to this requirement, we will need to add a `one-to-one` relationship as part of the validation rules.

```bash
curl -X POST https://api.moltin.com/v2/fields \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{ 
          "data": {
            "type": "field",
            "name": "Consignment ID",
            "slug": "consignment-id",
            "field_type": "string",
            "description": "field description",
            "required": false,
            "unique": false,
            "default": 0,
            "enabled": true,
            "order": 1,
            "relationships": {
              "flow": {
                "data": {
                  "type": "flow",
                  "id": "{{ORDERS_FLOW_ID}}"
               }
            }
        }
    }
}'
```

#### 6. Create a Shipping Provider relationship Field

Now, we need to create a relationship between orders and shipping provider Flows. To do this, we need to add a `relationship` type of a `field_type`.

```bash
curl -X POST https://api.moltin.com/v2/flows \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
     	"data":{  
   	    "type":"field",
   	    "name":"Shipping Provider",
   	    "slug":"shipping-provider",
   	    "description":"The shipping provider used to send the order",
   	    "required":false,
   	    "unique":false,
   	    "field_type":"relationship",
   	    "relationships":{  
      	    "flow":{  
              "data":{  
            	 "type":"flow",
            	 "id":"{{ORDERS_FLOW_ID}}"
                    }
      		     }
   		     },
   	     "validation_rules": [{  
            "type":"one-to-one",
            "to":"shipping-provider"
      }]
   }'
```

#### 7. Create shipping providers Entries

Entries represent the actual content each Field will contain. Create an Entry for every Field added. In this example, we will add Entries to the Shipping Provider Flow Fields we created in step 3. These were **Name** and **Cost**, and this is exactly what we'll be adding a name of a shipping provider and a cost of shipping.

```bash
curl -X POST https://api.moltin.com/v2/flows/{{FLOWS_SLUG}}/entries \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
   	      "type": "entry",
    	    "name": "Ralphio 720",
    	    "cost": 7.2
  	     }
	  }'
```

#### 8. Create a core Flow for carts

Create a new core Flow that will extend your `carts` resource. Note that `slug` must match the plural name of the resource you're extending, i.e. `carts`.

**If you already have a Flow for your existing resource, skip this step. You'll need the ID of the `carts` Flow to continue.**

```bash
curl -X POST https://api.moltin.com/v2/flows \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "flow",
          "name": "carts",
          "slug": "carts",
          "description": "flow description",
          "enabled": true
        }
     }'
```

#### 9. Create a Shipping Provider relationship Field

Now it's time to create a relationship between the Shipping Provider Flow and the carts Flow, just like we did in step 6 for the orders Flow.

```bash
curl -X POST https://api.moltin.com/v2/flows \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
     	"data":{  
   	  "type":"field",
   	  "name":"Shipping Provider",
   	  "slug":"shipping-provider",
   	  "description":"The shipping provider used to send the order",
   	  "required":false,
   	  "unique":false,
   	  "field_type":"relationship",
   	  "relationships":{  
      	 "flow":{  
           "data":{  
             "type":"flow",
             "id":"{{CARTS_FLOW_ID}}"
                       }
      		     }
   		   },
   	   "validation_rules": [{  
          "type":"one-to-one",
          "to":"shipping-provider"
      }]
   }'
```

#### 10. Create an order

This scenario assumes you already have your store set up with at least products and payment details. The order we will create here will be unpaid, but you'll typically need to [authorise and capture](https://docs.moltin.com/api/payments/paying-for-an-order) the payment. Here, we'll just show steps necessary to apply a shipping charge and to display shipping details on the order:

1. Get a cart.
2. Add an item to the cart \(you'll need a product id\).
3. Add a custom item \(shipping charge\) to the cart.
4. Checkout the cart with the customer object.
5. Update the order with the Consignment ID returned by the shipper.
6. Get the order with the shipping details.

### A. Get a cart

Make sure you replace the {{CART\_REFERENCE}} with a value, e.g. `1234`.

```bash
curl -X GET https://api.moltin.com/v2/carts/{{CART_REFERENCE}} \
     -H "Authorization: Bearer XXXX"
```

### B. Add an item to the cart

Here, you'll need the product id that you have previously added to your store.

```bash
curl -X POST https://api.moltin.com/v2/carts/{{CART_REFERENCE}}/items \
     -H "Authorization: Bearer XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
      "data": {
          "id": "{{PRODUCT_ID}}",
          "type": "cart_item",
          "quantity": 1
        }
      }'
```

### C. Add a custom item to the cart

A custom item allows you to add additional charges like a shipping fee. Here, we'll use one of the shipping provider Flow Entries we've created in step 7.

```bash
curl -X POST https://api.moltin.com/v2/carts/{{CART_REFERENCE}}/items \
     -H "Authorization: Bearer XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
      "data": {
        "type": "custom_item",
        "name": "Ralphio 720",
        "sku": "ralphio-720",
        "description": "Adds charges for shipping",
        "quantity": 1,
        "price": {
          "amount": 720
        }
      }
    }'
```

### D. Checkout the cart with the customer object

The customer object allows for anonymous checkout, i.e. you don't have to have any customer details previously added to your store.

```bash
curl -X POST https://api.moltin.com/v2/carts/{{CART_REFERENCE}}/checkout \
     -H "Authorization: Bearer XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
       "data": {
         "customer": {
           "email": "leslie.knope@gov.com",
           "name": "Leslie Knope"
         },
         "billing_address": {
           "first_name": "Leslie",
           "last_name": "Knope",
           "company_name": "Parks and Recreations Dept.",
           "line_1": "2nd Floor British India House",
           "line_2": "15 Carliol Square",
           "city": "Pawnee",
           "postcode": "NE1 6UF",
           "county": "Indiana",
           "country": "US"
         },
         "shipping_address": {
           "first_name": "Leslie",
           "last_name": "Knope",
           "company_name": "Parks and Recreations Dept.",
           "phone_number": "(555) 555-1234",
           "line_1": "2nd Floor British India House",
           "line_2": "15 Carliol Square",
           "city": "Pawnee",
           "postcode": "NE1 6UF",
           "county": "Indiana",
           "country": "US",
           "instructions": "Leave with Ron Swanson"
         }
       }
    }'
```

At this point the order is created. Make note of the order id sent in the response.

### Update the order with the Consignment ID

Typically, once the order is sent for shipping, the shipping provider returns a consignment id by which you can track the shipment. We have already create a field for that, so all we need to do at this point is to add this information to our order.

```bash
curl -X PUT https://api.moltin.com/v2/orders/{{ORDER_ID}}\
    -H "Authorization: Bearer XXXX" \
    -H "Content-Type: application/json" \
    -d $'{
      "data": {
        "type": "order"
        "shipping-provider": "Ralphio 720",
        "consignment-id": "JRTH720666"
        }
      }
    }'
```

### E. Get the order with shipping details

To review shipping details, all you need to do is to send GET for the order you wish to view. The order will display the shipping details.

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X GET https://api.moltin.com/v2/orders/{{ORDER_ID}} \
     -H "Authorization: Bearer XXXX"
```
{% endtab %}

{% tab title="Response" %}
```bash
{
    "data": {
        "type": "order",
        "id": "1190674f-7b4a-4403-bdfd-aaf790cca43a",
        "status": "incomplete",
        "payment": "unpaid",
        "shipping": "unfulfilled",
        "customer": {
            "name": "Leslie Knope",
            "email": "leslie.knope@gov.com"
        },
        "shipping_address": {
            "first_name": "Leslie",
            "last_name": "Knope",
            "phone_number": "(555) 555-1234",
            "company_name": "Parks and Recreations Dept.",
            "line_1": "2nd Floor British India House",
            "line_2": "15 Carliol Square",
            "city": "Pawnee",
            "postcode": "NE1 6UF",
            "county": "Indiana",
            "country": "US",
            "instructions": ""
        },
        "billing_address": {
            "first_name": "Leslie",
            "last_name": "Knope",
            "company_name": "Parks and Recreations Dept.",
            "line_1": "2nd Floor British India House",
            "line_2": "15 Carliol Square",
            "city": "Pawnee",
            "postcode": "NE1 6UF",
            "county": "Indiana",
            "country": "US"
        },
        "links": {},
        "meta": {
            "display_price": {
                "with_tax": {
                    "amount": 20720,
                    "currency": "USD",
                    "formatted": "$207.20"
                },
                "without_tax": {
                    "amount": 27020,
                    "currency": "USD",
                    "formatted": "$207.20"
                },
                "tax": {
                    "amount": 0,
                    "currency": "USD",
                    "formatted": "$0.00"
                }
            },
            "timestamps": {
                "created_at": "2019-05-17T10:20:20Z",
                "updated_at": "2019-05-17T10:21:48Z"
            }
        },
        "relationships": {
            "shipping-provider": {
                "data": {
                    "type": "shipping-provider",
                    "id": "0b53b418-98d2-4a15-ab73-495f2b765584"
                }
            },
            "items": {
                "data": [
                    {
                        "type": "item",
                        "id": "15b5aa28-1350-45ec-8c1f-7f627e7aed85"
                    },
                    {
                        "type": "item",
                        "id": "02bb7bbe-c8a2-4b72-a99a-fc20c43d4c9d"
                    }
                ]
            }
        },
        "shipping-provider": "Ralphio 720",
        "consignment-id": "JRTH720666"
    }
}
```
{% endtab %}
{% endtabs %}

