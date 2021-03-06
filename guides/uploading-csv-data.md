# Import Catalog Data

## Overview

Moltin flexible API allows you to quickly and easily import data. CSV files are a very common way to extract and share data. All endpoint are read write and update, meaning you can import products, customers, and orders. 

In this guide we will look at importing product data and establishing relationships in the API to have a functional product catalog organized programatically.

### Setup Summary

1. Prepare a file with data and setup a project with Moltin.
2. Install the sample script from GitHub.
3. Review the code in the sample script. The code can be customized according to your needs.  \(The moltin objects adjusted to your data\)
4. Review the commands that control how the script is run.
5. Upload new/updated data programatically.

### Prerequisites

Before you proceed with the import, you'll need:

* A file that contains the product data you wish to import into Moltin \(the file needs to match [Moltin standards](../organize-your-data-with-moltin.md)\),
* [Moltin's project](../getting-started/setup.md) \(aka store\).

### Scenario setup

In this guide, we will look to implement a CSV file of products that have main images, Category, Collection and Brand.

{% hint style="info" %}
To use another format adjust to the file format you need `delimiter: ','` \([http://csv.adaltas.com/parse/](http://csv.adaltas.com/parse/)\)
{% endhint %}

1. Download the sample Shopify data we would use to import into Moltin. Use the data file to follow along with the guide.

{% file src="../.gitbook/assets/apparel \(2\).csv" caption="Test data" %}

2. Install the script to upload the data you've created to Moltin. 

```javascript
git clone https://github.com/moltin-examples/import-catalogue-data
```

 3. Use this command to install the packages the script above uses.

```text
//In terminal navigate to the folder where you cloned the repo
npm install
```

Once the script and the command run successfully, your Moltin project will have a catalog of data you've specified in the data file.

### Organize your data

It is recommended to review your data before the upload, so that it matches Moltin standards. If you're using the sample data file, skip this step.

### Adjust the data model

Adjust the data model to match your CSV headers. The names of the headers in your CVS file need to match your code.  The uploader is going to take all of the headers from your CSV and turn it into an Object you can use to send data to Moltin.

It will then create flows for every field it does not recognize.  These flow fields will be applied to the product object.  You will want to adjust this list based on your data.  **This will ensure there is no overlap with Moltin fields and your custom fields**.

{% tabs %}
{% tab title="Moltin fields and your custom fields." %}
{% code-tabs %}
{% code-tabs-item title="moltinobjects.js" %}
```javascript
//add in defualt moltin fields.  This is just a general list.  You may need to adjust.
function checker(value) {
  var moltinObjects = [
    "price",
    "title",
    "sku",
    "slug",
    "type",
    "id",
    "status",
    "commodity_type",
    "meta",
    "stock",
    "relationships",
    "manage_stock",
    "description",
    "main_image",
    "relationships",
    "brand",
    "category",
    "name"
  ];
```
{% endcode-tabs-item %}
{% endcode-tabs %}
{% endtab %}
{% endtabs %}

{% hint style="info" %}
The only header that matter is image that will be used as the main image and price.  This column must have the header main\_image.  All other images will be add as files to the product.

For the price of the product you must name the header something beside price as moltin base object must use price.  I recommend naming your price column **price\_moltin.**
{% endhint %}

### Customize Moltin catalog objects

In the import folder you will see all of the Moltin catalog objects \(Brands, Categories, Collections and Main Image\). This is where you can match the parsed data to the data you want to send to Moltin. In this example we will add Products with Brand, Collection, Category and file relationships.

#### Create Brands, Categories, Collections and Main Images

{% tabs %}
{% tab title="Brand" %}
{% code-tabs %}
{% code-tabs-item title="brands.js" %}
```javascript
const brandM = await Moltin.Brands.Create({
        type: 'brand',
        name: brand.brand,
        description: brand.brand,
        slug: brand.brand.replace(/[^A-Z0-9]/gi, '_'),
        status: 'live'
```
{% endcode-tabs-item %}
{% endcode-tabs %}
{% endtab %}

{% tab title="Category" %}
{% code-tabs %}
{% code-tabs-item title="categories.js" %}
```javascript
const categoryM = await Moltin.Categories.Create({
        type: 'category',
        name: category.category,
        description: category.category,
        slug: category.category.replace(/[^A-Z0-9]/gi, '_'),
        status: 'live'
```
{% endcode-tabs-item %}
{% endcode-tabs %}
{% endtab %}

{% tab title="Collection" %}
```javascript
  const collectionM = await Moltin.Collections.Create({
        type: 'collection',
        name: collection.collection,
        description: collection.collection,
        slug: collection.collection.replace(/[^A-Z0-9]/gi, '_'),
        status: 'live'
      });
```
{% endtab %}

{% tab title="Main Image" %}
{% code-tabs %}
{% code-tabs-item title="images.js" %}
```javascript
 // need to identify what you want for your main image
       const options = {
        url: image.main_image,
        dest: `data/temp/${image.main_image}`
      };
```
{% endcode-tabs-item %}
{% endcode-tabs %}
{% endtab %}
{% endtabs %}

These will create the objects in Moltin. You can manipulate these to pass any data you want to these objects. This is also a great place to pass custom data to these objects using Flows. 

{% page-ref page="custom-data/" %}

#### Create a Product

Similar to the above objects this is where you can configure the data object to have all of the information you want to pass to the Moltin product object. By default, imported Products will have the `draft` status. You can change this by updating the `status` field form draft to `live`.

{% hint style="info" %}
Slug doesn't support foreign characters.
{% endhint %}

{% tabs %}
{% tab title="Create Product" %}
{% code-tabs %}
{% code-tabs-item title="product.js" %}
```javascript

let productM = await Moltin.Products.Create({
          type: 'product',
          name: product.title,
          slug: product.title.replace(/[^A-Z0-9]/gi, '_'),
          status: 'draft',
          price: [
            {
              amount: Number(product.price) * 100,
              currency: 'USD',
              includes_tax: true
            }
          ],
          sku: product.sku,
          manage_stock: false,
          commodity_type: 'physical',
          description: product.description
        });
```
{% endcode-tabs-item %}
{% endcode-tabs %}
{% endtab %}
{% endtabs %}

### Create Product catalogue relationships

Review the relationships you wish to establish in your catalog.  The key is to use the id from the newly created product, and then find the corresponding catalogue attribute \(brand, category, etc\).

#### Example

```javascript
// Find the category for the product from the list of already uploaded categories.
        var productsCategory = categoriesM.find(function(productsCategory) {
          return productsCategory.categories === product.categories;
        });
```

{% tabs %}
{% tab title="Brand" %}
{% code-tabs %}
{% code-tabs-item title="products.js" %}
```javascript
        //TIE PRODUCT to Brand
          console.log(
          'Assigning product id [%s] with brand name [%s]',
          productM.data.id,
          product.brand
        );

        const productsbrand = brandsM.find(function(productsbrand) {
          return productsbrand.name === product.brand;
        });

        await Moltin.Products.CreateRelationships(
          productM.data.id,
          'brand',
          productsbrand.id
        );

```
{% endcode-tabs-item %}
{% endcode-tabs %}
{% endtab %}

{% tab title="Category" %}
{% code-tabs %}
{% code-tabs-item title="product.js" %}
```javascript
        //TIE PRODUCT to Category
        console.log(
          "Assigning product id [%s] with category name [%s]",
          productM.data.id,
          product.category
        );

        const productscategory = categoryM.find(function(productscategory) {
          return productscategory.name === product.category;
        });

        await Moltin.Products.CreateRelationships(
          productM.data.id,
          "category",
          productscategory.id
        );
```
{% endcode-tabs-item %}
{% endcode-tabs %}
{% endtab %}

{% tab title="Collection" %}
```javascript
        //TIE PRODUCT to Collection
        console.log(
          "Assigning product id [%s] with collection name [%s]",
          productM.data.id,
          product.collection
        );

        var productscollection = collectionsM.find(function(productscollection) {
          return productscollection.name === product.collection;
        });

        await Moltin.Products.CreateRelationships(
          productM.data.id,
          "collection",
          productscollection.id
        );
```
{% endtab %}

{% tab title="Main Image" %}
{% code-tabs %}
{% code-tabs-item title="product.js" %}
```javascript
      //Need to format to match the file name used in images.js
        const fullName = productM.data.sku + ".jpg";
        console.log("Assigning image %s to %s", productM.data.id, fullName);

        //TIE PRODUCT to MainImage
        const productsMainImage = imagesImport.find(function(productsMainImage) {
          return productsMainImage.file_name === fullName;
        });

        console.log(
          "Assigning image %s to %s",
          productsMainImage,
          productM.data.id
        );
        await Moltin.Products.CreateRelationshipsRaw(
          productM.data.id,
          "main-image",
          {
            id: productsMainImage.id,
            type: "main_image"
          }
        );
```
{% endcode-tabs-item %}
{% endcode-tabs %}
{% endtab %}
{% endtabs %}

### Add your site credentials

You can either setup an .env file that will be used globally on the project or replace all placeholders in the project.

Add a .env file to your project.  Add in your keys.

{% tabs %}
{% tab title=".env" %}
{% code-tabs %}
{% code-tabs-item title=".env" %}
```javascript
NEW_SITE_CLIENT_ID=XXXXXX
NEW_SITE_SECRET=XXXXXX
```
{% endcode-tabs-item %}
{% endcode-tabs %}
{% endtab %}
{% endtabs %}

Search and replace process.env.NEW\_SITE\_CLIENT\_ID, process.env.NEW\_SITE\_SECRET, with your keys.  Below is an example, note there are several files you will need to update.

{% tabs %}
{% tab title="Client Id" %}
{% code-tabs %}
{% code-tabs-item title="moltin.js" %}
```javascript
const client = new MoltinClient({
  client_id: process.env.NEW_SITE_CLIENT_ID,
  client_secret: process.env.NEW_SITE_SECRET
});

```
{% endcode-tabs-item %}
{% endcode-tabs %}
{% endtab %}
{% endtabs %}

### Run the script

```text
node app.js "Path to your file"
//Example
node app.js "/Users/[username]/Downloads/apparel.csv"
```

{% hint style="info" %}
If you are using your own data update to match the path to your file
{% endhint %}

#### Options for running the script

If you are re-running the script, you can selectively **skip** certain steps \(of upload\) or require that a script first cleans up your store:

```text
node app.js "path" --delete=<brands> --delete=<another entity> --skip=<products> --skip=<another entity>
```

You can clean: `products`, `categories`, `brands`, and `Files` \(images\). To request that multiple entities be cleaned please use `--delete=<entity>` for each entity. Delete is for all, you can't skip anything.

### Where to go from here

This is the most basic script that can be used to get you started.  This can be extended to solve parent children hierarchy need and product variations.  The general paradigm can also be used in other language and environments. 

