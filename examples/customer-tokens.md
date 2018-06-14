---
description: >-
  Authenticate your customer’s identity by exchanging an email address and
  password for a customer token.  Read more here
  https://moltin.com/blog/2017/11/customer-tokens/
---

# Customer Tokens

In the first of our incremental builds on top of that functionality, we have now added the ability for those users to manage their addresses. This allows you to present them as part of your checkout flow and avoids the need for authenticated users \(as opposed to anonymous purchasers\) to duplicate information that largely remains constant and static.

Note: If you authenticate with a `client_credentials` grant type, you do not need to get a customer token.

When you make a request for a customer token, you get a response like the following:  


```javascript
{
  "data": {
    "type": "token",
    "id": "36f05940-0d38-411a-8909-3aea58bc1f09",
    "customer_id": "78cc0486-bbdf-491b-a0a2-722383b6288b",
    "token": "eyJhbGciOiAiSFMyNTYiLCAidHlwIjogIkpXVCJ9.eyJzdWIiOiI3OWNjMDQ4Ni1iYmRmLTQ5MWItYTBhMi03MjIzODNiNjI4OGIiLCJuYW1lIjoiUm9uIFN3YW5zb24iLCJleHAiOjE1MTA2ODQ.ea948e346d0683803aa4a2c09441bcbf7c79b",
    "expires": 1510684200
  }
}
```

You’ll need two bits of content from the response for your next requests - `data.customer_id` and `data.token`.

Using that token, you can now request a list of that addresses for that customer:

```javascript
curl "https://api.moltin.com/v2/customers/78cc0486-bbdf-491b-a0a2-722383b6288b/addresses" \
     -H 'X-Moltin-Customer-Token: eyJhbGciOiAiSFMyNTYiLCAidHlwIjogIkpXVCJ9.eyJzdWIiOiI3OWNjMDQ4Ni1iYmRmLTQ5MWItYTBhMi03MjIzODNiNjI4OGIiLCJuYW1lIjoiUm9uIFN3YW5zb24iLCJleHAiOjE1MTA2ODQ.ea948e346d0683803aa4a2c09441bcbf7c79b' \
     -H 'Authorization: Bearer: XXXX'
```

Obviously, we don’t have any yet, so let’s create one:  


```javascript
curl -X "POST" "https://api.moltin.com/v2/customers/78cc0486-bbdf-491b-a0a2-722383b6288b/addresses" \
     -H 'X-Moltin-Customer-Token: eyJhbGciOiAiSFMyNTYiLCAidHlwIjogIkpXVCJ9.eyJzdWIiOiI3OWNjMDQ4Ni1iYmRmLTQ5MWItYTBhMi03MjIzODNiNjI4OGIiLCJuYW1lIjoiUm9uIFN3YW5zb24iLCJleHAiOjE1MTA2ODQ.ea948e346d0683803aa4a2c09441bcbf7c79b' \
     -H 'Authorization: Bearer: XXXX' \
     -H 'Content-Type: application/json; charset=utf-8' \
     -d $'{
  "data": {
    "type": "address",
    "first_name": "Ron",
    "last_name": "Swanson",
    "name": "Home",
    "instructions": "Leave behind the bins",
    "company_name": "Ron Swanson Enterprises",
    "line_1": "1 Sunny Street",
    "line_2": "",
    "city": "Sunny Town",
    "county": "Sunnyville",
    "post_code": "SU33 1YY",
    "country": "US"
  }
}'
```

Now, when we call the `https://api.moltin.com/v2/customers/78cc0486-bbdf-491k-a0a2-722383b6288b/addresses` endpoint using the same customer token, we will see this address in the list.

```javascript
{
  "data": [
    {
      "id": "a3cbe1e6-d362-466a-986b-5035f04dbbcd",
      "type": "address",
      "name": "Home",
      "first_name": "Ron",
      "last_name": "Swanson",
      "company_name": "Ron Swanson Enterprises",
      "line_1": "1 Sunny Street",
      "line_2": "",
      "city": "Sunny Town",
      "post_code": "SU33 1YY",
      "county": "Sunnyville",
      "country": "US",
      "instructions": "Leave behind the bins.",
      "links": {
        "self": "https://api.moltin.com/v2/addresses/a3cbe1e6-d362-466a-986b-5035f012bbcd"
      },
      "meta": {
        "timestamps": {
          "created_at": "2017-12-01T12:13:23.513Z",
          "updated_at": "2017-12-01T12:13:23.513Z"
        }
      }
    }
  ]
}
```

You can update and delete addresses on behalf of a customer using the token so you could provide an interface for their address profiles if desired. Otherwise, you could save them the hassle of adding their address in again by providing it in a drop down or selecting it from a list next time they check out.

