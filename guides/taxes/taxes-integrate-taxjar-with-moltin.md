# Integrate TaxJar with Moltin

Add tax to items in your cart and calculate taxes using   TaxJar that provides a sales tax API for developers to get calculations for products.  

This guide will show you how to calculate sales tax via TaxJar and integrate it with your Moltin cart.   For more information on the tax object, see the [API Reference](https://docs.moltin.com/carts-and-checkout/carts/cart-items/tax-items#the-tax-item-object).

### Prerequisites

* [Moltin account](https://accounts.moltin.com/)
* [TaxJar account](https://app.taxjar.com/)
* Products configured through [Moltin dashboard](https://dashboard.moltin.com/) or through the API with the `includes_tax` set to true.

### Moltin tax integration key points:

* **Tax calculation logic.**  The `includes_tax` field on the product can be set to true or false:
  * `true` Moltin will perform a backward calculation of taxes meaning the original product price will not be affected during checkout.
  * `false` Moltin will calculate tax and add it to the original product price.
* **Promotions and Taxes.** How taxes are calculated depends on the [calculation method](../../calculation-methods.md) and may be added before or after promotional savings have been applied.
* **Cart-level promotions.** The way promotional savings are applied to carts depends on the [calculation method](../../calculation-methods.md). They may either be applied as negative cart items or equally distributed across all items in the cart. If there is a remainder after distributing promotional savings across cart items, the promo saving will be rounded up, so that there is no remainder left.

### Tax scenario overview

The guide uses clothing as an example, as the tax rules differ based on the state, city and item \(pretax\) cost, so it would allow us to demonstrate different use cases.

If you are selling in New York City, then the following tax rules apply:  

* No sales tax on an item of clothing or footwear that costs less than $110.
* An item of clothing or footwear that costs more than $110 is subject to the full 8.875% tax rate.
* Sales tax is calculated per item, so even if you buy two or more items that are worth more than $110 in total, you only pay tax on the items that individually cost more than $110.

To illustrate the rules above and show how taxes change, we will create three products:

1. Product that costs exactly $110.00.
2. Product that costs $110.01.
3. Product that that costs $200. 

## Moltin API and TaxJar integration step-by-step walkthrough

Before integrating TaxJar API, familiarize yourself with core sales tax concepts you'll need for integration.

| Tax concept | TaxJar API endpoint | Moltin API integration |
| :--- | :--- | :--- |
| Tax codes | [`categories`](https://developers.taxjar.com/api/reference/#categories) | Product custom data \([`flows`](https://docs.moltin.com/advanced/custom-data)\) to store TaxJar category code. |
| Nexus | [`nexus`](https://developers.taxjar.com/api/reference/#nexus) | Use the `regions` object to pass a list of [nexus](https://blog.taxjar.com/sales-tax-nexus-definition/) locations needed for tax calculation. |

Integrate TaxJar with Moltin in three simple steps:

1. Create Products Flow and add a tax\_code Field to it.
2. Create Cart Item Flow and add a tax\_code Field to it.
3. Apply appropriate tax codes to your Products, e.g. use 20010 for clothes.

### 1. **Create a Product data Flow**

As tax can differ based on the type of product you are selling, the categories from that endpoint need to be married with the product stored in Moltin.  

Create custom data for Products you've added for your project to store the TaxJar Category code, so that when adding Products to Moltin, you can be explicit about the tax that should be applied.  

{% tabs %}
{% tab title="cURL" %}
```bash
 curl -X POST "https://api.moltin.com/v2/flows" \      
-H "Authorization: XXXX" \      
-H "Content-Type: application/json" \      
    -d $'{         
        "data": {             
            "type": "flow",             
            "name": "Products",             
            "slug": "products",             
            "description": "Extends the default product object",             
            "enabled": true         
        }      
    }' 
```
{% endtab %}

{% tab title="Response" %}
```bash
{
    "data": {
        "id": "87987858-6e04-4e62-a295-7ca07eecd054",
        "type": "flow",
        "name": "Products",
        "slug": "products",
        "description": "Extends the default product object",
        "enabled": true,
        "links": {
            "self": "https://api.moltin.com/v2/flows/87987858-6e04-4e62-a295-7ca07eecd054"
        },
        "relationships": {},
        "meta": {
            "timestamps": {
                "created_at": "2018-12-17T14:02:39.845Z",
                "updated_at": "2018-12-17T14:02:39.845Z"
            }
        }
    }
```
{% endtab %}
{% endtabs %}

### **1a. Add a tax code field**

Add the `tax_code` field. This would be the tax code for the Product as a TaxJar category, Make sure to substitute the PRODUCT\_FLOW\_ID with the response value you got from step 1. 

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X "POST" "https://api.moltin.com/v2/fields" \      
-H "Authorization: XXXX" \      
-H "Content-Type: application/json" \     
    -d $'{       
        "data": {         
            "type": "field",         
            "name": "Tax Code",         
            "slug": "tax_code",         
            "field_type": "string",         
            "validation_rules": [             
                {                 
                "type": "enum",                 
                "options": [ "10040", "19000", "19001", "19002", "19003", "19004", "19005", "19006", "19007", "19008", "19009", "20010", "20041","30070", "31000", "40010", "40020", "40030", "40050", "40060", "41000", "51010", "81100", "81110", "81120","81300","81310", "99999" ]             
                 }        
                 ],         
                 "description": "The tax code for this product as a TaxJar category",         
                 "required": true,         
                 "unique": false,         
                 "default": "",         
                 "enabled": true,         
                 "order": 1,         
                 "omit_null": false,         
                 "relationships": {             
                     "flow": {                 
                     "data": {                     
                         "type": "flow",                     
                         "id": "{PRODUCT_FLOW_ID}"                 
                     }             
                 }         
             }       
         }     
     }'
```
{% endtab %}

{% tab title="Response" %}
```bash
{
    "data": {
        "id": "0baa7e6b-ef1b-49a1-bb45-2086bf82e22c",
        "type": "field",
        "field_type": "string",
        "slug": "tax-code",
        "name": "Tax Code",
        "description": "The tax code for this product as a TaxJar category",
        "required": true,
        "unique": false,
        "default": null,
        "enabled": true,
        "order": 1,
        "omit_null": false,
        "validation_rules": [
            {
                "type": "enum",
                "options": [
                    "10040",
                    "19000",
                    "19001",
                    "19002",
                    "19003",
                    "19004",
                    "19005",
                    "19006",
                    "19007",
                    "19008",
                    "19009",
                    "20010",
                    "20041",
                    "30070",
                    "31000",
                    "40010",
                    "40020",
                    "40030",
                    "40050",
                    "40060",
                    "41000",
                    "51010",
                    "81100",
                    "81110",
                    "81120",
                    "81300",
                    "81310",
                    "99999"
                ]
            }
        ],
        "links": {
            "self": "https://api.moltin.com/v2/flows/9d69c6cf-aaee-4dc6-8908-d2bd053446a2/fields/0baa7e6b-ef1b-49a1-bb45-2086bf82e22c"
        },
        "relationships": {
            "flow": {
                "data": {
                    "id": "9d69c6cf-aaee-4dc6-8908-d2bd053446a2",
                    "type": "flow"
                }
            }
        },
        "meta": {
            "timestamps": {
                "created_at": "2018-12-17T14:09:48.195Z",
                "updated_at": "2018-12-17T14:09:48.195Z"
            }
        }
    }
}
```
{% endtab %}
{% endtabs %}

### **2. Create a cart item data flow**

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST "https://api.moltin.com/v2/flows" \      
-H "Authorization: XXXX" \      
-H "Content-Type: application/json" \      
    -d $'{         
        "data": {             
            "type": "flow",             
            "name": "Cart Items",             
            "slug": "cart_items",             
            "description": "Extends the default cart item object",             
            "enabled": true         
        }      
    }' 
```
{% endtab %}

{% tab title="Response" %}
```bash
{
    "data": {
        "id": "14022133-913e-4256-aa98-70db4533ca25",
        "type": "flow",
        "name": "Cart Items",
        "slug": "cart-items",
        "description": "Extends the default cart item object",
        "enabled": true,
        "links": {
            "self": "https://api.moltin.com/v2/flows/14022133-913e-4256-aa98-70db4533ca25"
        },
        "relationships": {},
        "meta": {
            "timestamps": {
                "created_at": "2018-12-17T14:11:49.314Z",
                "updated_at": "2018-12-17T14:11:49.314Z"
            }
        }
    }
}
```
{% endtab %}
{% endtabs %}

### 2a. Add a tax code field

Repeat the step from 1a. This would be the tax code for the cart item as a TaxJar category. Make sure to substitute the PRODUCT\_FLOW\_ID with the response value you got from step 1. 

### 3. Create Products and allocate tax codes

In case of our Products \(which come under tax category of clothes\) the `tax_code` value is `20010`.

{% tabs %}
{% tab title="Product 1" %}
Product that costs exactly $110.00.

```bash
 curl -X POST https://api.moltin.com/v2/products \      
-H "Authorization: Bearer XXXX" \      
-H "Content-Type: application/json" \      
    -d $'{         
        "data": {             
            "type": "product",             
            "name": "Hat",             
            "slug": "hat",             
            "sku": "hats.1",             
            "description": "A hat",             
            "manage_stock": false,             
            "price": [             
            {                 
                "amount": 11000,                 
                "currency": "USD",                 
                "includes_tax": false             
            }             
            ],             
            "status": "live",             
            "commodity_type": "physical",             
            "tax_code": "20010"         
        }     
    }' 
```
{% endtab %}

{% tab title="Product 2" %}
Product that costs $110.01.

```bash
 curl -X POST https://api.moltin.com/v2/products \      
-H "Authorization: Bearer XXXX" \      
-H "Content-Type: application/json" \      
    -d $'{         
        "data": {             
            "type": "product",             
            "name": "Sweater",             
            "slug": "sweater",             
            "sku": "sweaters.1",             
            "description": "A sweater",             
            "manage_stock": false,             
            "price": 
            [             
            {                 
                "amount": 11001,                 
                "currency": "USD",                 
                "includes_tax": false             
            }             
            ],             
            "status": "live",             
            "commodity_type": "physical",             
            "tax_code": "20010"         
        }     
    }' 
```
{% endtab %}

{% tab title="Product 3" %}
Product that that costs $200.

```bash
 curl -X POST https://api.moltin.com/v2/products \      
-H "Authorization: Bearer XXXX" \      
-H "Content-Type: application/json" \      
    -d $'{         
        "data": {             
            "type": "product",             
            "name": "Socks",             
            "slug": "socks",             
            "sku": "socks.1",             
            "description": "A pair of socks",             
            "manage_stock": false,             
            "price": 
            [             
            {                 
                "amount": 20000,                 
                "currency": "USD",                 
                "includes_tax": false             
            }             
            ],             
            "status": "live",             
            "commodity_type": "physical",             
            "tax_code": "20010"         
        }     
    }' 
```
{% endtab %}
{% endtabs %}

As soon as the Product converts into a cart item, the appropriate tax-code will then be applied to it.

### Run the demo site

 For demonstration site, visit our [GitHub repo](https://github.com/moltin/taxjar-example). The demo is a basic webpage built using React and bootstrap. To run it locally, clone the [GitHub repo](https://github.com/moltin/taxjar-example) and run:

```text
yarn install
 yarn dev 
```

This will start the server locally on [localhost:3000](http://localhost:3000), and you can see server side operations in your terminal window.     
****

  




###  

