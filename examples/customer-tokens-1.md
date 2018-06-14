---
description: Additional functionality with customer tokens
---

# Customer Tokens

#### When using the `implicit` grant type \(usually frontend SDK’s like Javascript\), you don’t get full read/write access to the API.

There are some circumstances that would merit accessing that privileged data.

For those cases \(if your user has a password\), you can log them in and get a token which can be used in subsequent calls.

This topic covers the following processes:

* Create a Password for a customer or updating an existing customer.
* Generating a token for that customer.
* Making calls to endpoints for that customer’s information.

### Create a password for a customer {#create-a-password-for-a-customer}

Once your customer has a password, you can use a combination of their email and password to generate a token that will grant you access to specific endpoints scoped to that customer.

#### Creating a new customer {#creating-a-new-customer}

If you are creating a new customer on the API you can submit a password along with that customer’s details:

```javascript
curl -X POST https://api.moltin.com/v2/customers
    -H "Authorization: Bearer XXXX" \
    -d $'{
     "data": {
     "type": "customer",
     "name": "Ron Swanson",
     "email": "ron@swanson.com",
     "password": "mysecretpassword",
     }
}'
```

```javascript
Moltin.Customers.Create({
  "type": "customer",
  "name": "Ron Swanson",
  "email": "ron@swanson.com",
  "password": "mysecretpassword"
})
```

Once you create a password for a customer we cannot retrieve that password again, but any customers with passwords will show a `true` value in the password field:

```javascript
{
  "data": {
    "id": "c8c1c511-beef-4812-9b7a-9f92c587217c",
    "type": "customer",
    "name": "Ron Swanson",
    "email": "ron@swanson.com",
    "password": true
  }
}
```

#### Updating an existing customer {#updating-an-existing-customer}

If you have an existing customer who does not have a password, you can update that individual like so:

```javascript
curl -X PUT https://api.moltin.com/v2/customers/{CUSTOMER_ID}
    -H "Authorization: Bearer XXXX" \
    -d $'{
     "data": {
           "password": "mynewpassword"
     }
}'
```

```javascript
Moltin.Customers.Update({CUSTOMER_ID}), {
  "password": "mynewpassword"
})
```

And you will receive the standard customer object response:

```javascript
{
  "data": {
    "id": "c8c1c511-beef-4812-9b7a-9f92c587217c",
    "type": "customer",
    "name": "Ron Swanson",
    "email": "ron@swanson.com",
    "password": true
  }
}
```

### Generating a token for your customer {#generating-a-token-for-your-customer}

Now your customer has an email and password you can use that to generate a token which will grant `implicit` access to the following resources, scoped to the specific customer:

* Customers
* Orders

#### Generating a token {#generating-a-token}

```javascript
curl -X POST https://api.moltin.com/v2/customers/tokens
    -H "Authorization: Bearer XXXX" \
    -d $'{
    "data":{
        "type": "token",
        "email": "ron@swanson.com",
        "password": "mysecretpassword"
    }
}'
```

```javascript
Moltin.Customers.Token("ron@swanson.com", "mysecretpassword")
```

A successful request will respond with:  


```javascript
{
  "data": {
    "type": "token",
    "id": "36f05940-0d38-411a-8909-3aea58bc1f09",
    "customer_id": "78cc0486-bbdf-491b-a0a2-722383b6288b",
    "token": "eyJhbGciOiAiSFMyNTYiLCAidHlwIjogIkpXVCJ9.eyJzdWIiOiI3OWNjMDQ4Ni1iYmRmLTQ5MWItYTBhMi03MjIzODNiNjI4OGIiLCJuYW1lIjoiUm9uIFN3YW5zb24iLCJleHAiOjE1MTA2ODQ.ea948e346d0683803aa4a2c09441bcbf7c79b",
    "expires": 1510684200
  }
}
```

In the body, you can see `customer_id` which is the customer we’re scoped to, and a `token`. The token being the key piece of information you need to access further resources for this customer.

### Using a customer token {#using-a-customer-token}

Using the `token` from a successful token generation request, you can now make requests to certain endpoints to return data scoped to that customer.

#### Using customer tokens with customers {#using-customer-tokens-with-customers}

You can pass your customer token to the customers endpoint to return the customer information scoped to that customer:

```javascript
curl -X "GET" "https://api.moltin.com/v2/customers" \
  -H "Authorization: Bearer XXXX" \
  -H "X-Moltin-Customer-Token: eyJhbGciOi.AiSFMyNTYiLCAidHlwIjogIkpXVCJ9.eyJzdWIiOiI3OWN"
```

#### Using customer tokens with orders {#using-customer-tokens-with-orders}

You can pass your customer token to the orders endpoint to return orders scoped to that customer:

```javascript
curl -X "GET" "https://api.moltin.com/v2/orders" \
  -H "Authorization: Bearer XXXX" \
  -H "X-Moltin-Customer-Token: eyJhbGciOi.AiSFMyNTYiLCAidHlwIjogIkpXVCJ9.eyJzdWIiOiI3OWN"
```

```javascript
Moltin.Orders.All("eyJhbGciOi.AiSFMyNTYiLCAidHlwIjogIkpXVCJ9.eyJzdWIiOiI3OWN")
```

