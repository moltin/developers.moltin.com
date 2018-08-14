---
description: Describes how to create a related products feature using flows.
---

# Related Products

When creating a store it is useful to show the customer items that can be related to the product you are viewing. For example if you were to buy a pair of boxing gloves you may also want to buy a gum shield or some wraps for your hands. By displaying these related products a customer can conveniently add them to a cart without having to search through your product catalogue. One way we can achieve this is by using the flexibility of Flows and the relationship field. In this example, I am going to create a related-products feature for an online fishing and tackle store. You can follow along with this example, and modify the calls to use products and images relevant to your store.

### Create products

You can create your products via the API or the [Moltin dashboard](https://dashboard.moltin.com/stores), and in fact, adding product and images via the dashboard is quick and simple; however, in this example, we will create our products via the API for consistency with the later calls, where the direct API communication is needed.

When purchasing a fishing rod you may want to purchase other accessories such as a reel, tripod, tackle box, etc. First, we will create our fishing rod product, and then add the products that will be related.

#### Fishing Rod: {#fishing-rod}

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X "POST" https://api.moltin.com/v2/products \
  -H 'authorization: XXXX' \
  -H 'content-type: application/json' \
  -d '{
	"data": {
		"type": "product",
		"name" : "Penn Rampage II Surf Rod",
		"slug": "penn-rapage-II-surf-rod",
		"sku": "PRIISR",
		"manage_stock": true,
		"description": "PENN RAMPAGE II rods are a new generation of light but powerful rods. Engineered to be fished with both braided and monofilament line. The new, fast, blank construction provides a thin diameter blank which overall creates increasing casting distances and a better bite detection.",
		"status" : "live",
		"commodity_type": "physical",
		"price": [
			{ "amount": 9999, "currency": "GBP", "includes_tax": true}
		]
	}
}'
```
{% endtab %}

{% tab title="Response" %}
```bash
{
   "data":{
      "type":"product",
      "id":"7ca12065-7155-4d4c-93fd-a978844adc5d",
      "name":"Penn Rampage II Surf Rod",
      "slug":"penn-rapage-II-surf-rod",
      "sku":"PRIISR",
      "manage_stock": true,
      "description":"PENN RAMPAGE II rods are a new generation of light but powerful rods. Engineered to be fished with both braided and monofilament line. The new, fast, blank construction provides a thin diameter blank which overall creates increasing casting distances and a better bite detection.",
      "price":[
         {
            "amount":9999,
            "currency":"GBP",
            "includes_tax": true
         }
      ],
      "status":"live",
      "commodity_type":"physical",
      "relationships":{
​
      },
      "meta":{
         "display_price":{
            "with_tax":{
               "amount":9999,
               "currency":"GBP",
               "formatted":"£99.99"
            },
            "without_tax":{
               "amount":9999,
               "currency":"GBP",
               "formatted":"£99.99"
            }
         },
         "stock":{
            "level":0,
            "availability":"out-stock"
         }
      }
   }
}
```
{% endtab %}
{% endtabs %}

Next, we will add some accessories:

#### Fishing Reel: {#fishing-reel}

```bash
curl -X POST https://api.moltin.com/v2/products \
  -H 'authorization: XXXX' \
  -H 'content-type: application/json' \
  -d '{
	"data": {
		"type": "product",
		"name" : "Shimano Ultegra XSD Reel, Size: 14000",
		"slug": "shimano-ultegra-xsd-reel-size-14000",
		"sku": "SUXSD-RS14000",
		"manage_stock": true,
		"description": "If you'\''re a surf angler who doesnt just like your tackle to perform well but also look stunning too then you need look no further. Sleek and stylish, the Ultegra 14000 XS-D from Shimano will take your breath away at first glance. With its dark silver metallic frame and machined aluminium handle this is a reel that looks good at the waters edge.",
		"status" : "live",
		"commodity_type": "physical",
		"price": [
			{ 	"amount": 9999, 
				"currency": "GBP", 
				"includes_tax": true
			}
		]
	}
}'
```

#### Tackle Box: {#tackle-box}

```bash
curl -X POST https://api.moltin.com/v2/products \
  -H 'authorization: XXXX' \
  -H 'content-type: application/json' \
  -d '{
	"data": {
		"type": "product",
		"name" : "Grandeslam Hi Vis Seat & Tackle Box Combo with Black Harness & Side Tray",
		"slug": "grandslam-hi-vis-seat-and-tackle-box-combo",
		"sku": "GHVS-TB",
		"manage_stock": true,
		"description": "With a stylish, easy to carry design, this seat and tackle box combo from Grandeslam perfectly showcases what this brand is all about; angling made effortless, fishing given a stylish makeover, and anglers enjoying a lifestyle, not just a hobby",
		"status" : "live",
		"commodity_type": "physical",
		"price": [
			{ 
			"amount": 3999,
			"currency": "GBP",
			"includes_tax": true
			}
		]
	}
}'
```

#### Beach Shelter: {#beach-shelter}

```bash
curl -X POST https://api.moltin.com/v2/products \
  -H 'authorization: XXXX' \
  -H 'content-type: application/json' \
  -d '{
	"data": {
		"type": "product",
		"name" : "Imax Storm Safe Beach Shelter",
		"slug": "imax-storm-safe-beach-shelter",
		"sku": "ISS-BS",
		"manage_stock": true,
		"description": "The Imax Storm-Safe Beach Shelter is light, easily transported and simply indispensable! Secured by 8 supplied pegs (and skirting-shingle if necessary) this 4 -leg portable sanctuary can be fully closed and is designed to hold fast in the foulest conditions.",
		"status" : "live",
		"commodity_type": "physical",
		"price": [
			{ 
			"amount": 7999,
			"currency": "GBP",
			"includes_tax": true
			}
		]
	}
}'
```

Once we have a list of products, we can then add their images.

### Add images for products

We can add image files to our store via the files endpoint. We need to add files for each product by using the following call:

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST https://api.moltin.com/v2/files \
  -H 'authorization: XXXX' \
  -H 'content-type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW' \
  -F file=@gslam.jpg \
  -F public=true
```
{% endtab %}

