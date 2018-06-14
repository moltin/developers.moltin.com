---
description: Flexible way of handling tax options with TaxJar
---

# TaxJar

You can visit the [repo](https://github.com/matthew1809/taxes) and enter your own credentials, or you can follow along here step by step.

With this tutorial you’ll achieve the following:

1. User hits checkout with their full cart.
2. User enters their address.
3. Tax is calculated using 1 & 2.
4. Tax is added as an extra item to the cart.
5. Checkout continues.

Here’s what we’re going to do in order to achieve it:

* First, we’ll briefly cover Flows, which allows us to add some extra information on our Moltin products needed later.
* Then, we’ll set up a server, and some HTML to receive the user input that we need.
* Thirdly, we’ll jump through the TaxJar API, the third party service that will return the tax price.
* Finally, we’ll introduce custom line items, a new feature for carts. We’ll use this feature to add the tax to the cart and give the user the updated total.

Before you go ahead with this tutorial, you’ll need to set up a couple of [Moltin products.](https://docs.moltin.com/#create-a-product)

### Flows {#flows}

You’ll see in the following sections that we need to send some extra data with our Moltin products, so we should set that up now using the [Flows](https://docs.moltin.com/#flows) feature.

If you’re not familiar with Flows, they’re containers for custom data models. They are composed of one or more fields, which can be of various data types.

Specifically, in this tutorial, Flows allows us to store a tax category value for each product, which affects how it’s taxed.

First, you’ll need to create a `products` flow. It’s not yet in the [JS SDK](https://github.com/moltin/js-sdk/tree/master), so we’ll do this from command line, which will look like this:

\(FYI - you’ll need to insert your own Bearer token in the ‘Authorization’ header on line 2, [learn more here](https://docs.moltin.com/#authentication)\).  


```javascript
curl -X "POST" "https://api.moltin.com/v2/flows" \
     -H "Authorization: XXXXXXX" \
     -H "Content-Type: application/json" \
     -d $'{
  "data": {
    "slug": "products",
    "enabled": true,
    "type": "flow",
    "name": "products",
    "description": "tax"
  }
}'
```

The response from the call above will include an ‘id’, take note of that now.

Then, create a `tax_category` field, like so, but with your noted flow ID on line 14, and your Bearer token on line 2:  


```javascript
curl -X "POST" "https://api.moltin.com/v2/fields" \
     -H "Authorization: XXXXXXX" \
     -H "Content-Type: application/json" \
     -d $'{
  "data": {
    "slug": "taxjar_category",
    "required": false,
    "unique": false,
    "default": "yes",
    "relationships": {
      "flow": {
        "data": {
          "type": "flow",
          "id": "XXXXXXX"
        }
      }
    },
    "type": "field",
    "field_type": "string",
    "name": "taxjar_category",
    "description": "the tax category of a product"
  }
}'
```

Now, you’ll update your Moltin products to have a tax category. You’ll need to do a PUT request to the product ID, with the product ID on lines 1 & 7, and as always, Bearer token on line 2:

```javascript
curl -X "PUT" "https://api.moltin.com/v2/products/XXXXXX" \
     -H "Authorization: XXXXXX" \
     -H "Content-Type: application/json" \
     -d $'{
  "data": {
    "type": "product",
    "id": "XXXXXX",
    "taxjar_category": "20010"
  }
}'
```

Lastly, just to complete the setup for later, you’ll add one of those products to a cart called `custom` using the following command line call, where `id` is your product ID and your Bearer token is on line 2:

```javascript
curl -X "POST" "https://api.moltin.com/v2/carts/custom/items" \
     -H "Authorization: XXXXXX" \
     -H "Content-Type: application/json" \
     -d $'{
  "data": {
    "quantity": 1,
    "id": "XXXXXXX",
    "type": "cart_item",
  }
}'
```

Alright, so now we have our Flow, field, updated products, and cart. We’re ready to move onto building our script!

### Server {#server}

Before we begin, create an empty file called `app.js` and open it.

We’ll need to collect user input and show the user some updated information. To do this, we’ll start a HTTP server which can display and subsequently process forms via the Formidable module.

The first thing we need to do at the top of our app.js file is require this module, and a couple of others too, like HTTP.

```javascript
var http = require('http');
var fs = require('fs');
var formidable = require("formidable");
var util = require('util');
```

Now, we can define our form handling function, for when we add HTML:  


```javascript
function displayForm(res) {
  fs.readFile('form.html', function (err, data) {
    res.writeHead(200, {
      'Content-Type': 'text/html',
      'Content-Length': data.length
    })
    res.write(data)
    res.end()
  })
}
```

We’re saying the HTML will live in a file called `form.html` - go create that empty file now so we can fill it later.

Great! Let’s define the server and tell it what to do:

```text
var server = http.createServer(function (req, res) {
  if (req.method.toLowerCase() == 'get') {
    displayForm(res)
  } else if (req.method.toLowerCase() == 'post') {
    processFormFieldsIndividual(req, res);
  }
})
```

In this case, if it’s a `GET` request, show the form. If it’s a `POST`, call a function to process the form fields.

In the previous code block, we’ve said this will live in something called `processFormFieldsIndividual`, so we’d better define it! We’re going to do this in such a way that leaves it open to processing individual fields as they are filled, but for this example, we’ll just request the data once the form is entirely submitted.

```javascript
function processFormFieldsIndividual(req, res) {
  var fields = []
  var form = new formidable.IncomingForm()
  form.on('field', function (field, value) {
    fields[field] = value
  })
  form.on('end', function () {
    res.writeHead(200, {
      'content-type': 'text/plain'
    })
    res.write('Thanks, We're calculating your tax now! \n\n')
    res.end(
      util.inspect({
        fields: fields
      })
    )
  })
  form.parse(req)
}
```

One last step, instruct the server to listen on a localhost port. Add these two lines to the end of the file:

```text
server.listen(1185)
console.log("server listening on 1185")
```

OK, all the pieces of our server are in place. We can now `npm install` from Terminal.

Hold up though, if we then run the file using `node app.js` nothing happens!

It’s because we’ve yet to add our HTML file, so let’s move onto that.

### HTML form {#html-form}

You’ll see in the next section that we need to store the user’s address, and we’ll use a plain form for that. Here’s the HTML code you can paste into `form.html`:

```markup
<!doctype html>
<html lang="en">
  <head></head>
  <body>
  <form action="/my-handling-form-page" method="post" enctype="multipart/form-data">
    <label for="postcode">Postcode:</label>
    <input type="text" id="postcode" name="postcode">
    <label for="county">County:</label>
    <input type="text" id="county" name="county">
    <label for="country">Country:</label>
    <input type="text" id="country" name="country">
    <input type="submit" value="Submit">
  </form>
  </body>
</html>
```

At this stage, if we run our app.js file, our server should start and display the form.

Right, we’re set up to get user input, and process it. Next, we need to pass that user input to our Tax service, along with a couple of other things.

### TaxJar {#taxjar}

TaxJar requires you to send multiple pieces of information so it can calculate the tax correctly. We’ll step through them one by one and populate a variable called `taxObject` which we’ll initialize as

```javascript
var taxObject = {
  from_country: 'US',
  from_zip: '92093',
  from_state: 'CA',
  from_city: 'La Jolla',
  from_street: '9500 Gilman Drive',
  to_country: '',
  to_zip: '',
  to_state: '',
  shipping: 1.5,
  amount: '',
  nexus_addresses: [
    {
      id: 'Main Location',
      country: 'US',
      zip: '92093',
      state: 'CA',
      city: 'La Jolla',
      street: '9500 Gilman Drive'
    }
  ],  
};
```

We’re hard coding in the from\_address, nexus\_addresses, and shipping for now.

**To address:** The form allowed us to get this information, and we now save the completed fields into the TaxJar To address. First, initialize an empty string variable at the top of the file called `data`.

Now, back in the `processFormFieldsIndividual` function, add these lines below line 14:

```javascript
data = fields
console.log("We have the address" + '\n')
taxObject.to_country = data.country
taxObject.to_zip = data.postcode
taxObject.to_state = data.county
```

Awesome, we’ve set up the To address to populate from the HTML form right into the taxObject!

**Cart total:** You can see an empty field in the taxObject called `amount: '',`.

Let’s now get the cart total by defining a new function and having it populate the `amount`variable:

```javascript
var cart = {
  price: function() {
    Moltin.Cart.Get('custom')
    .then((cart) => {
      total = cart.data.meta.display_price.without_tax.amount / 100
      taxObject.amount = total
      console.log('The untaxed total of the cart is:', cart.data.meta.display_price.without_tax.amount / 100)
      console.log('\n')
      tax_line_items.items(taxObject)
    })
    .catch((err) => {
      console.log(err)
    })
  }
}
```

In order for this to work, we’ll also need to require Moltin at the top of the file using these lines:

```text
const moltin = require('moltin')

const Moltin = moltin.gateway({
  client_id: 'YOUR_CLIENT_ID_GOES_HERE',
  secretKey: 'YOUR_SECRET_KEY_GOES_HERE'
})
```

It’s very important to note that we’re using a cart that we made earlier, so to speak. You’ll see it defined in `Moltin.Cart.Get` as `'custom'`.

If no cart ID is passed to the function, it will rely on the most recent cart used by you in the SDK, which should be in local storage.

**Total, quantity and tax category of each product:** To get the line items for taxObject, we’ll loop through each cart item, create an object with the details, and push that object to the taxObject.

But first, let’s make sure the taxObject is set up to receive it by adding a new empty array like so: `line_items: [],`

OK, we’re ready to define the looping function:

```javascript
var tax_line_items = {
  items: function () {
    Moltin.Cart.Items('custom')
      .then((items) => {
        items.data.forEach(function(item) {
          var obj = {
            quantity: 0,
            unit_price: 0,
            product_tax_code: 0
          }
          obj.quantity = item.quantity
          obj.unit_price = item.meta.display_price.without_tax.value.amount / 100
          obj.product_tax_code: item.taxjar_category
          taxObject.line_items.push(obj)
        })
      })
      .then((res) => {
        tj.calculate(taxObject)
      })
      .catch((err) => {
        console.log(err)
      })
    .catch((err) => {
      console.log(err)
    })
  }
}
```

Then add this line into the var tax\_line\_items code, after line 20: `taxjar.taxForOrder(taxObject);`. It calls our new function.

Lastly, because we’re calling a TaxJar helper library function, we’ll need to require the module with the following line at the top of our code: `var taxjar = require('taxjar')('YOUR_API_KEY_GOES_HERE');`

### Custom cart items {#custom-cart-items}

New in version 2 is the ability to add what’s called a ‘custom cart item’ to the cart. This item is defined in the cart, and lets you add in extras which might be built up dynamically along the customer’s journey \(check out the [docs](https://docs.moltin.com/#add-a-custom-item-to-a-cart) for more info on this\).

The returned object for tax is what we want to add to the cart as a custom item, and we can do so using a new function, like so:

```javascript
var AddCustomItem = {
  taxItem: function() {
    Moltin.Cart.AddCustomItem(customItem, 'custom')
    .then((cart) => {
      console.log('The new cart total is: ', cart.meta.display_price.without_tax.formatted)
    })
    .catch((err) => {
      console.log(err)
    })
  }
}
```

We can call the new function once we reach the end of the `var tj` code, by adding this after line 11:

```javascript
AddCustomItem.taxItem();
```

We made it! Let’s run the code from command line, and watch the magic happen &lt;3  


```javascript
node app.js
```

