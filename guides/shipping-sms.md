# Shipping SMS

One of the quickest ways to tell customers their orders have been shipped is by SMS. Using [Twilio](https://www.twilio.com) and [Zeit Now](https://zeit.co/now), we can deploy a function that is automatically triggered on `order.shipped`✨

This guide will walk you through extending the Order resource via the Flows \([Custom Data](https://docs.moltin.com/advanced/custom-data)\) API and deploying a function to handle sending the SMS.

## 1. Get your access token

You will need to get a [`client_credentials`](https://docs.moltin.com/basics/authentication/client-credential-token) access token to follow along making the API requests outlined below.

```bash
curl -X "POST" "https://api.moltin.com/oauth/access_token" \
     -d "client_id=XXXX" \
     -d "client_secret=XXXX" \
     -d "grant_type=client_credentials"
```

## 2. Create a new Flow

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

Take note of the ID that is returned. You'll need this to create a new Flow field, as described below.

## 3. Create a few Flow Fields

{% hint style="info" %}
Replace `FLOW_ID` with the ID of the `orders` Flow you created.
{% endhint %}

In this step, we will create a field for `phone_number` that will be used to send the SMS to.

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

Next, we will store a boolean flag for `shipping_sms_sent` that is used to determine if an SMS should be sent.

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

## 4. Deploy the function

To successfully deploy the function you will need:

* Account with [Twilio](https://www.twilio.com/)
* Account with [Moltin](https://dashboard.moltin.com/signup)
* Account with [Zeit](https://zeit.co/now)

You'll need the following ENV variables to successfully deploy the function:

* `MOLTIN_CLIENT_ID`
* `MOLTIN_CLIENT_SECRET`
* `MOLTIN_WEBHOOK_SECRET`
* `TWILIO_ACCOUNT_SID`
* `TWILIO_AUTH_TOKEN`
* `TWILIO_FROM_NUMBER`
* `TWILIO_SMS_BODY`

```bash
npm i -g now # unless installed already
now moltin-examples/short-order-id -e MOLTIN_CLIENT_ID=x MOLTIN_CLIENT_SECRET=x MOLTIN_WEBHOOK_SECRET=x TWILIO_ACCOUNT_SID=x TWILIO_AUTH_TOKEN=x TWILIO_FROM_NUMBER=x TWILIO_SMS_BODY=x
```

{% hint style="warning" %}
Take note of the URL that is returned on successful deployment to Now.
{% endhint %}

## 5. Create a new webhook

With the function deployed, we can now tell Moltin to start subscribing to new orders created.

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
            "url": "INSERT_URL_OF_FUNCTION_HERE",
            "secret_key": "MOLTIN_WEBHOOK_SECRET_VALUE_HERE"
          }
        }
     }'
```



