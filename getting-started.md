# Getting Started

## 1. Create a Moltin account

You must [create a Moltin account](https://dashboard.moltin.com/signup) to use the Moltin API.

## 2. Invite your team

You can invite team members to join your project via the Moltin [account dashboard](https://accounts.moltin.com). Each team member will have full access to your project via the API and dashboard.

Every team member has their own unique API `client_id` and `client_secret`. This is really helpful if you invite external developers to work on your project, you can easily revoke their access at anytime.

## 3. Get your API keys

Access to the Moltin API is protected by [authentication tokens](https://docs.moltin.com/basics/authentication). Tokens are generated using API keys and can be found via the [Moltin dashboard](https://dashboard.moltin.com). 

Every user is provided `client_id` and `client_secret` keys. These API keys are per user per project and are used to generate two types of [authentication tokens](https://docs.moltin.com/basics/authentication).

{% hint style="warning" %}
You should never share the `client_secret` or use it client side.
{% endhint %}

## 4. Start building with Moltin

You now have everything to get started building with Moltin. We recommend you get started with [your first API request](your-first-api-request.md).  


