# Adding Email

In our last blog post, we showed you how to automatically generate a PDF invoice on order creation. Today, let’s learn how to automatically email that invoice to the customer! If you didn’t read the [previous post](https://moltin.com/blog/2017/06/automated-invoicing/), you’ll need to do so in order to complete this tutorial.

If you’d like to go directly to the code, have a look at the [invoicing repo](https://github.com/matthew1809/Invoicing-moltin).

###  Jump straight in {#jump-straight-in}

We need to add one more dependency to our project, so in Terminal, `cd` to the root directory of the project, and run;

```text
npm i -s nodemailer
```

Great! Now we’re going to be working with the `invoice.js` file that we created before, so hop back into that. If you need a reference, the file can be found in [the repo](https://github.com/matthew1809/Invoicing-moltin/blob/master/invoice.js).

Add our new dependency to the top, but below the other ones:

```text
const nodemailer = require('nodemailer');
```

Now, below our `generateInvoice` function, add the following code:

```text
let transporter = nodemailer.createTransport({  service: config.service,  auth: {    user: config.user,    pass: config.pass  }});​let mailOptions = {  from: '"Your Store" <invoicing@youremail.com>', // sender address  to: 'jon.doe@gmail.com', // list of receivers  subject: 'Your Invoice', // Subject line  text: 'Please find your invoice attached', // plain text body  html: '<b>Your Invoice</b>', // html body  attachments: [{   // file on disk as an attachment    filename: 'invoice.pdf',    path: './invoice.pdf' // stream this file   }] };​var sendMail = () => {  transporter.sendMail(mailOptions, (error, info) => {    if (error) {      return console.log(error);    }    console.log('Message %s sent: %s', info.messageId, info.response);  })}
```

Look for where we’ve triggered the `generateInvoice` function \(within second `.then` of `Moltin.Orders.Items`\). Replace the code within this `.then` with:

```text
var to = invoice.to + '.pdf'​generateInvoice(invoice, 'invoice.pdf', function() {  console.log("Saved invoice to" + to)  mailOptions.attachments[0].filename = to  mailOptions.attachments[0].path = './' + to  return sendMail()})
```

Lastly, where we have our `app.post` callback, add this line:

```text
mailOptions.to = pbody.data.customer.email
```

We’re done! Now when our integration is triggered by an order being created, it will automatically generate the invoice with the order details, and email it to the email address specified at checkout.  


