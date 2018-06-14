# Authentication

All API endpoints are secured with an `Authorization` token. The level of authorization depends on the type of token you created with your API keys. There are two types of authentication, implicit and client credentials.

Once you authenticate, you will receive back the seconds until the token expires. If you use one of our SDKs, we silently handle reauthentication for you. You will need to handle obtaining a new `Bearer` token if you are managing authentication yourself.

**Implicit**

The implicit authentication type is most commonly found inside client side applications that consume product data and perform add to cart and checkout processes.

You can’t perform any destructive actions to the store or view any sensitive data with an `implicit` token type.

**Client credential**

The client credential authentication type is most used inside backend applications and custom Dashboards. This authentication type permits actions like create, update and delete products, as well as viewing customer records.

If you are building a service that requires you to modify inventory or fulfil orders, then you’ll need a `client_credential`token.