{% tab title="Response" %}
```bash
{
    "data": {
        "type": "file",
        "id": "c152e2b2-6453-41b3-87c4-eeeb3c5e2890",
        "link": {
            "href": "https://s3-eu-west-1.amazonaws.com/bkt-svc-files-cmty-api-moltin-com/fd66f8c3-fad8-4da6-8c8e-ab8ab07dec47/c152e2b2-6453-41b3-87c4-eeeb3c5e2890.jpg"
        },
        "file_name": "ultegra.jpg",
        "mime_type": "image/jpeg",
        "file_size": 61013,
        "public": true,
        "meta": {
            "dimensions": {
                "width": 650,
                "height": 650
            },
            "timestamps": {
                "created_at": "2018-07-16T13:38:37.269617256Z"
            }
        },
        "links": {
            "self": "https://api.moltin.com/v2/files/c152e2b2-6453-41b3-87c4-eeeb3c5e2890"
        }
    }
}
```
{% endtab %}
{% endtabs %}

We can repeat this for every product image we need to upload, changing the file path for each call.

### Main Images to Product Relationships {#main-images-to-product-relationships}

Now that we have our image files uploaded, we need to link them to their respective products, so that they can be included with our product calls for display.

The following code example shows how to create a relationship between a product and a main image file. The product ID is submitted via the URL, and the file ID via the request body.

