# Setup

The **Setup** section will take you step by step through basic project configuration.

Basic project configuration happens at the Moltin [account](https://accounts.moltin.com/stores/1795057077903687859) and [dashboard](https://dashboard.moltin.com/app/orders/orders?dir=desc) level. You'll need to **register as a user** to get access to both.

* Use **account** to add projects and manage users \(first project is added as part of an account onboarding process\).
* Use **dashboard** to manage your projects and project settings.

## Sign up as a user

Use the [Moltin website](https://moltin.com/) to sign up for an account and add your first project. Once you've created an account and added your project\(s\), you'll get access to dashboard where you can manage your existing projects. From now on, you can sign in to your dashboard and account separately.

## Manage your account

Once you are a registered user and have a [Moltin account](https://accounts.moltin.com/), you can create multiple projects \(also known as stores\), and invite team members to collaborate.

### Add projects {#add-projects}

Use your [Moltin account](https://accounts.moltin.com/) to add as many projects as you need. If you need to delete any project, contact [Moltin support](https://support.moltin.com/hc/en-us), and we will do it for you. For more details on managing projects, see the [**Manage your projects**](setup.md#manage-your-projects) section below.

### Invite team members {#invite-team}

Invite team members to join specific projects via your [Moltin account](https://accounts.moltin.com/). Each team member will have full access to your project via the API and [dashboard](https://dashboard.moltin.com/app).

Every team member has their own unique API `client_id` and `client_secret`. This is really helpful if you invite external developers to work on your project, you can easily revoke their access at anytime.

You can invite users that are already registered with Moltin, but it is not a requirement. An invitation for unregistered users additionally includes the link to the registration form.

### Delete team members {#manage-your-team}

Delete existing users through your [Moltin account](https://accounts.moltin.com/). Deleting users is permanent. You can re-add deleted users who will receive a new set of credentials \(`client_id` and `client_secret`\).

{% hint style="info" %}
#### Project owners

Each user can invite and delete other users; however, normal users can't delete the project owner. The project owner user type is identified by a star icon appearing next to their username.
{% endhint %}

## Manage your projects

Projects \(aka stores\) are managed through the [Moltin dashboard](https://dashboard.moltin.com/). If you have multiple projects, switch between them using the dashboard's store switcher.

Use the dashboard to manage projects. In detail you can:

* Add or delete products.
* Organise products into catalogs, brands and categories.
* Manage project settings, such as currency, payment method, etc.
* Manage your inventory.
* Assign and manage promotions.

### Project settings {#project-settings}

Moltin's API provides the ability to to configure global settings across your project at the API-level only. See: [Settings](https://docs.moltin.com/advanced/settings) for more details.

## Get your API Keys

Access to Moltin's API is protected by [authentication tokens](https://docs.moltin.com/basics/authentication). Tokens are generated using API keys and can be found via both: [Moltin account](https://accounts.moltin.com/dashboard) and  [Moltin dashboard](https://dashboard.moltin.com). 

Every user is provided with `client_id` and `client_secret` keys. These API keys are per user per project, and are used to generate two types of [authentication tokens](https://docs.moltin.com/basics/authentication).

{% hint style="warning" %}
You should never share the `client_secret` or use it client side.
{% endhint %}

Now that you are all set up with your project, you can make your **first API request**!

