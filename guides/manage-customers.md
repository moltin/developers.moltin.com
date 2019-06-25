# Manage customers

{% hint style="danger" %}
Content moved to [Developer Concepts page](https://www.moltin.com/developer/concepts/how-to-manage-customers).
{% endhint %}

Manage your customers through the API or directly through [Moltin dashboard](https://dashboard.moltin.com/).

### **Create a customer**

If you are creating a new customer on the API, you can optionally submit a password along with that customer’s details.

{% hint style="warning" %}
Customer creation is currently not available in the iOS SDK \(support for implicit authentication only\). Also, the PHP SDK doesn’t contain the Customers endpoints.
{% endhint %}

### **Delete a customer**

Use a `customer_id` or `customer token` to delete an existing customer if using an API, or do it directly through [Moltin dashboard](https://dashboard.moltin.com/).

### **Customer authentication - passwords**

If you are creating a new customer on the API you can submit a password along with that customer’s details. Once you create a password for a customer, it cannot be retrieved again. Any customers with passwords will have a `true` value in the `password` field.

If you have an existing customer who does not have a password, you can add the password field using the `customer_id`.

### **Customer token**

{% hint style="info" %}
You can only generate a customer token for existing customers using a combination of their email and password.
{% endhint %}

Use customer token for a scenario in which you don’t have full read/write access to the API \([implicit token](https://docs.moltin.com/basics/authentication/implicit-token) type\) and you want to authenticate your customers through email and password to access and manage their own details, such as address, orders, etc.

In detail, use a [`customer token`](https://docs.moltin.com/orders-and-customers/customers/customer-tokens) to:

* Authenticate your customer’s identity \(instead of using email address and password\).
* Get/update customer’s details.
* Get/update customer’s address.
* Get customer’s orders.
* Provide your customer with implicit access to their details and orders.

### **Manage existing customers**

#### **Addresses**

One customer can have unlimited addresses set up for them, such as home, neighbor, wife’s office, etc. Use [Moltin dashboard](https://dashboard.moltin.com/) or set them up through the [API](https://docs.moltin.com/orders-and-customers/addresses). You can add/update/delete customer addresses on their behalf.

If you’re using customer token scenario, you can grant your customers access to their own data so that they can manage their own address details \(add/update/delete an address\). You can also cater for already authenticated users, and make their address part of the checkout workflow \(as opposed to guest users\).

#### **Orders**

Streamline the checkout workflow for your existing customers and create a [Customer\_ID-based checkout](https://docs.moltin.com/carts-and-checkout/checkout) workflow. The returned order object will include the customer’s mailing details and create a relationship between the customer and the order that you can later use to retrieve all orders for a particular customer. Use it to provide your customers with ability to review their order history.

To retrieve orders for a customer, include the following details in the request header:

* Authentication token
* Customer token

### **Guest customers**

To cater for guest customers, create a checkout flow based on the customer object. Each time, a guest customer will have to provide full shipping and billing details.

{% hint style="info" %}
It is not possible to link orders with customer object, and as such, create orders records for guest users.
{% endhint %}

### **Add custom fields to a Customer object**

Extend Customer object with [Flows](https://docs.moltin.com/~/drafts/-LKaw44strBlTmOqfUwj/primary/advanced/custom-data) that allow you to add custom fields. To add a field, you’ll need to pass customer token in the request header.

### **Filter customers**

Customer [filtering](https://docs.moltin.com/basics/filtering) is useful for customer management, e.g. it could help you determine whether to send a user to a signup or login function. If using JavaScript SDK, make sure it’s version 3.3.0 or higher.  