{% hint style="info" %}
If we do a GET request on our product, we can see that the relationship has been created in the call response.
{% endhint %}

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST https://api.moltin.com/v2/products/add95435-7d5e-4bb0-b498-5719c4965373/relationships/main-image \
  -H 'authorization: XXXX' \
  -H 'content-type: application/json' \
  -d '{
	"data": {
	    "type": "main_image",
	    "id": "ba131f2b-5805-46c3-9b76-241145307c5a"
	}
}'
```
{% endtab %}

{% tab title="Response" %}
```bash
{
    "data": {
        "type": "product",
        "id": "add95435-7d5e-4bb0-b498-5719c4965373",
        "name": "Grandeslam Hi Vis Seat & Tackle Box Combo with Black Harness & Side Tray",
        "slug": "grandslam-hi-vis-seat-and-tackle-box-combo",
        "sku": "GHVS-TB",
        "manage_stock": true,
        "description": "With a stylish, easy to carry design, this seat and tackle box combo from Grandeslam perfectly showcases what this brand is all about; angling made effortless, fishing given a stylish makeover, and anglers enjoying a lifestyle, not just a hobby",
        "price": [
            {
                "amount": 3999,
                "currency": "GBP",
                "includes_tax": true
            }
        ],
        "status": "live",
        "commodity_type": "physical",
        "meta": {
            "timestamps": {
                "created_at": "2018-07-16T11:46:01+00:00",
                "updated_at": "2018-07-19T10:21:58+00:00"
            },
            "display_price": {
                "with_tax": {
                    "amount": 3999,
                    "currency": "GBP",
                    "formatted": "£39.99"
                },
                "without_tax": {
                    "amount": 3999,
                    "currency": "GBP",
                    "formatted": "£39.99"
                }
            },
            "stock": {
                "level": 0,
                "availability": "out-stock"
            },
            "variation_matrix": []
        },
        "relationships": {
            "main_image": {
                "data": {
                    "type": "main_image",
                    "id": "ba131f2b-5805-46c3-9b76-241145307c5a"
                }
            }
        }
    }
}
```
{% endtab %}
{% endtabs %}

We can now rinse and repeat for each of our products and images, until all the relationships are created, and that is our products set up!

### Create Related-Items

We need to create the Flow functionality to link products together as related items.

#### Create Flow on Products

First, we need to create a products flow, as we are extending the functionality of our products resource.

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST https://api.moltin.com/v2/flows \
  -H 'authorization: XXXX' \
  -H 'content-type: application/json' \
  -d '{
  "data": {
    "type": "flow",
    "name": products",
    "slug": "products",
    "description": "creates a flow for related item",
    "enabled": true
  }
}'
```
{% endtab %}

{% tab title="Response" %}
{% hint style="info" %}
Take note of the updated Flow ID.
{% endhint %}

```bash
{
    "data": {
        "id": "7a890393-9771-488b-ab1c-a92ad11b6d99",
        "type": "flow",
        "name": "related-items",
        "slug": "product",
        "description": "creates a flow for related item",
        "enabled": true,
        "links": {
            "self": "https://api.moltin.com/v2/flows/7a890393-9771-488b-ab1c-a92ad11b6d99"
        },
        "relationships": {},
        "meta": {
            "timestamps": {
                "created_at": "2018-07-23T09:19:37.264Z",
                "updated_at": "2018-07-23T09:19:37.264Z"
            }
        }
    }
}
```
{% endtab %}
{% endtabs %}

#### Create Related-Items field

To store our related product IDs, we need to create a new field and relate it to our Flow:

```bash
curl -X POST https://api.moltin.com/v2/fields \
  -H 'authorization: XXXX' \
  -H 'content-type: application/json' \
  -d '{
  "data": {
    "type": "field",
    "name": "Related Items",
    "slug": "related-items",
    "description": "Items related to a given product",
    "required": false,
    "unique": false,
    "enabled": true,
    "field_type": "relationship",
        "relationships": {
        "flow": {
            "data": {
                "type": "flow",
                "id": "7a890393-9771-488b-ab1c-a92ad11b6d99"
            }
        }
    },
        "validation_rules": [
        {
            "type": "one-to-many",
			"to": "product"
        }
    ]
  }
}'
```

You may have noticed in the call we just made that there are some validation rules.

```bash
"validation_rules": [
        {
            "type": "one-to-many",
			"to": "product"
        }
   ]
```

As a product may have several or "many" related products, we need to specify that we want this relationship field to be a **one-to-many** relationship.

#### Create Product Relationships

Now that we have set up our flow and our relationship field, we can begin linking our products to create the related products data model.

