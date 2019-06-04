---
description: >-
  Extend a customer profile to add more customer-specific information that your
  business needs.
---

# Extend the customer resource

### Prerequisites

* Moltin account \(you'll need the Client ID and Client Secret of your store\).
* Basic understanding of object-oriented programming or JavaScript.

### Summary of steps required

* Create a Flow for the endpoint you want to extend: `customers`
* Create Fields to add custom data.
* Create a `customer` that will include your custom data.

Fetching the extended `customer` object will show all custom data added to that customer.

### Step-by-step walkthrough

The steps below will show you how to extend an existing resource, `customers`, with a core Flow so it can have all of the JSON you want and need. For the purpose of this guide, we're going to create an entry field for customers to provide their delivery details, and then we're going to display it as part of the checkout process.

### 1. Get your access token

You will need to get a `client_credentials` access token to follow along making the API requests outlined below.

```bash
curl -X POST https://api.moltin.com/oauth/access_token \
     -d "client_id=XXXX" \
     -d "client_secret=XXXX" \
     -d "grant_type=client_credentials"
```

### 2. Create a core Flow

Next, we will create a new Flow, `customers`. Note that the slug must match the plural name of the resource you're extending.

{% hint style="info" %}
If you already have a Flow for `customers`, skip to Create a Flow Field. You'll need the ID of the customers Flow to continue.
{% endhint %}

```bash
curl -X POST https://api.moltin.com/v2/flows \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
         "data": {
           "type": "flow",
           "name": "Customers",
           "slug": "customers",
           "description": "Extends the default customer object",
           "enabled": true
          }
      }'
```

Take note of the Flow's ID that is returned. You'll need this to create a Flow field, as described below.

### 3. Create a Field

Let's create the **Delivery Details** field now. The Field will be returned when you call the `customer` object. Replace `FLOW_ID` with the ID that was generated for the `customers` Flow in step 2. In this scenario, we have set the `unique` field to `false`, which means that entries can repeat themselves. This way we don't constrain customers to write an original description for their delivery details. The field is also not required, making an entry optional for customers to fill in. 

```bash
curl -X POST https://api.moltin.com/v2/fields \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
          "data": {
            "type": "field",
            "name": "Delivery Details",
            "slug": "delivery_details",
            "field_type": "string",
            "description": "customers delivery details",
            "required": false,
            "unique": false,
            "default": 0,
            "enabled": true,
            "order": 1,
            "relationships": {
              "flow": {
                "data": {
                  "type": "flow",
                  "id": "{{FLOW_ID}}"
               }
             }
           }
         }
       }'
```

### 4. Create a customer with your custom field

Now, we need to start adding data to the customer profile, by calling the customers endpoint and adding the new key for the custom field in the data object with the value for the delivery details. Take a note of the ID returned for your customer.

```bash
curl -X POST https://api.moltin.com/v2/customers \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "customer",
          "name": "Ron Swanson",
          "email": "ron.swanson@gmail.com",
          "delivery_details": "Do NOT leave in a government building"
        }
      }'
```

### 5. Get the customer

Now, we've extended the customer profile and created our first customer with the custom field, we can now make a call to get that customer with the delivery details. 

{% tabs %}
{% tab title="cURL Request" %}
```bash
curl -X GET https://api.moltin.com/v2/customers/{{CUSTOMER_ID}} \
     -H "Authorization: XXXX" 
     -H "Content-Type: application/json"
```
{% endtab %}

{% tab title="Response" %}
```bash
{  
   "data":{  
      "type": "customer",
      "id": "{{CUSTOMER_ID}}",
      "name": "Ron Swanson",
      "email": "ron.swanson@gmail.com",
      "password": false,
      "delivery_details": "Do NOT leave in a government building"
   }
}
```
{% endtab %}
{% endtabs %}

### Update the customer

To update the delivery details all you have to do is to pass the field you want to update to the `customer` endpoint.

{% tabs %}
{% tab title="cURL Request" %}
```bash
curl -X PUT https://api.moltin.com/v2/customers/{{CUSTOMER_ID}} \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "customer",
          "id": "{{CUSTOMER_ID}}",
          "delivery_details": "Leave in Leslie Knope’s office"
        }
      }'
```
{% endtab %}

{% tab title="Response" %}
```bash
{  
   "data":{  
      "type": "customer",
      "id": "{{CUSTOMER_ID}}",
      "name": "Ron Swanson",
      "email": "ron.swanson@gmail.com",
      "password": false,
      "delivery_details": "Leave in Leslie Knope’s office?"
   }
}
```
{% endtab %}
{% endtabs %}

