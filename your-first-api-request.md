# Your First API Request

This section covers how to get started with making your first API request. You may use the API directly with a cURL request or make use of one of our supported SDKs.

## 1. Install SDK \(recommended\)

Although you can interact with the API directly using a plain old http request, developers often rely on one of our SDKs.

Currently we actively maintain the JavaScript and Swift SDKs, and with the support of our open source community, host a variety of other SDKs on our GitHub.

Following the steps below, you can quickly get up and running with your API keys.

{% tabs %}
{% tab title="JavaScript SDK" %}
If you're using [Yarn](http://yarnpkg.com) or [npm](https://www.npmjs.com) to manage dependencies, inside the same directory as `package.json` you will want to run:

```bash
yarn add @moltin/sdk # npm install --save @moltin/sdk
```

If you are using [webpack](https://webpack.js.org), you will need to add the following to your projects configuration file.

```javascript
node: {
  fs: 'empty'
}
```
{% endtab %}

{% tab title="Swift SDK" %}
The Swift SDK is available to install via Cocoapods, Carthage and Swift Package Manager.

### CocoaPods

Add the following to your `Podfile`:

```swift
pod 'Moltin', '~> 3.0.11'
```

### Carthage

Add the following to your `Cartfile`:

```swift
github "Moltin/ios-sdk" ~> 3.0.11
```

### Swift Package Manager

Add the following to your dependencies value in `Package.swift`:

```swift
dependencies: [
  .package(url: "https://github.com/moltin/ios-sdk.git", from: "3.0.11")
]
```
{% endtab %}
{% endtabs %}

## 2. Create a client \(recommended\)

One of the biggest advantages of using an SDK is the built-in support for storing and renewing your authentication token.

{% tabs %}
{% tab title="JavaScript SDK" %}
With the SDK installed, you'll next want to import and instantiate the Moltin gateway.

```javascript
import { gateway as MoltinGateway } from '@moltin/sdk'
// const MoltinGateway = require('@moltin/sdk').gateway
​
const Moltin = MoltinGateway({
  client_id: 'XXXX'
})
```
{% endtab %}

{% tab title="JavaScript in the Browser" %}
You can use the UMD build directly from [unpkg](https://unpkg.com) inside your HTML. This is great for CodePen and small applications.

{% hint style="warning" %}
You should not use client\_credentials when authenticating this way.
{% endhint %}

Inside a `<script />` tag you can instantiate the Moltin gateway using the global `moltin`.

```markup
<script src="https://unpkg.com/@moltin/sdk"></script>
​
<script>
  const Moltin = moltin.gateway({
    client_id: 'XXX'
  })
</script>
```
{% endtab %}

{% tab title="Swift SDK" %}
```swift
let moltin = Moltin(withClientID: "<your client ID>")
```
{% endtab %}
{% endtabs %}

## 3. Make your first API request

You'll need to authenticate to make your first API request and every request must contain your `Bearer` token inside the `header`. If you're using an SDK, these silently handle authentication, so you don't need to call the authenticate method directly.

Below are some example API requests you can make. **Try them out!**

{% tabs %}
{% tab title="cURL" %}
With plain old HTTP request you can make a request to the API. First you will need to authenticate to get your access token.

### Authenticate

```bash
curl -X POST https://api.moltin.com/oauth/access_token \
     -d "client_id=XXXX" \
     -d "client_secret=XXXX" \
     -d "grant_type=client_credentials"
```

### Create a Product

Using the token returned from the response above, apply it below instead of `XXXX`.

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

### Get all Products

Make sure to apply your authentication token instead of `XXXX`.

```bash
curl -X GET https://api.moltin.com/v2/products \
     -H "Authorization: Bearer XXXX"
```
{% endtab %}

{% tab title="Node" %}
With [Node.js](https://nodejs.org/en) installed, create a file named `index.js` with the following:

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

Save the file and run `node index.js` to see `products` logged to the console.
{% endtab %}

{% tab title="Client-side" %}
Using a client-side library like [React](https://reactjs.org), [React Native](https://facebook.github.io/react-native), [Vue](https://vuejs.org) or [Angular](https://angular.io) simply add the JS SDK as a dependency to your project.

{% hint style="warning" %}
We don't recommend you authenticate with client credentials using this method.
{% endhint %}

If you head to the [Dashboard](https://dashboard.moltin.com), add a product with an image and associated category or brand, you'll be able to query the results in your client application using the code below.

### Get all Products

```javascript
import { gateway as MoltinGateway } from '@moltin/sdk'
​
const Moltin = MoltinGateway({
  client_id: 'XXXX'
})

Moltin.Products.All().then(products => {
  console.log(products)
})
```

### Get all Products with main image, brands and categories

```javascript
import { gateway as MoltinGateway } from '@moltin/sdk'
​
const Moltin = MoltinGateway({
  client_id: 'XXXX'
})

Moltin.Products.With(['main_image', 'brand', 'category']).All().then(products => {
  console.log(products)
})
```
{% endtab %}

{% tab title="Swift SDK" %}
After you have added the Swift SDK you are ready to start.

```swift
import moltin
```

If you head to the [Dashboard](https://dashboard.moltin.com), add a product with an image and associated category or brand, you'll be able to query the results in your client application using the code below.

### Get all Products

```swift
let moltin = Moltin(withClientID: "<your client ID>")

self.moltin.product.all { (result: Result<PaginatedResponse<[moltin.Product]>>) in
   switch result {
       case .success(let response):
            DispatchQueue.main.async {
                print("Products", response.data)
            }
        case .failure(let error):
            print("Products error", error)
        }
    }
}
```

### Get all Products with main image, brands and categories

```javascript
let moltin = Moltin(withClientID: "<your client ID>")

self.moltin.product.include([.mainImage, .brand, .category]).all { (result: Result<PaginatedResponse<[moltin.Product]>>) in
   switch result {
       case .success(let response):
            DispatchQueue.main.async {
                print("Products", response.data)
            }
        case .failure(let error):
            print("Products error", error)
        }
    }
}
```
{% endtab %}
{% endtabs %}



