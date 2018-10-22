---
description: >-
  A walkthrough how you could generate a list of your orders that includes
  items.
---

# Create a formatted order csv

## 1. Get your access token <a id="1-get-your-access-token"></a>

You will need to get a [`client_credentials`](https://docs.moltin.com/basics/authentication/client-credential-token) access token to follow along making the API requests outlined below.

```javascript
const MoltinGateway = require('@moltin/sdk').gateway
​
const Moltin = MoltinGateway({
  client_id: 'X',
  client_secret: 'X'
})
```

## 2. Fetch all of your order <a id="2-create-a-new-flow"></a>

 You can use filter to only grab the orders you want.  Example of that would be to filter based on time.  In the example we will be grabbing them all

```javascript
const MoltinGateway = require('@moltin/sdk').gateway
//This global orders array will be used through out the example code snippets.
let orders;
​
const Moltin = MoltinGateway({
  client_id: 'X',
  client_secret: 'X'
})
​
Moltin.Orders.All().then(orders => {
  this.orders = orders
})
```

## 3. Creating an array that has orders and items <a id="2-create-a-new-flow"></a>

 Using the above API response you will want to loop through the data and create an array that has all if the data you may need.

```javascript
exports.itemsLookup(orders.data[0], orders.included.items).then((order) => {
    console.log(order.relationships.items);
});
```

The itemLookup function is taking a order which includes an array of items.  The lookup loops through the orders and assigns the items to the id.

```javascript
exports.itemsLookup = function(order, items) {
    return new Promise(function(resolve, reject) {
      
      var itemsProcessed = 0;
      let itemsArray = [];
      //for each of the orders related items
      order.relationships.items.data.forEach(function(item) {
        // ID for the orders item 
        let id = item.id;
        // simple counter
        itemsProcessed++;
        // look up each item
        items.forEach(function(item) {
          if(item.id === id) {
            console.log('matching order item object found');
            itemsArray.push(item);
            console.log(itemsArray);
          }
        });

        // if there are not order items left to process
        if(itemsProcessed === order.relationships.items.data.length) {
          console.log('finished processing items');
          order.relationships.items = itemsArray;
          resolve(order);
        };
      })
  })
};
```

## 4. Adding array to CSV <a id="2-create-a-new-flow"></a>

```javascript
Moltin.Orders
.With('items')
.All()
.then((orders) => {
	exports.formatOrders(orders, orders.included.items)
    .then((formattedOrders) => {
      console.log(formattedOrders);
    })

})
.catch((e) => {
	console.log(e);
})
```



To see the full code

[https://gist.github.com/gje4/7d78c7cff7928ba9023bdef6d37c3016\#file-export-moltin-products](https://gist.github.com/gje4/7d78c7cff7928ba9023bdef6d37c3016#file-export-moltin-products)

