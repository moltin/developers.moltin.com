---
description: >-
  Integrate any third party via webhooks or set up automated emails based on
  events in Moltin
---

# Via Webhooks



1. [Set up a trigger \(webhook\) for invoice creation, based on new orders in our Moltin store.](https://moltin.com/blog/2017/06/automated-invoicing/#part-1)
2. [Create an express app to create an invoice \(action\) based on the trigger.](https://moltin.com/blog/2017/06/automated-invoicing/#part-2)
3. [Tie our trigger \(order created webhook\) to our action \(invoice creation\) so they can reach one another.](https://moltin.com/blog/2017/06/automated-invoicing/#part-3)

You can find the [working repo here](https://github.com/matthew1809/Invoicing-moltin).

This tutorial assumes you know how to create a Moltin order i.e. product to cart =&gt; cart =&gt; checkout =&gt; order. If you haven’t done that before, [check out the docs](https://docs.moltin.com/#checkout). Remember, carts persist, so once you have a cart created, you can generate an order based on the same cart as many times as you want!

### Part 1 {#part-1}

Alright, let’s jump into the [integrations service](https://docs.moltin.com/#integrations) and take a look.

If we look at the [model](https://docs.moltin.com/#integration) for an integration, we can see it has a number of different fields. There are two `integration_types` \(`webhook` or `email`\), and a configuration for each type \(we’re going to create a `webhook`\).

Each integration is set to observe \(listen for\) certain events. You pass these events into the `observes` field within an array.

Looking to the `configuration` for a webhook, you’ll see it needs a `URL` field which it will notify when the webhook is triggered. For now, let’s spin up a [RequestBin](https://requestb.in/) to use.

OK, here’s the cURL request to create our first integration. Note that it observes `order.created`, because we create invoices based on an order:

* If you’re not sure where to get a Bearer token, [read about authentication in the docs](https://docs.moltin.com/#authentication) first.

```javascript
## Create an Integration
curl -X "POST" "https://api.moltin.com/v2/integrations" \
     -H "Authorization: YOUR_BEARER_TOKEN_GOES_HERE" \
     -H "Content-Type: application/json; charset=utf-8" \
     -d $'{
  "data": {
    "observes": [
      "order.created"
    ],
    "configuration": {
      "url": "YOUR_REQUESTBIN_URL_GOES_HERE"
    },
    "type": "integration",
    "name": "invoicing",
    "integration_type": "webhook",
    "enabled": true
  }
}'
```

The response to this should be `201 Created`, and the body will contain an `ID` for the integration. Note this ID down now.

Time to test! Check out a cart with Moltin:

```javascript

# Checkout a cartcurl -X "POST" "https://api.moltin.com/v2/carts/YOUR_CART_ID/checkout" \     -H "Authorization: YOUR_BEARER_TOKEN_GOES_HERE" \     -H "Content-Type: application/json" \     -d $'{  "data": {    customer: {      "name": "John Doe",      "email": "john@doe.co"    },    billing_address: {      "first_name": "John"      "last_name": "Doe",      "line_1": "123 Sunny Street",      "line_2": "Sunnycreek",      "county": "California",      "postcode": "CA94040",      "country": "US"    },    shipping_address: {      "first_name": "Jon",      "last_name": "Doe",      "line_1": "123 Sunny Street",      "line_2": "Sunnycreek",      "county": "California",      "postcode": "CA94040",      "country": "US"    }  }}'
```

If we check our RequestBin URL with `/inspect` on the end, we should now see some data! It will include the order details, and the order item IDs but not details about the items themselves. We’ll have to make an extra Moltin call to grab those.

If you’re not seeing anything in the RequestBin at this stage, make sure that the integration has the right URL configuration, and make sure you haven’t missed the `enabled` boolean field, which will default to disabled if you’ve not set it to true.

### Part 2 {#part-2}

Jump over to your friendly neighborhood Terminal, and enter `mkdir invoicing && cd invoicing`followed by `touch invoice.js`. Now you can open this new repo containing the file, in whatever code editor you feel most comfortable with. I use Atom, my command will be `atom .`.

Include the dependencies we’re going to need, at the top of our file.

```javascript
var express = require('express')
var https = require("https")
var fs = require("fs")
var bodyParser = require('body-parser')
const moltin = require('@moltin/sdk')
const Moltin = moltin.gateway({
  client_id: "XXXX",
  client_secret: "YYYY",
})
```



Make sure to fill in your own Moltin client ID and secret where it say XXXX & YYYY on `const Moltin`!

Next, instantiate an express app and instruct it to parse the bodies of requests correctly using body-parser.

```javascript
var app = express()
app.use(bodyParser.json()); // for parsing application/json
```

Now, we’ll define the most important function. It takes in the details for the invoice, name of the invoice file, and makes the POST request to invoice-generator.com to generate that invoice. It then writes back to file.

```javascript
function generateInvoice(invoice, filename, success, error) {
    var postData = JSON.stringify(invoice)
    var options = {
        hostname  : "invoice-generator.com",
        port      : 443,
        path      : "/",
        method    : "POST",
        headers   : {
            "Content-Type": "application/json",
            "Content-Length": Buffer.byteLength(postData)
        }
    };

    var file = fs.createWriteStream(filename)

    var req = https.request(options, function(res) {
        res.on('data', function(chunk) {
            file.write(chunk)
        })
        .on('end', function() {
            file.end()

            if (typeof success === 'function') {
                success()
            }
        })
    })
    req.write(postData)
    req.end()

    if (typeof error === 'function') {
        req.on('error', error);
    }
}
```

This function needs the invoice details, so let’s add a variable where they can live and populate it with some sample data:

```javascript
var invoice = {
    logo: "http://invoiced.com/img/logo-invoice.png",
    from: "My Moltin Store",
    to: "Johnny Appleseed",
    currency: "usd",
    number: "INV-0001",
    payment_terms: "Auto-Billed - Do Not Pay",
    items: [],
    notes: "Thanks for being an awesome customer!",
    terms: "No need to submit payment. You will be auto-billed for this invoice."
}
```

In order to get order items, we will need to access our order ID, so create an empty variable for that too:

`var order_id = ''`

Now we can define the function that gets the order items, loops through each item, and pushes the information we need to our invoice object, within the items array. Finally, it triggers our invoice creation function as by this time, we have all the info we need:

```javascript
var get_order_items = function(order_id) {

  Moltin.Orders.Items(order_id)

  .then((items) => {
    var data = items.data
    data.forEach((item) => {
      invoice.items.push({
        name: item.name,
        quantity: item.quantity,
        unit_cost: item.unit_price.amount
      })
    })
  })

  .then(() => {
    generateInvoice(invoice, 'invoice.pdf', function() {
        console.log("Saved invoice to invoice.pdf")
    }, function(error) {
        console.error(error)
    })
  })

  .catch((error) => {
    console.log(error)
  })
}
```

With everything set up, we can now code up how our app will handle a POST request to its root. You can see that this is where we assign a value to our previously empty `order_id` variable so that our order items function can use it.

```javascript
app.post('/', function (req, res) {
  pbody = JSON.parse(req.body.resources)
    order_id = pbody.data.id
    console.log('the parsed order id is: ' + order_id)
    invoice.to = pbody.data.customer.name
    invoice.currency = pbody.data.meta.display_price.with_tax.currency
  return get_order_items(order_id)
})
```

Finally we can start the app:

```javascript
app.listen(3000, function () {
  console.log('Example app listening on port 3000!')
})
```

Run the app via Terminal using `nodemon invoice.js` and navigate to `localhost:3000` in your browser. We use nodemon to automatically restart our app when we change it. It can be installed via `npm install -g nodemon`.

### Part 3 {#part-3}



Quick recap. So far we can create invoices programmatically, as well as detect when a new Moltin order is created.

This step is all about wiring the previous two together so that the invoice creation is triggered by the webhook.

Firstly, time to make our express app reachable by the webhook. Let’s use one of my favourite tools, [Ngrok](https://ngrok.com/).

Once you’ve installed it, enter `ngrok http 3000` in a second terminal tab. It will expose our localhost app to the world! P.S. Make sure that in the first Terminal tab, you’ve still got the express app running.

Next, back to our integration, you’ll need to update the URL to the Ngrok one:

* You’ll need the [integration ID](https://moltin.com/blog/2017/06/automated-invoicing/#integration-id) from before

```javascript
## Update an Integration
curl -X "PUT" "https://api.moltin.com/v2/integrations/YOUR_INTEGRATION_ID_GOES_HERE" \     -H "Authorization: YOUR_BEARER_TOKEN_GOES_HERE" \     -H "Content-Type: application/json; charset=utf-8" \     -d $'{  "data": {    "type": "integration",    "id": "YOUR_INTEGRATION_ID_GOES_HERE",    "configuration": {      "url": "YOUR_NGROK_URL_GOES_HERE"    }  }}'
```

Time to check out again! Hopefully, you will see a new invoice file created, filled with data from your Moltin order.

Awesome sauce! Everything should now be hooked up so that whenever you get a new Moltin order, it will create a new PDF invoice for that order and save it to the same folder as your script.

