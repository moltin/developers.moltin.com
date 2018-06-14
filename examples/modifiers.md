---
description: >-
  There are several types of modifier available within the platform. Each type
  has an effect on one property of the base product and this is the mechanism
  that augments details for the child-products.
---

# Modifiers

My work mates are a dreadful bunch. Yeah, people often state how great their colleagues are, but we all know they’re trying to convince themselves that the people they work with are somehow ‘OK’.

I got sent a list for blog bingo — words to insert into a blog post to challenge creativity in a ‘fun’ way. The words in that list are designed to be near impossible to get into a post without some obvious contrived effort…

* Conduit
* Discombobulated
* Flange
* Cricket
* Defibrillate
* Boost

There. That’s the list; so technically I have ‘won’ — in-your-face work-mates.

Now, in the interests of team dynamics and what-not, I suppose I should still play the game — so feel free to check the words off as you find them. \(Feel cheapened now.\) **CONDUIT** \(&lt; there’s one\).

So in the previous post about [Moltin’s variations](https://moltin.com/blog/2017/06/introducing-variations-options-modifiers/), we looked at building up an entire variation in order to facilitate child product generation. It’s reasonably in depth but a quick recap…

Variations on the Moltin platform are containers for options which are, in turn, containers for modifiers. The system allows arbitrary numbers of `options` to be applied to `variations`, and an arbitrary number of these can be applied to a base product. The combinations of `options` are then applied to build all the possible child-products.

The [variations blog post](https://moltin.com/blog/2017/06/introducing-variations-options-modifiers/) details how these structures are built up. It is a little involved, so I do hope that if you’ve read the post you weren’t left feeling completely discombobulated. Since creating `modifiers` has already been covered in that post, we’ll concentrate on: the types of modifier available, what effect they have on the resulting child-products and how they achieve the _magic_.

### Modifier types {#modifier-types}

There are several types of modifier available within the platform. Each type has an effect on one property of the base product and this is the mechanism that augments details for the child-products.

| Modifier Type | Effect |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| name\_equals | Overwrite in its entirety the name of the base product with what is specified as the modifier value. |
| name\_append | Append the string specified in modifier value to the base product name. |
| name\_prepend | Prepend the string specified in modifier value to the base product name. |
| description\_equals | Overwrite the description of the product. |
| description\_append | Append a string to the product description. |
| description\_prepend | Prepend a string to product description. |
| commoditytype | Set the commodity type. |
| price\_increment | Increase the price of the product. |
| price\_decrement | Decrease the price of the product. |
| price\_equals | Set the price of the product - cannot be further modified. |
| slug\_equals | Set the product slug. |
| slug\_append | Append a string to the product slug. |
| slug\_prepend | Prepend a string to the product slug. |
| sku\_equals | Set the product SKU. |
| sku\_append | Append a string to the product SKU. |
| sku\_prepend | Prepend a string to the product SKU. |
| sku\_builder | Set part of the product SKU. |
| status | Set the status of the product. |

You will note that these correspond to the properties of a product. It should not surprise you, then, to hear that the values you supply to these `modifiers` match that of the properties of the base product. Anything else would most certainly not be cricket!

### Modifier values {#modifier-values}

```javascript
{
     "data": {
         type": "product-modifier",
         "modifier_type": TYPE,
         "value": VALUE
     }
 } 
```



The type, obviously, is one listed above to target the property you wish to modify. Below we see the values they accept.

| Modifier Type | Data Type |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| name\_equals  name\_append  name\_prepend | string |
| description\_equals  description\_append  description\_prepend | string |
| commoditytype | string |
| price\_increment  price\_decrement  price\_equals | price collection |
| slug\_equals  slug\_append  slug\_prepend | string |
| sku\_equals  sku\_append  sku\_prepend | string |
| sku\_builder | seek/set object |
| status | string |

#### String types {#string-types}

```javascript
{
     "data": {
         type": "product-modifier",
         "modifier_type": "name_append",
         "value": " - Flexible"
     }
 }

```

If this `name_append` modifier were applied to a product with the name `Flange` — the resulting child product’s product name would be `Flange - Flexible` — not sure you’d ever want a flexible flange but it’s an example…

Short and sweet, but I hope this is sufficient to describe how all the modifiers that accept string types function. These modifiers behave in a compound manner. If you have another modifier that appended to the product name on a different variation option combination then it would append that `string` to `Flange - Flexible` to become `Flange - Flexible STRING`.

#### Price collection types {#price-collection-types}

The `price` modifiers simply target the price of the base product. A price collection value looks like:  


```javascript
{
    "data": {
        "type": "product-modifier",
        "modifier_type": "price_increment",
        "value": [
            {
                "currency": "GBP",
                "amount": 1000,
                "includes_tax": true
            },
            {
                "currency": "USD",
                "amount": 1500,
                "includes_tax": false
            },
            {
                "currency": "EUR",
                "amount": 1200,
                "includes_tax": true
            },
            {
                "currency": "AUD",
                "amount": 2000,
                "includes_tax": false
            }
        ]
    }
}
```

So a price increment modifier as defined above would increase the price of a product by:

* £10 GBP
* $15 USD
* €12 EUR
* $20 AUD

_Only_ if those currencies were set on the product that the modifier is applied to. If you only set a product’s price in GBP and EUR then the above modifier would do _nothing_ with the USD and AUD values - this is important as you might expect a `price_equals` modifier to set those values - if the currency doesn’t exist on the product already, the modification for for that currency will _not_ be reflected in the child product generated.

#### Seek/set type {#seekset-type}

This modifier type is quite powerful. Currently only applicable to SKU and the `sku_builder` modifier, this tool allows you to build up product properties \(well just SKUs right now\) following some rules. It was inspired by problems faced in managing SKUs and it is in that spirit we present this modifier type to help boost your SKU management kungfu.

```javascript
{
    "data": {
        "type": "product-modifier",
        "modifier_type": "sku_builder",
        "value": {
            "seek": "TOKEN",
            "set": "XXX"
        }
    }
}
```

This is the anatomy of a `sku_builder` modifier. The power in this modifier is based on the token. When you declare an SKU for your base product you can tokenize sections of the value. Let’s use an example where a base product has a size and a color variation like that in the [variations blog post](https://moltin.com/blog/2017/06/introducing-variations-options-modifiers/). If we define our base product with an SKU of `P1-{SIZE}-{COLOUR}` then we can attach an `sku_builder` to each option. The size options small, medium, large and the color options red, blue, and the ugly one.

```javascript
{
    "data": {
        "type": "product-modifier",
        "modifier_type": "sku_builder",
        "value": {
            "seek": "SIZE",
            "set": "SML"
        }
    }
}
```

```javascript
{
    "data": {
        "type": "product-modifier",
        "modifier_type": "sku_builder",
        "value": {
            "seek": "COLOR",
            "set": "RD"
        }
    }
}
```

Above we have the `sku_builder` modifiers for the small size option and the red color option. The generated SKU after application of the `modifiers` through the `variation options` would be: `P1-SML-RD`.

_Note_ — tokens in a string are bound by `{}`, you do not add these to the seek property of the modifier’s value - the system handles this itself.

I hope you can see the power available to you here. Managing SKUs can leave some needing to defibrillate just to stay alive after a frustrating session trying to create unique values. The builder system provides you with an elegant method of applying a simple ruleset to your SKU generation.

### Product building {#product-building}

I hope these posts have been useful and provided you with insight into the Moltin modifiers system. While the examples here uses t-shirts, the system isn’t limited to clothing, but can also be used on any type of product you’d like to build. We think this system is very flexible and we hope you agree.

Go and build your catalog using these tools and we are confident you’ll find variant products easy to administer and the efforts needed to populate your product listings reduced!

