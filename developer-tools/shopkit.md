# Shopkit

Moltin Shopkit is a minimalist component library that enables developers to easily embed commerce inside applications built with [React](https://reactjs.org).

Shopkit is built on top of the Moltin [JS SDK](https://github.com/moltin/js-sdk), removing all of the setup boilerplate to get up and running with React. There are several components available that return a function you can use to render various types of components in your application.

* [`<BuyButton />`](shopkit.md#button)
* [`<Product />`](shopkit.md#product)
* [`<CartButton />`](shopkit.md#cart)

## Installation

Using [Yarn](http://yarnpkg.com) or [npm](https://www.npmjs.com/get-npm), you'll want to install the Shopkit package inside your React application.

```bash
yarn add @moltin/react-shopkit # npm install --save @moltin/react-shopkit
```

Now inside your application, you need to wrap your root component with the `<Shopkit />` and set your `clientId`.

You can also set a custom `color` that is shared amongst all components for simple customization.

```javascript
import { Shopkit } from '@moltin/shopkit-react'
​
ReactDOM.render(
  <Shopkit clientId='j6hSilXRQfxKohTndUuVrErLcSJWP15P347L6Im0M4'>
    <App />
  </Shopkit>,
  document.querySelector('#root')
)
```

Next, you can use the components listed below inside your application and they will all share the same Moltin project.

## Components

The components below can be imported and configured for use inside your application.

### Buy Button

The quickest way to add Moltin to your website is to use the `<BuyButton />` component. Simply specify a Product ID and instantly have it added to the cart functionality.

#### Props

| **Prop** | **Default** | **Required** | **Description** |
| :--- | :--- | :--- | :--- |
| `id` | `undefined` | **Yes** | Your Moltin product ID |
| `cartId` | `undefined` | No | A custom cart ID \(otherwise created automatically\) |
| `children` | `undefined` | **Yes** | A custom render function for your product |

#### Example

```javascript
import React from 'react'
import { BuyButton } from '@moltin/react-shopkit'

export default () => (
  <BuyButton id="61abf56a-194e-4e13-a717-92d2f0c9d4df">
    {({ addToCart }) => <button onClick={addToCart}>Add to Cart</button>}
  </BuyButton>
)
```

### Product

The `<Product />` component is great place to start for rendering individual product items on a page. You may use this component on a single page to render anything associated with the product.

#### Props

| **Prop** | **Default** | **Required** | **Description** |
| :--- | :--- | :--- | :--- |
| `id` | `undefined` | **Yes** | Your Moltin product ID |
| `children` | `undefined` | **Yes** | A custom render function for your product |

#### Example

```javascript
import React from 'react'
import { Product, Button } from '@moltin/react-shopkit'
​
export default () => (
  <Product id="1f6eaa8a-80dc-4b1d-a66d-4cc3a1f7f3bb">
    {({ loading, error, data }) => {
      if (loading) return <p>Loading!</p>
      if (error) return <p>Error occured!</p>
​
      return (
        <div>
          <h2>{data.name}</h2>
          <Button id={data.id} />
        </div>
      )
    }}
  </Product>
)
```

### Product Grid

For pages you wish to display an array of products, you can use the `<ProductGrid />` component to render all `live` products.

{% hint style="warning" %}
You will need to import `Button`, if you wish to enable add to cart functionality.
{% endhint %}

#### Props

| **Prop** | **Default** | **Required** | **Description** |
| :--- | :--- | :--- | :--- |
| `maxProducts` | `12` | No | Set the max products per page |
| `children` | `undefined` | **Yes** | A custom render function for grid |

#### Example

```javascript
import React from 'react'
import { Product, Button } from '@moltin/react-shopkit'
​
export default () => (
  <Product id="1f6eaa8a-80dc-4b1d-a66d-4cc3a1f7f3bb">
    {({ loading, error, data }) => {
      if (loading) return <p>Loading!</p>
      if (error) return <p>Error occured!</p>
​
      return (
        <div>
          <h2>{data.name}</h2>
          <Button id={data.id} />
        </div>
      )
    }}
  </Product>
)
```

### Cart Button

Shopkit abstracts the cart functionality to the `<ShopkitProvider />` component that wraps your entire application. Using the React [Context API](https://reactjs.org/docs/context.html#reactcreatecontext) internally we are able to manage all cart state in one place and make it available to all other components.

#### Props

| **Prop** | **Default** | **Required** | **Description** |
| :--- | :--- | :--- | :--- |
|  |  |  |  |

#### Example

```javascript
import React from 'react'
import { CartButton } from '@moltin/react-shopkit'
​
export default () => (
  <CartButton>
    {({
      total,
      count,
      shown,
      onClick,
      items,
      updateCartQuantity,
      removeFromCart
    }) => (
      <ul>
        {items.map(item => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>
    )}
  </CartButton>
)
```

