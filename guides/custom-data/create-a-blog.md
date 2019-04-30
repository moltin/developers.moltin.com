---
description: >-
  If you want to use a single platform for multiple solutions, Moltin API gives
  you the ability to use custom data to create a blog and manage your content
  with some light configuration.
---

# Create a Blog with lightweight CMS

### Prerequisites

* Moltin dashboard \(you'll need Client ID and Client Secret of your store\).
* Basic understanding of object-oriented programming or JavaScript.

### How to expand Moltin API to create a blog

Use Flow API \(custom data\) to create a new resource: a blog object to store blog content you can surface on your website.

* See: [Custom Data](./) to learn about Flows.

### Summary of steps required

1. Create a Flow that will contain your blog object.
2. Create Fields to create blog's building blocks \(date, title, blog post entry, fields, etc.\).
3. Create Entries to store the actual content \(blog posts\).
4. Fetch blog content to display and render it according to your needs on your blog site.

### Step-by-step walkthrough

### 1. Get your access token

Get a [`client_credentials`](https://docs.moltin.com/basics/authentication/client-credential-token) access token to follow along making the API requests outlined below.

```bash
curl -X "POST" "https://api.moltin.com/oauth/access_token" \
     -d "client_id=XXXX" \
     -d "client_secret=XXXX" \
     -d "grant_type=client_credentials"
```

### 2. Create a new custom Flow 

Create a custom \(non-core\) [Flow](./). This Flow is going to power a blog website, and used to store blog content objects.

{% tabs %}
{% tab title="cURL" %}
```bash
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
{% endtab %}

{% tab title="Response" %}
```bash
 "data": {
        "id": "e051bb18-b526-49dc-a154-63659505d0f6",
        "type": "flow",
        "name": "Blog_Posts",
        "slug": "blog_posts",
        "description": "Stores content that will be used for a blog",
        "enabled": true,
        "links": {
            "self": "https://api.moltin.com/v2/flows/e051bb18-b526-49dc-a154-63659505d0f6"
        },
        "relationships": {},
        "meta": {
            "timestamps": {
                "created_at": "2018-08-24T12:26:57.518Z",
                "updated_at": "2018-08-24T12:26:57.518Z"
            }
        }
    }
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
Make sure to take note of the Flow ID returned \(the `id` field in the response\), you'll need this to create relationship between the Flow and its Fields.
{% endhint %}

### 3. Create Fields

Fields will be returned when you call the blog object. Repeat this for every blog feature you'd like to include, e.g. blog title, blog date, blog post content, etc. The example below shows how to create fields to store basic blog detail: title, date and content. 

Each Field must be passed separately.

{% hint style="info" %}
Populate the `data.relationships.flow.id` field with the Flow ID generated as part of the POST \(create\) Flow response.
{% endhint %}

{% tabs %}
{% tab title="Create Title Field" %}
#### Request

```bash
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
          "required": true,
          "relationships": {
            "flow": {
              "data": {
                "type": "flow",
                "id": "XXXX"
                }
            }
          }
        }
     }'
```

#### Response

```bash
{
    "data": {
        "id": "59b62de4-7a81-4e4f-a659-93fb21d4a5b3",
        "type": "field",
        "field_type": "string",
        "slug": "blogTitle",
        "name": "blogTitle",
        "description": "This is going to return the blog title",
        "required": true,
        "unique": true,
        "default": null,
        "enabled": true,
        "validation_rules": [],
        "order": null,
        "links": {
            "self": "https://api.moltin.com/v2/flows/e051bb18-b526-49dc-a154-63659505d0f6/fields/59b62de4-7a81-4e4f-a659-93fb21d4a5b3"
        },
        "relationships": {
            "flow": {
                "data": {
                    "id": "e051bb18-b526-49dc-a154-63659505d0f6",
                    "type": "flow"
                }
            }
        },
        "meta": {
            "timestamps": {
                "created_at": "2018-08-24T12:49:48.662Z",
                "updated_at": "2018-08-24T12:49:48.662Z"
            }
        }
    }
}
```
{% endtab %}

{% tab title="Create Date Field" %}
#### Request

```bash
curl -X POST https://api.moltin.com/v2/fields \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
        "data": {
          "type": "field",
          "name": "blogDate",
          "slug": "blogDate",
          "description": "This is going to return the blog date",
          "unique": false,
          "enabled": true,
          "field_type": "string",
          "required": true,
          "relationships": {
            "flow": {
              "data": {
                "type": "flow",
                "id": "XXXX"
                }
            }
          }
        }
     }'
```

#### Response

```bash
{
    "data": {
        "id": "a57e6e7e-f581-4b1e-80db-7790891de870",
        "type": "field",
        "field_type": "string",
        "slug": "blogDate",
        "name": "blogDate",
        "description": "This is going to return the blog date",
        "required": false,
        "unique": false,
        "default": null,
        "enabled": true,
        "validation_rules": [],
        "order": null,
        "links": {
            "self": "https://api.moltin.com/v2/flows/e051bb18-b526-49dc-a154-63659505d0f6/fields/a57e6e7e-f581-4b1e-80db-7790891de870"
        },
        "relationships": {
            "flow": {
                "data": {
                    "id": "e051bb18-b526-49dc-a154-63659505d0f6",
                    "type": "flow"
                }
            }
        },
        "meta": {
            "timestamps": {
                "created_at": "2018-08-24T13:11:56.233Z",
                "updated_at": "2018-08-24T13:11:56.233Z"
            }
        }
    }
}
```
{% endtab %}

{% tab title="Create Content Field" %}
#### Request

```bash
{
	"data": {
		"type": "field",
		"name": "blogContent",
		"slug": "blogContent",
		"description": "This is going to return the blog content",
		"unique": false,
		"enabled": true,
		"field_type": "string",
		"required": true,
		"relationships": {
			"flow": {
				"data": {
					"type": "flow",
					"id": "XXXX"
				}
			}
		}
	}
}
```

#### Response

```bash
{
    "data": {
        "id": "695ef16e-468c-44c6-aad8-a01342f7604e",
        "type": "field",
        "field_type": "string",
        "slug": "blogContent",
        "name": "blogContent",
        "description": "This is going to return the blog content",
        "required": true,
        "unique": false,
        "default": null,
        "enabled": true,
        "validation_rules": [],
        "order": null,
        "links": {
            "self": "https://api.moltin.com/v2/flows/e051bb18-b526-49dc-a154-63659505d0f6/fields/695ef16e-468c-44c6-aad8-a01342f7604e"
        },
        "relationships": {
            "flow": {
                "data": {
                    "id": "e051bb18-b526-49dc-a154-63659505d0f6",
                    "type": "flow"
                }
            }
        },
        "meta": {
            "timestamps": {
                "created_at": "2018-08-24T13:22:31.694Z",
                "updated_at": "2018-08-24T13:22:31.694Z"
            }
        }
    }
}
```
{% endtab %}
{% endtabs %}

### 4. Create Entries for a Blog

Entries represent the actual content each Field will contain. Create an Entry for every Field added. In this example, the Title, Date and Content Fields will be a part of the Entry created, as they were all flagged as required.

### Using slugs

Slug forms the end part of the URL. Use it to create human-readable and search-engine-friendly URIs for your blog. 

{% hint style="warning" %}
Note that the slug used in the URL is case sensitive.
{% endhint %}

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST https://api.moltin.com/v2/flows/{Flow:slug}/entries \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
       "data": {
         "type": "entry",
         "blogTitle": "Creating a Blog",
         "blogDate": "8.24.2018",
         "blogContent": "Learn how to create blogs with the Moltin API"
       }
     }'
```
{% endtab %}

{% tab title="Response" %}
```bash
{
    "data": {
        "id": "25dd4193-4346-4c9b-bbc9-6b15d2bf7fb9",
        "type": "entry",
        "blogTitle": "Creating a Blog",
        "blogDate": "8.25.2018",
        "blogContent": "Learn how to create blogs with the Moltin API",
        "meta": {
            "timestamps": {
                "created_at": "2018-08-24T14:56:47.332Z",
                "updated_at": "2018-08-24T14:56:47.332Z"
            }
        },
        "links": {
            "self": "https://api.moltin.com/v2/flows/blog_posts/entries/25dd4193-4346-4c9b-bbc9-6b15d2bf7fb9"
        }
    }
}
```
{% endtab %}
{% endtabs %}

### 5. Create relationships to organize your data

Create Fields related to a Flow to adapt a data model to how you wish to present it on the frontend, e.g. posts per author, posts per category, etc.

To create a relationship, take the Flow you want to relate your Fields to, and use its slug in the URL of the request. Then, specify which Field you wish to relate to in the request body, and repeat it for every Field you wish to relate to this Flow.

When fetching the Flow, it will list all Fields that are related to it.

{% tabs %}
{% tab title="cURL" %}
**1.Create a Flow**

```bash
curl -X POST https://api.moltin.com/v2/flows \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
  "data": {
    "type": "flow",
    "name": "Joannas blog posts",
    "slug": "blog_joanna",
    "description": "stores Joannas blog posts",
    "enabled": true
  }
}'
```

**2. Create Related Field**

```bash
curl -X POST https://api.moltin.com/v2/fields \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
     -d $'{
  "data": {
    "type": "field",
    "name": "blog_title_3",
    "slug": "blog_title_3",
    "field_type": "string",
    "description": "Joannas third blog post",
    "required": true,
    "unique": true,
    "default": 0,
    "enabled": true,
    "order": 1,
    "relationships": {
        "flow": {
            "data": {
                "type": "flow",
                "id": "a7b53c11-ece3-4c11-b0b6-05568fa636ad"
            }
        }
    }
  }
}'
```

**3. Fetch the Flow with all Related Entries**

```bash
curl -X GET https://api.moltin.com/v2/flows/{Flow:id} \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
```
{% endtab %}

{% tab title="Response" %}
**1.Created Flow**

```bash
{
    "data": {
        "id": "a7b53c11-ece3-4c11-b0b6-05568fa636ad",
        "type": "flow",
        "name": "Joanna's blog posts",
        "slug": "blog_joanna",
        "description": "stores Joanna's blog posts",
        "enabled": true,
        "links": {
            "self": "https://api.moltin.com/v2/flows/a7b53c11-ece3-4c11-b0b6-05568fa636ad"
        },
        "relationships": {},
        "meta": {
            "timestamps": {
                "created_at": "2018-10-17T15:12:21.276Z",
                "updated_at": "2018-10-17T15:12:21.276Z"
            }
        }
    }
}
```

**2.Created Related Field**

```bash
{
    "data": {
        "id": "3901e3b5-edd2-4123-b464-a01cc4c554ee",
        "type": "field",
        "field_type": "string",
        "slug": "blog_title_3",
        "name": "blog_title_3",
        "description": "Joanna's third blog post",
        "required": true,
        "unique": true,
        "default": null,
        "enabled": true,
        "order": 1,
        "omit_null": false,
        "validation_rules": [],
        "links": {
            "self": "https://api.moltin.com/v2/flows/a7b53c11-ece3-4c11-b0b6-05568fa636ad/fields/3901e3b5-edd2-4123-b464-a01cc4c554ee"
        },
        "relationships": {
            "flow": {
                "data": {
                    "id": "a7b53c11-ece3-4c11-b0b6-05568fa636ad",
                    "type": "flow"
                }
            }
        },
        "meta": {
            "timestamps": {
                "created_at": "2018-10-17T15:13:47.411Z",
                "updated_at": "2018-10-17T15:13:47.411Z"
            }
        }
    }
}
```

**3.Fetched Flow with all Related Fields listed by their unique IDs**

```bash
{
    "data": {
        "id": "a7b53c11-ece3-4c11-b0b6-05568fa636ad",
        "type": "flow",
        "name": "Joanna's blog posts",
        "slug": "blog_joanna",
        "description": "stores Joanna's blog posts",
        "enabled": true,
        "links": {
            "self": "https://api.moltin.com/v2/flows/a7b53c11-ece3-4c11-b0b6-05568fa636ad"
        },
        "relationships": {
            "fields": {
                "data": [
                    {
                        "id": "93c4413a-01b8-4000-8aca-ae338518a078",
                        "type": "field"
                    },
                    {
                        "id": "fa1f5ed0-2cea-4fcd-b4d2-ced85fbc231b",
                        "type": "field"
                    },
                    {
                        "id": "3901e3b5-edd2-4123-b464-a01cc4c554ee",
                        "type": "field"
                    }
                ]
            }
        },
        "meta": {
            "timestamps": {
                "created_at": "2018-10-17T15:12:21.276Z",
                "updated_at": "2018-10-17T15:12:21.276Z"
            }
        }
    }
}
```
{% endtab %}
{% endtabs %}

### 6. Fetch Blog content to display

Using the Moltin API, fetch the blog Flow which will now contain the blog post created above.

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X GET "https://api.moltin.com/v2/flows/posts/entries" \
     -H "Authorization: XXXX" \
     -H "Content-Type: application/json" \
```
{% endtab %}

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

### Expected outcome

Your blog is now surfaced on your website. The data you've fetched is in JSON format, and you can render it according to your needs directly on your blog site. You can now expand it by adding photo galleries, comments, reviews, etc.



