# Getting Started

This section covers how to get started with making your first API request. You may use the API directly with a cURL request or make use of one of our supported SDKs.

### 1. Get your API keys

Every user is provided `client_id` and `client_secret` keys. These API keys are per user per project and are used to generate two types of [authentication tokens](https://docs.moltin.com/basics/authentication).

{% hint style="warning" %}
You should never share the client\_secret or use it client side.
{% endhint %}

API keys are available from the [Moltin Dashboard](https://dashboard.moltin.com).

### 2. Install a SDK \(recommended\)

Although you can interact with the API directly using a plain old http request, developers often rely on one of our SDKs. Currently we actively maintain the JavaScript and Swift SDKs, and with the support of our open source community, host a variety of other SDKs on our GitHub.

Following the steps below, you can quickly get up and running with your API keys.

{% tabs %}
{% tab title="JavaScript SDK" %}
If you're using [Yarn](http://yarnpkg.com/) or [npm](https://www.npmjs.com/) to manage dependencies, inside the same directory as `package.json` you will want to run:

```bash
npm install --save @moltin/sdk
```

If you are using [webpack](https://webpack.js.org/) you will need to add the following to your projects configuration file.

```javascript
node: {
  fs: 'empty'
}
```
{% endtab %}

{% tab title="Swift SDK" %}

{% endtab %}
{% endtabs %}

### 3. Create a client \(recommended\)

{% tabs %}
{% tab title="JavaScript in the Browser" %}
You can use the UMD build directly from [unpkg](https://unpkg.com/) inside your HTML. This is great for CodePen and small applications.

{% hint style="info" %}
We don't recommend you authenticate with client credentials using this method.
{% endhint %}

Inside a `<script />` tag you can instantiate the Moltin gateway using the global `moltin`.

```markup
<script src="https://unpkg.com/@moltin/sdk"></script>

<script>
  const Moltin = moltin.gateway({
    client_id: 'XXX'
  })
</script>
```
{% endtab %}

{% tab title="JavaScript SDK" %}
With the SDK installed, you'll next want to import and instantiate the Moltin gateway.

```javascript
import { gateway as MoltinGateway } from '@moltin/sdk'
// const MoltinGateway = require('@moltin/sdk').gateway

const Moltin = MoltinGateway({
  client_id: 'XXXX'
})
```
{% endtab %}

{% tab title="Swift SDK" %}

{% endtab %}
{% endtabs %}

### 3. Make your first API request

You'll need to authenticate to make your first API request and every request must contain your `Bearer` token inside the `header`. If you're using an SDK, these silently handle authentication so you don't need to call the authenticate method directly.

Below are some example API requests you can make. **Try them out!**

{% tabs %}
{% tab title="cURL" %}
#### Authenticate

```bash
curl -X "POST" "https://api.moltin.com/oauth/access_token" \
     -d "client_id=XXXX" \
     -d "client_secret=XXXX" \
     -d "grant_type=client_credentials"
```

#### Create a Product

```bash
curl -X POST https://api.moltin.com/v2/products \
     -H "Authorization: Bearer XXXX" \
     -d $'{
        "data": {
            "type": "product",
            "name": "Deck Shoe",
            "slug": "deck-shoe",
            "sku": "deck-shoe-001",
            "description": "Modern boat shoes were invented in 1935 by American Paul A. Sperry",
            "manage_stock": true,
            "price": [
            {
                "amount": 5891,
                "currency": "USD",
                "includes_tax": true
            }
            ],
            "status": "live",
            "commodity_type": "physical"
        }
    }'
```

#### Get all Products

```bash
curl -X GET https://api.moltin.com/v2/products \
     -H "Authorization: Bearer XXXX"
```
{% endtab %}

{% tab title="JavaScript in the Browser" %}

{% endtab %}

{% tab title="JavaScript SDK" %}
#### Get all Products

```javascript
const MoltinGateway = require('@moltin/sdk').gateway
​
const Moltin = MoltinGateway({
  client_id: 'X',
  client_secret: 'X'
})
​
Moltin.Products.All().then(products => {
  console.log(products)
})
```

#### 
{% endtab %}

{% tab title="Swift SDK" %}

{% endtab %}
{% endtabs %}

### Next steps



