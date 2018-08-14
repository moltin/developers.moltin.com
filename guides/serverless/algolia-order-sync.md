# Algolia Order Sync

This short guide will get you up and running with order and transaction syncing with [Algolia](https://www.algolia.com/). This is extremely useful if you already use Algolia and/or require the advanced search facilities they provide.

The sync happens via a Moltin webhook and triggers a lamdba function. You will need an account with [Moltin](https://moltin.com/), Algolia, [Serverless](https://serverless.com/) and [AWS](https://aws.amazon.com/) to continue.

{% hint style="info" %}
Any orders that are updated, with fulfilment, change of shipping or payment will automatically update the Algolia index.
{% endhint %}

## 1. Deploy the serverless function

Before we begin, make sure you're familiar with the Serverless Framework and you have AWS configured. Head to the [Serverless Quickstart](https://serverless.com/framework/docs/providers/aws/guide/quick-start/) to learn more. You'll need to be signed into Serverless & AWS to continue.

You'll first want to clone the [GitHub repo](https://github.com/moltin-examples/algolia-order-sync) to your local machine and then install the necessary dependencies. If you're using Node 8, you can make use of `npx`, to avoid installing another global dependency.

```bash
git clone https://github.com/moltin-examples/algolia-order-sync.git
cd algolia-order-sync
yarn # or npm install
```

Next, sign up or sign into [Algolia](https://www.algolia.com/) and grab your API keys. Inside the `algolia-order-sync` directory you'll want to create an `.env` file and provide the following ENV variables:

* `ALGOLIA_APP_ID`
* `ALGOLIA_API_KEY`

Now you're ready to deploy!

```bash
npx serverless deploy
```

## 2. Create a Moltin Order webhook

{% hint style="info" %}
Replace `SERVERLESS_URL_HERE` with the actual deployed serverless URL provided during the step above.
{% endhint %}

{% tabs %}
{% tab title="Payload" %}
```javascript
{
  "data": {
    "type": "integration",
    "name": "Order sync with Algolia",
    "description": "Sync order data with Algolia",
    "enabled": true,
    "observes": [
      "order.created",
      "order.updated"
    ],
    "integration_type": "webhook",
    "configuration": {
      "url": "SERVERLESS_URL_HERE/orders"
    }
  }
}
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
	"data": {
		"id": "e0f8a58b-4003-4b79-a54c-a8b343f7e151",
		"type": "integration",
		"integration_type": "webhook",
		"name": "Order sync with Algolia",
		"description": "Sync order data with Algolia",
		"enabled": true,
		"configuration": {
			"url": "SERVERLESS_URL_HERE/orders"
		},
		"observes": [
			"order.created",
			"order.updated"
		],
		"links": {
			"self": "https://api.moltin.com/v2/integrations/e0f8a58b-4003-4b79-a54c-a8b343f7e151"
		},
		"meta": {
			"timestamps": {
				"created_at": "2018-08-14T08:25:57.374Z",
				"updated_at": "2018-08-14T08:25:57.374Z"
			}
		}
	}
}
```
{% endtab %}
{% endtabs %}

## 3. Create a Moltin Order Transaction webhook

{% hint style="info" %}
Replace `SERVERLESS_URL_HERE` with the actual deployed serverless URL provided during the step above.
{% endhint %}

{% tabs %}
{% tab title="Payload" %}
```javascript
{
  "data": {
    "type": "integration",
    "name": "Order transaction sync with Algolia",
    "description": "Sync order transaction data with Algolia",
    "enabled": true,
    "observes": [
      "transaction.created",
      "transaction.updated"
    ],
    "integration_type": "webhook",
    "configuration": {
      "url": "SERVERLESS_URL_HERE/transactions"
    }
  }
}
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
	"data": {
		"id": "42e07110-e116-4449-96e4-4b5edfc80d36",
		"type": "integration",
		"integration_type": "webhook",
		"name": "Order transaction sync with Algolia",
		"description": "Sync order transaction data with Algolia",
		"enabled": true,
		"configuration": {
			"url": "SERVERLESS_URL_HERE/transactions"
		},
		"observes": [
			"transaction.created",
			"transaction.updated"
		],
		"links": {
			"self": "https://api.moltin.com/v2/integrations/42e07110-e116-4449-96e4-4b5edfc80d36"
		},
		"meta": {
			"timestamps": {
				"created_at": "2018-08-14T08:26:58.935Z",
				"updated_at": "2018-08-14T08:26:58.935Z"
			}
		}
	}
}
```
{% endtab %}
{% endtabs %}

## 4. Test the integration

Now you have successfully deployed the serverless function and configured the Moltin webhooks, any new orders and order transactions that come in will be automatically synced to Algolia. 🎉

