# API Keys

Every user is provided `client_id` and `client_secret` keys. These API keys are per user per project and are used to generate two types of authentication `Bearer` tokens. You can find out more about this in Security &gt; Authentication.

It’s import you keep the `client_secret` safe as this permits destructive actions. We recommend you create multiple users for development and production so API keys can easily be revoked later.

{% hint style="warning" %}
**You should never share the `client_secret`**
{% endhint %}

