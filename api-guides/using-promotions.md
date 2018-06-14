---
description: How to apply a discount to an entire brand/category
---

# Using Promotions

You need to set up the promotion first and add promotion codes to the promotion id.

#### To follow along with postman, import the below collection.

[https://www.getpostman.com/collections/58d18085e5dd9708cfd5](https://www.getpostman.com/collections/58d18085e5dd9708cfd5)

#### Create a promotion

{% tabs %}
{% tab title="cURL" %}
```javascript
curl -X POST https://api.moltin.com/v2/promotions \
     -H "Authorization: Bearer XXXX" \
     -d $'{
    "data": {
        "type": "promotion",
        "id": "c12ddfe7-3478-4822-b339-4a68ee9926a6",
        "name": "Coolbrandjunepromo",
        "description": "June Promotion",
        "enabled": true,
        "promotion_type": "percent_discount",
        "schema": {
            "currencies": [
                {
                    "currency": "USD",
                    "percentage": 10
                },
                {
                    "currency": "GBP",
                    "percentage": 10
                }
            ]
        },
        "start": "2017-01-13T00:00:00Z",
        "end": "2018-01-13T00:00:00Z",
        "created_at": "2018-05-29T20:02:57.238736345Z",
        "updated_at": "2018-05-29T20:02:57.238736906Z"
    }
}'
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
    "data": {
        "type": "promotion",
        "id": "841cd304-fb2a-47c2-9dfd-a4b6976f1aa3",
        "name": "Coolbrandjunepromo",
        "description": "June Promotion",
        "enabled": true,
        "promotion_type": "percent_discount",
        "schema": {
            "currencies": [
                {
                    "currency": "USD",
                    "percentage": 10
                },
                {
                    "currency": "GBP",
                    "percentage": 10
                }
            ]
        },
        "start": "2017-01-13T00:00:00Z",
        "end": "2018-01-13T00:00:00Z",
        "created_at": "2018-05-30T13:07:06.365225801Z",
        "updated_at": "2018-05-30T13:07:06.365226643Z"
    }
}
```
{% endtab %}
{% endtabs %}

#### Create a custom Flow

{% tabs %}
{% tab title="cURL" %}
```javascript
curl -X POST "https://api.moltin.com/v2/flows" \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
 	"data": {
        "type": "flow",
        "name": "Coolbrandjunepromo",
        "slug": "Coolbrandjunepromo",
        "description": "Store Cool-brand-june-promo",
        "enabled": true
      }
    }'
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
    "data": {
        "id": "a64d905e-7098-4136-8ec7-a0e2b2e2fd70",
        "type": "flow",
        "name": "Coolbrandjunepromo",
        "slug": "Coolbrandjunepromo",
        "description": "Store Cool-brand-june-promo",
        "enabled": true,
        "links": {
            "self": "https://api.moltin.com/v2/flows/a64d905e-7098-4136-8ec7-a0e2b2e2fd70"
        },
        "relationships": {},
        "meta": {
            "timestamps": {
                "created_at": "2018-05-30T13:08:52.015Z",
                "updated_at": "2018-05-30T13:08:52.015Z"
            }
        }
    }
}
```
{% endtab %}
{% endtabs %}

#### Create a Field

Now you'll need to create a Flow Field so you can keep a record of a the promotion and tie it to the brand. _This field should be marked as optional._

{% tabs %}
{% tab title="cURL" %}
```javascript
curl -X POST "https://api.moltin.com/v2/fields" \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
       "data": {
        "type": "field",
        "name": "Brands",
        "slug": "brands",
        "field_type": "relationship",
        "validation_rules": [{
          "type": "one-to-many",
          "to": "brand"
        }],
        "description": "Promotion brands",
        "unique": false,
        "enabled": true,
        "required": false,
        "relationships": {
          "flow": {
            "data": {
              "type": "flow",
              "id": "e679882b-79fd-40f0-a538-4f8580b73125"
            }
          }
        }
      }
}'
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
    "id": "08a4191b-73fb-4bc4-a7ac-290e833542c2",
    "type": "field",
    "field_type": "relationship",
    "slug": "brands",
    "name": "Brands",
    "description": "Promotion brands",
    "required": true,
    "unique": false,
    "default": null,
    "enabled": true,
    "validation_rules": [
        {
          "type": "one-to-many",
          "to": "brand"
        }
    ],
    "order": null,
    "links": {
               "self": "https://api.moltin.com/v2/flows/e679882b-79fd-40f0-a538-4f8580b73125/fields/08a4191b-73fb-4bc4-a7ac-290e833542c2"
           },
      "relationships": {
            "flow": {
                   "data": {
                        "id": "e679882b-79fd-40f0-a538-4f8580b73125",
                        "type": "flow"
                            }
                    }
                },
    "meta": {
        "timestamps": {
            "created_at": "2018-05-30T01:44:41.478Z",
            "updated_at": "2018-05-30T01:44:41.478Z"
            }
    }
}
```
{% endtab %}
{% endtabs %}

#### Create an empty Entry

Before we can associate brand\(s\) to promotions, we must first create an empty entry that will next be used to relate the brand\(s\) to the promotion entry.

{% tabs %}
{% tab title="cURL" %}
```javascript
curl -X POST "https://api.moltin.com/v2/flows/Coolbrandjunepromo/entries" \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
      "data": {
        "type": "entry"
      }
    }'
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
    "data": {
        "id": "71b33617-94ba-412b-90c2-fd7b23498cc4",
        "type": "entry",
        "meta": {
            "timestamps": {
                "created_at": "2018-05-30T13:14:15.633Z",
                "updated_at": "2018-05-30T13:14:15.633Z"
            }
        },
        "links": {
            "self": "https://api.moltin.com/v2/flows/Coolbrandjunepromo/entries/71b33617-94ba-412b-90c2-fd7b23498cc4"
        }
    }
}
```
{% endtab %}
{% endtabs %}

#### Relate a brand to promotion

{% tabs %}
{% tab title="cURL" %}
```javascript
curl -X POST "https://api.moltin.com/v2/flows/Coolbrandjunepromo/entries" \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
       "data": [
          {
            "type": "brands",
            "id": "0d28e5b6-f615-4e1c-b038-275cfde97927"
          }
        ]
      }
```
{% endtab %}

{% tab title="Response" %}
```javascript
{
  "data": [
    {
      "type": "brands",
      "id": "0d28e5b6-f615-4e1c-b038-275cfde97927"
    }
  ]
}
```
{% endtab %}
{% endtabs %}

Now when you pull down brands you will be able to see if the brand has a promotion applied to it.  This will give you everything you need to then apply the discount on the client side.

