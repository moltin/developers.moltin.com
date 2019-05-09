# Custom data - Flows

One of the great features of Moltin is the ability to extend existing core resources and the flexibility to create your own. With [Flows](https://docs.moltin.com/advanced/custom-data), you are able to create various types of fields, including strings, booleans, dates, integers and relationships with other custom flows.

The Moltin API aims to provide predictable, easy to use, RESTful endpoints that can power any ecommerce project. With Flows, you are able to apply Moltin to almost any use case.

### **Flows taxonomy**

When we talk about Flows, we need to talk about Fields and Entries that are an integral part of them. A Flow describes a collection of Fields, whereas a Field represents a single field of data. An Entry is a specific instance of a Flow, and is associated with a specific instance of an entity \(for example, a single product\). Entries for core Flows \(existing resources\) are managed automatically for you.

So to put simply, Flows are like tables in a database. Fields are like columns whereas Entries are like database records.

There are two scenarios for using Flows:

1. Extending an existing resource \(e.g. products, orders, etc.\).
2. Adding a new resource \(e.g. blog, wish list, customer review, etc.\).

## Extending resources

The most common usage of the Flows API is to extend existing resources like products, customers and orders. These are called core Flows and are handled by Moltin differently to non-core Flows - see: [Custom Data](https://docs.moltin.com/~/drafts/-LKaw44strBlTmOqfUwj/primary/advanced/custom-data) in the API Reference for details and the full list of core Flows.

If you wish to store extra fields per product, such as `manufactured_date` for customers or a string field to store a `colour` hexadecimal for the product for use on the UI, you'll want to extend the `products` resource.

Click the links below to view examples of how to use Flows to extend resources.

{% page-ref page="extending-product/" %}

## New resources

With the power of Flows, you can create new resources to power product reviews, wish lists, blog articles, staff profile pages, and many more.

Click the links below to view examples of how to use non-core \(custom\) Flows to create new resources.

{% page-ref page="create-a-wishlist.md" %}

{% page-ref page="create-a-blog.md" %}



