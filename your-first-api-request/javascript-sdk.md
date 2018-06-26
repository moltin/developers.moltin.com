# JavaScript SDK

One of the easiest ways to get going inside your existing JavaScript project is to use the moltin [JS SDK](https://github.com/moltin/js-sdk). Let's talk through through setup and making your first API call.

## Create a moltin project

[Sign up](https://dashboard.moltin.com/signup) or [login](https://dashboard.moltin.com/login) to the moltin dashboard to obtain your API keys. If you already have an existing moltin store, you can create additional stores inside the [accounts dashboard](https://accounts.moltin.com/).

## Add the SDK as a npm dependency

Now let's go ahead and install the moltin JavaScript SDK from npm. Inside your project root, run the following command:

```bash
npm install --save @moltin/sdk
```

If you are using [webpack](https://webpack.js.org/) you will need to add the following to your projects configuration file.

```javascript
node: {
  fs: 'empty'
}
```

### Add the SDK via unpkg

Alternatively you may wish to use the UMD build directly from [unpkg](https://unpkg.com/) inside your HTML. This is great for Codepen and small applications.

```markup
<script src="https://unpkg.com/@moltin/sdk"></script>

<script>
  const Moltin = moltin.gateway({
    client_id: 'XXX'
  });
</script>
```

## Create a moltin client

Once the dependency is installed, you can import and instantiate the moltin `gateway`.

Wherever you wish to use the moltin API, you will want to import the SDK like below. You will also need your `client_id`from your moltin dashboard.

```javascript
import { gateway as MoltinGateway } from '@moltin/sdk'
// const MoltinGateway = require('@moltin/sdk').gateway

const moltin = MoltinGateway({
  client_id: 'XXXX'
})
```

## Make your first request

Inside the same file you configured the `MoltinGateway`, add the following code:

```javascript
const products = Moltin.Products.All().then(products => {
  console.log(products)
})
```

If you run your JavaScript or Node.js project, you should see in the console the products response from moltin. 🎉

#### Next steps

Now that you have the SDK installed within your project, it's time to go further. You can follow along with our basic checkout flow that uses the JavaScript SDK or you could dive into the SDK README.

