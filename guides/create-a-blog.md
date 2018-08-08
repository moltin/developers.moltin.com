# Create a Blog

Use the Moltin API to extend your project's functionality by creating a blog object to store blog content you can surface on your website. You will be creating two flow objects. One will be used to store the actual blog content,  the other will include an overview of all the blogs grouped.

## 1. Get your access token

You will need to get a [`client_credentials`](https://docs.moltin.com/basics/authentication/client-credential-token) access token to follow along making the API requests outlined below.

```bash
curl -X "POST" "https://api.moltin.com/oauth/access_token" \
     -d "client_id=XXXX" \
     -d "client_secret=XXXX" \
     -d "grant_type=client_credentials"
```

##  2. Create a new custom Flow to hold Blog content {#2-create-a-new-custom-flow}

Using the Moltin API, go ahead and create a flow for "post".  This will be used to store blog content objects.

```javascript
curl -X POST "https://api.moltin.com/v2/flows" \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
      "data": {
        "type": "flow",
        "name": "Posts",
        "slug": "posts",
        "description": "Stores content that will be used for a blog",
        "enabled": true
      }
    }'
```

{% hint style="warning" %}
Make sure to take note of the Flow ID returned, you'll need this below as `post_FLOW_ID`.
{% endhint %}

## 3. Create Flow fields

The Flow fields will be returned when you call the blog object. In the example below, we will be creating a field where a blog content object will be stored.

{% hint style="info" %}
Replace Flow ID with the ID that was generated above.
{% endhint %}

```bash
//Title Field
curl -X POST https://api.moltin.com/v2/fields \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "field",
          "name": "blogTitle",
          "slug": "blogTitle",
          "description": "This is going to return the blog title",
          "unique": true,
          "enabled": true,
          "field_type": "string",
          "required": false,
          "relationships": {
            "flow": {
              "data": {
                "type": "flow",
                "id": "post_FLOW_ID"
                }
            }
          }
        }
     }'
     
//Date Field
curl -X POST https://api.moltin.com/v2/fields \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "field",
          "name": "blogDate",
          "slug": "blogDate",
          "description": "This is going to return the blog date",
          "unique": true,
          "enabled": true,
          "field_type": "string",
          "required": false,
          "relationships": {
            "flow": {
              "data": {
                "type": "flow",
                "id": "post_FLOW_ID"
                }
            }
          }
        }
     }'

//Content Field
curl -X POST https://api.moltin.com/v2/fields \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "field",
          "name": "blogContent",
          "slug": "blogContent",
          "description": "This is going to return the blog content",
          "unique": "true",
          "enabled": "true",
          "field_type": "string",
          "required": "false",
          "relationships": {
            "flow": {
              "data": {
                "type": "flow",
                "id": "post_FLOW_ID"
                }
            }
          }
        }
     }
```

## 4. Create entry's for a Blog

With our custom Flow configured, we next create an empty Entry so we can blog content objects.

```bash
//Title Entry
curl -X POST https://api.moltin.com/v2/flows/posts/entries \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
       "data": {
         "type": "entry",
         "blogTitle": "Creating a Blog in Moltin",
         "blogDate": "1532371596",
         "blogBody": "Make any touchpoint transactional -  from traditional websites, to social platforms, content, IoT and more. With Moltin, create unique, customer-centric shopping experiences for now and the future.  EVEN BLOGSSSS"
       }
     }'
```

{% hint style="warning" %}
Note the slug is case sensitive, Blog.
{% endhint %}

## 5. Fetching Blog content to display {#2-create-a-new-custom-flow}

Using the Moltin API, fetch the posts flow which will now contain the blog post above we just created.

```javascript
curl -X GET "https://api.moltin.com/v2/flows/posts/entries" \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
```

{% tabs %}
{% tab title="Response" %}
```javascript
{
    "data": [
        {
            "id": "efad5cf7-62cb-4a30-af46-ed1ff5cd8628",
            "type": "entry",
            "postTitle": "Creating a Blog in Moltin",
            "postDate": "1532371596",
            "postBody": "Make any touchpoint transactional -  from traditional websites, to social platforms, content, IoT and more. With Moltin, create unique, customer-centric shopping experiences for now and the future.  EVEN BLOGSSSS",
            "meta": {
                "timestamps": {
                    "created_at": "2018-07-23T18:49:54.274Z",
                    "updated_at": "2018-07-23T18:49:54.274Z"
                }
            },
            "links": {
                "self": "https://api.moltin.com/v2/flows/posts/entries/efad5cf7-62cb-4a30-af46-ed1ff5cd8628"
            }
        }
    ]
}
```
{% endtab %}
{% endtabs %}

