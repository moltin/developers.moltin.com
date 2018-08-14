# Example Projects

Moltin maintains a [collection of examples](https://github.com/moltin-examples/react-microsite) that demonstrate how to use the API with a variety of server-side and client-side libraries. See each library below to view the details on how to use it with the Moltin API.

## React + Shopkit

The Moltin [react-microsite](https://github.com/moltin-examples/react-microsite) example is built with React and uses [Shopkit](developer-tools/shopkit.md) to display a product carousel, add to cart button, cart and checkout flow.

* [Demo](https://moltin-examples.github.io/react-microsite/#/)
* [Source Code](https://github.com/moltin-examples/react-microsite)

Using the [Now CLI](https://zeit.co/now) you can deploy directly from GitHub. You'll need your Moltin `client_id` to successfully deploy.

```bash
npm i -g now # unless installed already
now moltin-examples/react-microsite -e MOLTIN_CLIENT_ID=x
```

## Next.js

[Next.js](https://nextjs.org/) is a lightweight React framework for static and server-rendered applications. This example is built using the Moltin JS SDK and provides a simple checkout flow.

* [Demo](https://moltin-nextjs-demo-store.now.sh/)
* [Source Code](https://github.com/moltin-examples/nextjs-demo-store)

## Swift

* [Source Code](https://github.com/moltin-examples/swift-demo-app)

## Vue

The Vue example projects demonstrates how to connect the Moltin JS SDK to get all products, a single product and checkout. All of the code is available on GitHub.

* [Demo](https://moltin-examples.github.io/vue-demo-store)
* [Source Code](https://github.com/moltin-examples/vue-demo-store)

## Angular

This example Progressive Web App project uses the [Ionic framework](https://ionicframework.com/) and the Moltin SDK.

* [Demo](https://evening-peak-15305.herokuapp.com/#/home)
* [Source Code](https://github.com/moltin-examples/progressive-web-app)

## GraphQL

The server repository demonstrates how you could wrap Moltin requests with GraphQL and provide your UI a single endpoint to get and mutate data.

* [Source Code](https://github.com/moltin-examples/graphql-moltin-server)

