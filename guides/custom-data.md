# Custom Data

One of the great features of Moltin is the ability to extend existing core resources and the flexibility to create your own. With [Flows](https://docs.moltin.com/advanced/custom-data), you are able to create various types of fields, including strings, booleans, dates, integers and relationships with other custom flows.

The Moltin API aims to provide predictable, easy to use, RESTful endpoints that can power any ecommerce project. With Flows, you are able to apply Moltin to almost any use case.

## Extending resources

The most common usage of the Flows API is to extend resources like products, customers and orders. If you wish to store extra fields per product, such as `manufactured_date` for customers or a string field to store a `colour` hexadecimal for the product for use on the UI, you'll want to extend the `products` resource.

Click the links below to view examples of how to use Flows to extend resources.

{% page-ref page="product/related-products.md" %}

{% page-ref page="product/extending-product.md" %}

{% page-ref page="serverless/short-order-id.md" %}

## New resources

With the power of Flows, you are able to use Moltin to power product reviews, wishlists, blog articles, staff profile pages, and many more.

Click the links below to view examples of how to use Flows to create new resources.

{% page-ref page="create-a-wishlist.md" %}

{% page-ref page="create-a-blog.md" %}



