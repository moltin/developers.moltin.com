# Shopkit React

Moltin Shopkit is a minimalist React component library that enables developers to easily embed commerce inside React powered applications.

## Installation

Using [npm](https://www.npmjs.com/get-npm) or [Yarn](http://yarnpkg.com/), you'll want to install the Shopkit package inside your React application.

```bash
npm install --save @moltin/shopkit-react
```

Next inside your application, you will need to wrap your root component with the `<ShopkitProvider />`. You may also specify a global `clientId`.

```javascript
import { Provider as ShopkitProvider } from '@moltin/shopkit-react'

ReactDOM.render(
  <ShopkitProvider clientId='j6hSilXRQfxKohTndUuVrErLcSJWP15P347L6Im0M4'}}>
    <App />
  </ShopkitProvider>,
  document.querySelector('#root')
)
```

Next you can use the components below inside your application and they will all share the same Moltin project. You can additionally specify a `color` that will be applied to buttons and borders.

## Components

The components below can be imported and configured for use inside your application.

### Card

The `<Card />` component is great for places you wish to embed single product views. The Card is also available without the description in portrait mode.

![Card with description](../.gitbook/assets/product-widget-with-description%20%281%29.jpg)

#### Usage

```javascript
import React from 'react'
import { Card } from '@moltin/shopkit-react'

export default () => <Card productId="X" showDescription />
```

#### Props

| **Prop** | **Default** | **Required** | **Description** |
| --- | --- | --- | --- | --- | --- | --- |
| `id` | `undefined` | **Yes** | Your Moltin product ID |
| `showDescription` | `false` | No | Provide this prop to render the description in landscape mode |
| `showSKU` | `false` | No | Display your product SKU |
| `showQty` | `true` | No | Display the quantity picker |
| `hideBtn` | `false` | No | Hide the add to cart button |
| `btnText` | Add to Cart | No | Provide custom button text |

### Button

The `<Button />` component is great for places where you wish to embed eCommerce functionality. With a single button you can provide "Add to Cart" functionality with a quantity picker.

![Button with quantity select](../.gitbook/assets/screen-shot-2018-06-18-at-13.27.10.png)

#### Usage

```javascript
import React from 'react'
import { Button } from '@moltin/shopkit-react'

export default () => <Button id="X" showQty />
```

#### Props

| **Prop** | **Default** | **Required** | **Description** |
| --- | --- | --- | --- |
| `id` | `undefined` | **Yes** | Your Moltin product ID |
| `showQty` | `true` | No | Do you want to show the quantity field? |
| `btnText` | Add to Cart | No | Provide custom button text |

### Cart

You can use the Shopkit `<Cart />` component to provide a cart + checkout flow inside a modal.

![](../.gitbook/assets/shopping-cart.png)

#### Usage

```javascript
import React from 'react'
import { Cart } from '@moltin/shopkit-react'

export default () => <Cart />
```

#### Props

| **Prop** | **Default** | **Required** | **Description** |
| --- | --- |
| `showCartTotal` | `false` | No | Show the cart total, instead of item count |

### Product Grid

You may display a grid of products using the `<ProductGrid />` component. By default it will return all your products, paginated by `maxProducts`. You can also filter these products by Brand, Category and Collection.

#### Usage

```javascript
import React from 'react'
import { ProductGrid } from '@moltin/shopkit-react'

export default () => <ProductGrid categoryId="X" />
```

#### Props

| **Prop** | **Default** | **Required** | **Description** |
| --- | --- | --- | --- | --- |
| `maxProducts` | `12` | No | Set the max products per page |
| `brandId` | `undefined` | No | Filter products by Brand |
| `categoryId` | `undefined` | No | Filter products by Category |
| `collectionId` | `undefined` | No | Filter products by Collection |



