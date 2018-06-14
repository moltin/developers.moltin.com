---
description: 'Variations, options and modifiers'
---

# Introducing Variations

Variations allow stores to define their products and easily generate the multitude of ‘child-products’ available by specifying options such as color or size. This post describes how, why, and what to expect from the system when generating your ‘child-products’.

All of the information you need to start using variations is available in [the docs](https://docs.moltin.com/#products) but we’re going to embellish a little more here to try and demonstrate the power of this new system.

### Overview of the variation product process {#overview-of-the-variation-product-process}

So you sell t-shirts… well some are pretty similar. They might have the same design but they come in different colors and sizes. This is where `variations` enter the fray.

You might sell a certain t-shirt in three colors — red, blue and [Pantone 448 C](https://www.digitaltrends.com/photography/pantone-448-c-ugliest-color/), and three sizes — small, medium and large. Now you _could_ go and create nine different products within the system that have no relationship to each another and try and manage them all separately.

**STOP!** `Variations` are your friend here. The process would be something like \(shortened version - omitting many important facets for brevity\):

* Add the base product like [3 Wolves t-shirt](https://www.amazon.com/The-Mountain-Men-s-Three-Wolf-Moon-Short-Sleeve-Tee/dp/B002HJ377A) with price, SKU, description etc, etc.
* Create a variation for color
* Create three variation options for red, blue and Pantone 448 C
* Create a variation for size
* Create three variation options for small, medium, large
* Apply the two variations \(color, size\) to your product
* Build child products

The result of this process would be the creation of nine child products:

* Red Small
* Red Medium
* Red Large
* Blue Small
* Blue Medium
* Blue Large
* Pantone 448 C Small
* Pantone 448 C Medium
* Pantone 448 C Large

OK, so we skipped a few things like managing SKUs and prices but these are addressed in [our blog post on modifiers](https://moltin.com/blog/2017/06/magic-modifiers/), so do not fret, we’re on it.

Now, none of that should be surprising nor remarkable; while implementations will differ, most commerce systems provide this kind of feature. Some variation systems are pretty restrictive — at the time of writing, a Shopify product variation can only have a maximum of three options and a product can have a maximum 100 variations, a lot yes, but still a restriction. We like being different \(we actually like being better even more - but you’ll be the judge of that\) so we’re presenting a `variations` system we feel is more flexible and, consequently, more useful to our customers.

### Moltin’s approach to variations {#moltins-approach-to-variations}

`Variations` are standalone entities on the Moltin platform. You do not have to create a product, let alone a product of a predefined type, and then start adding variations to it.

Moltin does _not_ bind `variations` to a solitary product. This may be a strange concept, but actually, the work involved in creating and tuning your variations can be quite significant. We decided to make your work re-useable so you won’t have to sit and duplicate your variations over and over and over again for similar types of products.

Don’t worry if you are not a technical individual — while there are code examples by way of cURL requests, JSON payloads and demonstration responses, they are accompanied by some explanation.

All the requests demonstrated in the post are predicated on you having authenticated with the platform using your client credentials like so:

**Request**:

```javascript
curl -X POST
     -d "client_id=XXXXX"
     -d "client_secret=xxxxx"
     -d "grant_type=client_credentials"
     https://api.moltin.com/oauth/access_token
```

#### Response: {#response}

```javascript
{
    "expires": 1436287646,
    "identifier": "client_credentials",
    "expires_in": 3600,
    "access_token": "0bfcb98e2d903c680775e209cde36a73c5d82000",
    "token_type": "Bearer"
}
```

You will need the token in the response from the above call in _ALL_ request made to administer this feature.

We don’t detail any error conditions in this post — please see the [API reference](https://docs.moltin.com/)\) for more details on the full range of responses you might expect.

Right, enough of this — let’s take a deep\(ish\) dive into `variations`.

### Managing variations {#managing-variations}

Within our ecosystem, a `variation` is simply a container for a number of `options`. The `options` are defined by you,  
but even they do not actually _do_ anything, they just hold magic. All the magic is packaged in `modifiers` — which we think are awesome and covered in far greater depth in [our blog post on modifiers](https://moltin.com/blog/2017/06/magic-modifiers/) so you have to read it!

### Creating variations {#creating-variations}

Let’s start with a simple cURL request…  


**Request:**

```javascript
curl -H "Authorization: Bearer 0bfcb98e2d903c680775e209cde36a73c5d82000"
     -H "Content-Type: application/json"
     -X POST
     -d '{
             "data": {
                 "type": "product-variation",
                 "name": "Size"
             }
         }'
     https://api.moltin.com/v2/variations
```

**Response:**

```text
{
    "data": {
        "type": "product-variation",
        "id": "eb85a218-ce00-44dc-a676-b8345b314544",
        "name": "Size"
    }
}
```

That is the `size` variation created. The data: `-d '{ "data": { "type": "product-variation", "name": "Size" } }'` is posted to the endpoint `https://api.moltin.com/v2/variations` and what is returned is almost identical with the exception of this `id` property.

We have our `variation` which, as we mentioned earlier, is a container for some `options` so these need to be added.

### Creating options {#creating-options}

So, we said that `variations` are containers for `options`. It doesn’t really make sense for an option to exist outside the context of a `variation`. To that end `options` are managed within the context of the parent `variation`.

**Request:**

```javascript
curl -H "Authorization: Bearer 0bfcb98e2d903c680775e209cde36a73c5d82000"
     -H "Content-Type: application/json"
     -X POST
     -d '{
             "data": {
                 "type": "product-variation-option",
                 "name": "Small",
                 "description": "Small item"
             }
         }'
     https://api.moltin.com/v2/variations/eb85a218-ce00-44dc-a676-b8345b314544/variation-options
```

**Response:**

```javascript
{
    "data": {
        "type": "product-variation",
        "id": "eb85a218-ce00-44dc-a676-b8345b314544",
        "name": "Size",
        "options": [{
            "type": "product-variation-option",
            "id": "427c253f-29e1-47bc-9139-c44c1157d181",
            "name": "Small",
            "description": "Small item"
        }]
    }
}
```

The payload is almost self-explanatory, a `product-variation-option` with the name `small` is to be created. Note that we reference the parent `variation` by its `ID` in the URL.

The response is the full `variation` which contains our newly created `option` - we don’t recognize an `option` outside it’s parent `variation` so we do not represent an `option` but as a constituent of it’s `variation`.

Now for the magic…

### Creating modifiers {#creating-modifiers}

It’s all well and good that we can define our `variations` and `options` but they don’t _do_ anything. We need to augment our base product to differentiate child-products from one another.

The first caveat of Moltin’s modifiers - for each option you _MUST_ create modifiers to adapt the `SKU` and `slug` of the base product to ensure that each product in the system has unique values for these properties. The second is that there are restrictions on the number of modifier types being applied to an option; you cannot attach a price\_increment _AND_ a price\_decrement modifier to the same variation option.

The types of modifiers you can create are detailed in the [modifiers post](https://moltin.com/blog/2017/06/magic-modifiers/), so for now, we are simply demonstrating how to create one using the `name_append` `modifier`which, as the name suggests, will append something to the name of the generated product.

**Request:**

```javascript
curl -H "Authorization: Bearer 0bfcb98e2d903c680775e209cde36a73c5d82000"
     -H "Content-Type: application/json"
     -X POST
     -d '{
             "data": {
                 type": "product-modifier",
                 "modifier_type": "name_append",
                 "value": "-Mini"
             }
         }'
     https://api.moltin.com/v2/variations/eb85a218-ce00-44dc-a676-b8345b314544/variation-options/427c253f-29e1-47bc-9139-c44c1157d181/product-modifiers

```

**Response:**

```javascript
{
    "data": {
        "type": "product-variation",
        "id": "eb85a218-ce00-44dc-a676-b8345b314544",
        "name": "Size",
        "options": [{
            "type": "product-variation-option",
            "id": "427c253f-29e1-47bc-9139-c44c1157d181",
            "name": "Small",
            "description": "Small item",
            "modifiers": [{
                "type": "product-modifier",
                "id": "6f5749b8-c341-4911-89e4-a9afc5d25c57",
                "modifier_type": "name_append",
                "value": "-Mini"
            }
        }]
    }
}
```

Again, we specify which `variation` and which `option` under which we are creating this `modifier`. The `id` of the `variation` and the `option` are present in the URL. Also once more we do not recognize a `modifier` outside the context of its parents and so the response body contains the full `variation` object.

More details on `modifiers`, what they can do and how to use them can be found later in our [magic of modifiers post](https://moltin.com/blog/2017/06/magic-modifiers/).

### Building child products {#building-child-products}

With a little more work creating more `options` and `modifiers` \(not forgetting the need for SKU and slug `modifiers`!\) and perhaps more `variations` we might have something capable of generating a significant number of child products, but we have yet to accomplish anything remotely ‘useful’.

In order to build our child-products, we must first attach our `variations` to a product. Let’s pretend you already have a product that is destined for the `variation` treatment…

Let us imagine we had the product `id: 3255c45e-dd8c-4503-9b13-37e9afca7553` and had created a second `variation` with the `id: b6890952-a843-4e77-9b39-729591759bd9.`

**Request:**

```javascript
curl -H "Authorization: Bearer 0bfcb98e2d903c680775e209cde36a73c5d82000"
     -H "Content-Type: application/json"
     -X POST
     -d '{
             "data": [ {
                 "type": "product-variation",
                 "id": "eb85a218-ce00-44dc-a676-b8345b314544"
             }, {
                 "type": "product-variation",
                 "id": "b6890952-a843-4e77-9b39-729591759bd9"
             }]
         }'
     https://api.moltin.com/v2/products/3255c45e-dd8c-4503-9b13-37e9afca7553/relationships/variations
```

The successful response, in this case, would be the same as the payload sent in the request. The product has now had these two `variations` attached to it. You could attach the same `variations` to any number of products so, depending on how you design your `modifiers` and the augmentations they perform, the \`variations could be used many times over.

The final step in the process is to actually build the child products.

**Request:**

```javascript
curl -H "Authorization: Bearer 0bfcb98e2d903c680775e209cde36a73c5d82000"
     -H "Content-Type: application/json"
     -X POST
     https://api.moltin.com/v2/products/3255c45e-dd8c-4503-9b13-37e9afca7553/build
```

This response is lifted straight out of the documentation — it is quite sizeable but, TL;DR: it’s a list of the child products built from the `variations`.

The longer version… from a base product, each and every combination of variation option is applied to create a new product and the `modifiers` attached to these `options` modify some property of the base product. How they modify them is detailed in the [modifier blog post](https://moltin.com/blog/2017/06/magic-modifiers/).

**Response:**

```javascript
{
    "data": [
        {
            "type": "product",
            "id": "abd229a2-bce1-47bd-960c-30f0585abb8a",
            "name": "ut",
            "slug": "veniam sed",
            "sku": "anim dolore",
            "manage_stock": true,
            "description": "occaecat dolore",
            "price": [
                {
                    "amount": 98564450,
                    "currency": "ullamco",
                    "includes_tax": true
                }
            ],
            "status": "live",
            "commodity_type": "physical"
        },
        {
            "type": "product",
            "id": "3774a68b-2c8b-4b0b-9f4a-3eeaa83964a7",
            "name": "dolore consectetur in Ut",
            "slug": "Duis",
            "sku": "do labore in minim",
            "manage_stock": true,
            "description": "al",
            "price": [
                {
                    "amount": -56995833,
                    "currency": "mollit",
                    "includes_tax": false
                },
                {
                    "amount": 29671084,
                    "currency": "nisi et mollit",
                    "includes_tax": true
                },
                {
                    "amount": -11207982,
                    "currency": "Ut",
                    "includes_tax": true
                },
                {
                    "amount": -88347566,
                    "currency": "aute ipsum nostrud",
                    "includes_tax": false
                }
            ],
            "status": "live",
            "commodity_type": "digital",
            "links": {
                "self": null,
                "first": "http://selena.name",
                "last": null,
                "prev": "http://michelle.org",
                "next": null
            },
            "relationships": {
                "variations": {
                    "data": [
                        {
                            "type": "product-variation",
                            "id": "fc4ff53f-1749-499b-aa0d-697b4f8842c5"
                        },
                        {
                            "type": "product-variation",
                            "id": "e5bb603c-7ba4-4d3d-83ff-d42d52482148"
                        },
                        {
                            "type": "product-variation",
                            "id": "2d43607a-2870-429d-b31f-f49b265bd88f"
                        },
                        {
                            "type": "product-variation",
                            "id": "a0120a70-14b0-4833-9d84-e586dc5b455c"
                        },
                        {
                            "type": "product-variation",
                            "id": "67895663-fca3-489c-8d42-e2b12f0ab524"
                        }
                    ]
                },
                "files": {
                    "data": [
                        {
                            "type": "file",
                            "id": "4d5bb6b2-c992-4402-9e51-7fa740543baf"
                        }
                    ]
                }
            }
        },
        {
            "type": "product",
            "id": "de47b27d-66c5-4e25-9199-a3a21f855493",
            "name": "in in exercitation Ut est",
            "slug": "dolore minim nisi sit",
            "sku": "voluptate sint",
            "manage_stock": false,
            "description": "laboris ea enim",
            "price": [
                {
                    "amount": -3302978,
                    "currency": "off",
                    "includes_tax": false
                },
                {
                    "amount": -3034645,
                    "currency": "officia et non mollit adipisicing",
                    "includes_tax": false
                }
            ],
            "status": "live",
            "commodity_type": "physical",
            "weight": {
                "g": {
                    "unit": "g",
                    "value": -24175932
                },
                "kg": {
                    "unit": "kg",
                    "value": 53628106
                },
                "lb": {
                    "unit": "lb",
                    "value": 10693173
                },
                "oz": {
                    "unit": "oz",
                    "value": -52846370
                }
            }
        },
        {
            "type": "product",
            "id": "2df96f6a-472f-4a7c-b981-7354a116ab2e",
            "name": "nulla dolor officia",
            "slug": "cillum minim",
            "sku": "Lorem Excepteur anim proident",
            "manage_stock": true,
            "description": "nisi",
            "price": [
                {
                    "amount": 4969341,
                    "currency": "Ut dolore amet",
                    "includes_tax": true
                },
                {
                    "amount": -75235226,
                    "currency": "dolore commodo culpa deserunt do",
                    "includes_tax": false
                },
                {
                    "amount": 41637350,
                    "currency": "dolor",
                    "includes_tax": false
                },
                {
                    "amount": 48076630,
                    "currency": "labore",
                    "includes_tax": true
                }
            ],
            "status": "live",
            "commodity_type": "digital",
            "relationships": {
                "variations": {
                    "data": [
                        {
                            "type": "product-variation",
                            "id": "0563ec58-149c-4948-a803-70839c545231"
                        },
                        {
                            "type": "product-variation",
                            "id": "58468f09-3a8c-4325-8123-294f255618c5"
                        },
                        {
                            "type": "product-variation",
                            "id": "813f2752-4988-4fdc-80e7-9f8e2b5db214"
                        },
                        {
                            "type": "product-variation",
                            "id": "5703e538-8594-4740-a092-f779386ee600"
                        }
                    ]
                },
                "files": {
                    "data": [
                        {
                            "type": "file",
                            "id": "8aa8ba31-4aed-48e6-9b8f-c50caf925d39"
                        },
                        {
                            "type": "file",
                            "id": "38d07c91-62c4-4d71-9ab1-7268d8b75586"
                        },
                        {
                            "type": "file",
                            "id": "0774dd6e-8261-405e-8ca6-5347aa67787c"
                        }
                    ]
                }
            },
            "dimensions": {
                "width": {
                    "cm": {
                        "unit": "labore",
                        "value": 9372622
                    },
                    "m": {
                        "unit": "enim est cillum labore",
                        "value": 58356722
                    },
                    "ft": {
                        "unit": "laborum id",
                        "value": 95669514
                    },
                    "in": {
                        "unit": "Lorem incididunt consequat Duis velit",
                        "value": -24995090
                    },
                    "yd": {
                        "unit": "labore ut aute elit",
                        "value": 72065727
                    },
                    "mi": {
                        "unit": "ea nulla adipisicing consequat dolor",
                        "value": -43338206
                    }
                },
                "height": {
                    "cm": {
                        "unit": "elit labore sunt esse",
                        "value": 91173905
                    },
                    "m": {
                        "unit": "et in",
                        "value": 10278651
                    },
                    "ft": {
                        "unit": "non ullamco Lorem cupidatat",
                        "value": -16964045
                    },
                    "in": {
                        "unit": "reprehenderit",
                        "value": -56841676
                    },
                    "yd": {
                        "unit": "do eu",
                        "value": -31092122
                    },
                    "mi": {
                        "unit": "dolor",
                        "value": 5011454
                    }
                },
                "length": {
                    "cm": {
                        "unit": "proident amet Excepteur",
                        "value": -25374450
                    },
                    "m": {
                        "unit": "in laboris commodo",
                        "value": 17751634
                    },
                    "ft": {
                        "unit": "eiusmod",
                        "value": -15883622
                    },
                    "in": {
                        "unit": "pariatur",
                        "value": 71040123
                    },
                    "yd": {
                        "unit": "do magna quis elit",
                        "value": -19415235
                    },
                    "mi": {
                        "unit": "culpa",
                        "value": 50293387
                    }
                }
            }
        },
        {
            "type": "product",
            "id": "bcc48a65-483b-4b27-a485-cfc63e759eb8",
            "name": "venia",
            "slug": "nisi qui occaecat",
            "sku": "nostrud",
            "manage_stock": false,
            "description": "consectetur in",
            "price": [
                {
                    "amount": 97503691,
                    "currency": "tempor consequat do",
                    "includes_tax": false
                },
                {
                    "amount": -9041012,
                    "currency": "commodo",
                    "includes_tax": false
                },
                {
                    "amount": -5677198,
                    "currency": "commodo exercitation sint esse",
                    "includes_tax": true
                },
                {
                    "amount": 30080268,
                    "currency": "culpa nisi velit sint occaecat",
                    "includes_tax": true
                },
                {
                    "amount": -27979210,
                    "currency": "aute qui velit",
                    "includes_tax": false
                }
            ],
            "status": "live",
            "commodity_type": "digital",
            "relationships": {
                "variations": {
                    "data": [
                        {
                            "type": "product-variation",
                            "id": "f1a1358a-15d1-4da8-861a-ff742cfa8536"
                        },
                        {
                            "type": "product-variation",
                            "id": "48f8e24d-7725-4a45-8aec-c6cf9a350822"
                        }
                    ]
                },
                "files": {
                    "data": [
                        {
                            "type": "file",
                            "id": "b3b27262-4d06-4740-b3a0-e401f67921bc"
                        },
                        {
                            "type": "file",
                            "id": "3033fc29-bcd3-478c-9567-fb68f107bd25"
                        },
                        {
                            "type": "file",
                            "id": "6841e1c6-104f-4a33-a000-19ee7cb45f95"
                        },
                        {
                            "type": "file",
                            "id": "ce64d9ba-c22e-44e0-8bca-b6c67ac439c6"
                        }
                    ]
                }
            },
            "dimensions": {
                "width": {
                    "cm": {
                        "unit": "dolore occaecat velit",
                        "value": 64253323
                    },
                    "m": {
                        "unit": "ex aliqua ",
                        "value": 76003186
                    },
                    "ft": {
                        "unit": "esse",
                        "value": -75361415
                    },
                    "in": {
                        "unit": "pariatur",
                        "value": 20078478
                    },
                    "yd": {
                        "unit": "culpa elit reprehenderit aliquip est",
                        "value": 47457596
                    },
                    "mi": {
                        "unit": "est",
                        "value": -10691069
                    }
                },
                "height": {
                    "cm": {
                        "unit": "amet eu",
                        "value": -86471183
                    },
                    "m": {
                        "unit": "magna proident amet",
                        "value": -63518024
                    },
                    "ft": {
                        "unit": "voluptate sint",
                        "value": 18923107
                    },
                    "in": {
                        "unit": "eu irure adipisicing ull",
                        "value": 94078857
                    },
                    "yd": {
                        "unit": "nisi nostrud",
                        "value": 9165204
                    },
                    "mi": {
                        "unit": "dolore quis",
                        "value": -73875153
                    }
                },
                "length": {
                    "cm": {
                        "unit": "sint minim",
                        "value": -44724465
                    },
                    "m": {
                        "unit": "culpa consectetur adipisicing",
                        "value": -75404941
                    },
                    "ft": {
                        "unit": "dolore velit exercitation",
                        "value": -78662490
                    },
                    "in": {
                        "unit": "occaecat dolor Duis veniam",
                        "value": -33246900
                    },
                    "yd": {
                        "unit": "nostrud dolore non",
                        "value": 60419916
                    },
                    "mi": {
                        "unit": "adipisicing",
                        "value": 2882594
                    }
                }
            },
            "links": {
                "self": null,
                "first": null,
                "last": null,
                "prev": null,
                "next": null
            }
        }
    ]
}
```

That was fairly heavy but lets now turn our attention to the magic of `modifiers`. Take a look at our [blog post on modifiers](https://moltin.com/blog/2017/06/magic-modifiers/) to see what these simple yet powerful things can achieve.  


