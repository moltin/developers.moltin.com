# Shipping SMS

One of the quickest ways to tell customers their orders have been shipped is by SMS.

Using [Twilio](https://www.twilio.com) and [Zeit Now](https://zeit.co/now) we can deploy a function that is automatically triggered on `order.shipped`.

## How to use

This guide will walk you through extending the Order resource via the Flows \(Custom Data\) API and deploying a function to handle sending the SMS.

## 1. Create a new Flow

With the power of Flows we can extend the Moltin API with our own custom data. Let's extend the orders resource by creating a new flow.

{% hint style="info" %}
If you already have a Flow for `orders`, skip to [Create a few Flow Fields](shipping-sms.md#2-create-a-few-flow-fields). You'll need the ID of the orders Flow to continue.
{% endhint %}

```bash
curl -X POST https://api.moltin.com/v2/flows \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "flow",
          "name": "orders",
          "slug": "orders",
          "description": "Extend orders resource with a custom flow.",
          "enabled": true
        }
     }'
```

Take note of the ID that is returned. You'll need this below.

## 2. Create a few Flow Fields

{% hint style="info" %}
Replace `FLOW_ID` with the ID of the `orders` Flow you created.
{% endhint %}

In this step we will create a field for `phone_number` that will be used to send the SMS to.

```bash
curl -X POST https://api.moltin.com/v2/fields \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "string",
          "name": "Customer phone number",
          "slug": "phone_number",
          "description": "This is the phone number for the customer.",
          "enabled": true,
          "relationships": {
            "flow": {
              "type": "flow",
              "id": "FLOW_ID"
            }
          }
        }
     }'
```

Next we will store a boolean flag for `shipping_sms_sent` that is used to determine if an SMS should be sent.

```bash
curl -X POST https://api.moltin.com/v2/fields \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "boolean",
          "name": "Shipping SMS Sent (managed by webhook)",
          "slug": "shipping_sms_sent",
          "description": "This is a SMS flag managed by a webhook.",
          "default": false,
          "enabled": true,
          "relationships": {
            "flow": {
              "type": "flow",
              "id": "FLOW_ID"
            }
          }
        }
     }'
```

## 3. Deploy the function

To successfully deploy the function you will need:

* Account with Twilio
* Account with Moltin
* Account with Zeit Now

```bash
git clone git@github.com:moltin-examples/moltin-twilio-shipping-sms.git
cd moltin-twilio-shipping-sms
npm install
```

Before you can deploy the function, you'll need to configure the `.env` with the required environment variables below to successfully deploy the function. You can see more about the following over at [GitHub](https://github.com/moltin-examples/moltin-twilio-shipping-sms#-setup).

Inside the `moltin-twilio-shipping-sms` directory, **create a file** called `.env` and add the following:

* `MOLTIN_CLIENT_ID`
* `MOLTIN_CLIENT_SECRET`
* `MOLTIN_WEBHOOK_SECRET`
* `TWILIO_ACCOUNT_SID`
* `TWILIO_AUTH_TOKEN`
* `TWILIO_FROM_NUMBER`
* `TWILIO_SMS_BODY`

Once done, you're ready to deploy the function to Zeit Now! You'll need to install the `now-cli` if you don't already have it.

```bash
npm i -g now # if not already installed
now
```

{% hint style="warning" %}
Take note of the URL that is returned on successful deployment to Now.
{% endhint %}

## 4. Create a new webhook

With the function deployed we can now tell Moltin to start subscribing to new orders created.

```bash
curl -X POST https://api.moltin.com/v2/integrations \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "integration",
          "name": "Shipping SMS notification",
          "description": "Send an SMS via Twilio when an order is shipped",
          "enabled": true,
          "observes": [
            "order.shipped"
          ],
          "integration_type": "webhook",
          "configuration": {
            "url": "INSERT_URL_OF_FUNCTION_HERE"
          }
        }
     }'
```