We take the product we want to relate items to, in this case the Rampage fishing rod, and put it in the URL of the request.

 We then specify which product we wish to relate to it in the request body, and repeat this for every product we wish to relate to the product specified in the request URL. With each request, you should see your related items being added to the response:

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST https://api.moltin.com/v2/products/7ca12065-7155-4d4c-93fd-a978844adc5d/relationships/related-items \
  -H 'authorization: XXXX' \
  -H 'content-type: application/json' \
  -d '{
 "data": [{
        "type": "product",
        "id": "add95435-7d5e-4bb0-b498-5719c4965373"
    }]
}'
```
{% endtab %}

{% tab title="Response" %}
```bash
{
    "data": [
        {
            "type": "product",
            "id": "add95435-7d5e-4bb0-b498-5719c4965373"
        },
        {
            "type": "product",
            "id": "c4dc0875-ffed-44b2-8979-e331c7de0b72"
        },
        {
            "type": "product",
            "id": "91000863-337b-4482-a4a1-10866b5229c6"
        }
    ]
}
```
{% endtab %}
{% endtabs %}

Once we have our products related, we can display them in our web applications.

If we do a GET request on our main product, we should see a response containing the product information and the IDs of the related products.

```bash
{
    "data": {
        "type": "product",
        "id": "7ca12065-7155-4d4c-93fd-a978844adc5d",
        "name": "Penn Rampage II Surf Rod",
        "slug": "penn-rapage-II-surf-rod",
        "sku": "PRIISR",
        "manage_stock": true,
        "description": "PENN RAMPAGE II rods are a new generation of light but powerful rods. Engineered to be fished with both braided and monofilament line. The new, fast, blank construction provides a thin diameter blank which overall creates increasing casting distances and a better bite detection.",
        "price": [
            {
                "amount": 9999,
                "currency": "GBP",
                "includes_tax": true
            }
        ],
        "status": "live",
        "commodity_type": "physical",
        "meta": {
            "timestamps": {
                "created_at": "2018-07-16T11:23:58+00:00",
                "updated_at": "2018-07-19T10:54:57+00:00"
            },
            "display_price": {
                "with_tax": {
                    "amount": 9999,
                    "currency": "GBP",
                    "formatted": "£99.99"
                },
                "without_tax": {
                    "amount": 9999,
                    "currency": "GBP",
                    "formatted": "£99.99"
                }
            },
            "stock": {
                "level": 0,
                "availability": "out-stock"
            },
            "variation_matrix": []
        },
        "relationships": {
            "related-items": {
                "data": [
                    {
                        "type": "product",
                        "id": "add95435-7d5e-4bb0-b498-5719c4965373"
                    },
                    {
                        "type": "product",
                        "id": "c4dc0875-ffed-44b2-8979-e331c7de0b72"
                    },
                    {
                        "type": "product",
                        "id": "91000863-337b-4482-a4a1-10866b5229c6"
                    }
                ]
            },
            "main_image": {
                "data": {
                    "type": "main_image",
                    "id": "33a3ab07-bb7b-4dda-9a42-3c07870002fa"
                }
            }
        }
    }
}
```

### Display Related Items

{% tabs %}
{% tab title="JavaScript" %}
```javascript
<html>
<head>
  <title>Related Items</title>
</head>
<body>
  <h1>Main Product</h1>
  <div id="app">
      <h3>{{ product.name }}</h3>
      <img :src="product.image">
      {{ product.description }}
      <h2>Related Items</h2>
    <div v-for="product in relatedItems">
            <h3>{{ product.name }}</h3>
            <img :src="product.image">
            {{ product.description }}
    </div>
  </div>
​
  <script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
  <script src="https://unpkg.com/@moltin/sdk"></script>
​
  <script>
    const Moltin = moltin.gateway({
      client_id: 'ANH4lUED9iDKiQAFjgG7urWW5DGYIp6QIS9agUqrYo'
    })
​
    new Vue({
      el: "#app",
      data() {
        return {
          product: {},
          relatedItems: []
        }
      },
      created() {
        this.fetchMoltinData({ id: '7ca12065-7155-4d4c-93fd-a978844adc5d' })
      },
      methods: {
        async fetchMoltinData({ id }) {
          const product = await this.fetchProduct({ id })
​
          this.product = product
          this.relatedItems = await this.fetchRelatedProducts({ product })
        },
        async fetchProduct({ id }) {
          const { data, included: { main_images } } = await Moltin.Products.With('main_image').Get(id)
​
          const imageId = data.relationships.main_image.data.id
​
          return {
              ...data,
              image: main_images.find(img => img.id === imageId).link.href
        }
        },
        fetchRelatedProducts({ product }) {
          const { relationships: { 'related-items': { data: products } } } = product
​
          return Promise.all(products.map(({ id }) => this.fetchProduct({ id })))
        }
      }
    })
  </script>
​
</body>
</html>
```
{% endtab %}
{% endtabs %}

