---
description: A walk through of using apple pay and google pay
---

# Apple / Google Pay

## Apple Pay Walkthrough

Feel free to skip to the end and grab the completed example project.

###  1. **Get** the code from GitHub

In terminal

```bash
git clone https://github.com/moltin-examples/applepay-starter.git
```

Then navigate into the new directory, and install the cocopods.

```bash
pod install
```

### 2.  Setting up the project

Open the workspace project and run to confirm everything is working.

![](../.gitbook/assets/screen-shot-2018-07-16-at-11.00.42-am.png)

### 3.  Displaying a product

The app as it stands is a simple _Master/Detail_ application that shows a list of products from a moltin demo store in the _master_ view. Tap on any product to navigate to a _detail_ view which shows a bigger picture of the swag as well as a more detailed description.

#### 3a. Update to your Moltin Store

Update the store id in MoltinProductsViewController to use your products.

```swift
//Replace with your client id
let moltin = Moltin(withClientID: "u8cV0fAtS8ELXcyxWY2r4deLTHs1i3NkgV8rt7ZqWX")
```

### 4.  Set up apple pay credentials

Go to [http://developer.apple.com](http://developer.apple.com/) and log in to your developer account. Go to _Member Center_ and click on _Certificates, Identifiers & Profiles\Identifiers\App IDs_.

Click on the _+_ button to create a new App ID, name it _Apple MoltinProducts \(_or your app name\) and give it a Bundle ID of the format _com.YOURDOMAIN.MoltinProducts \(_use your own here, if you already have a project\). Make sure that _Explicit App ID_ is selected, as wildcard App IDs aren’t able to make payments with Apple Pay. Finally, check the _Apple Pay_ checkbox under _App Services_, click _Continue_ and then click _Submit_ to complete the creation of your new App ID.

Next, click on _Merchant IDs_ under _Identifiers_ in the left navigation pane

Click _+_ to create a new merchant ID; use whatever description and merchant identifier you’d like. Generally, it’s recommended to create merchant identifiers in the reverse domain style that start with `merchant`, similar to bundle IDs. Click _Continue_ and then click _Register_ to create your new merchant ID.

Now that your App ID and merchant ID are set up, head back to Xcode to get your project ready for Apple Pay.

Select the _MoltinProducts_ project in the left navigation bar, then select the _MoltinProducts_ target and change the _Bundle Identifier_ to match the one you created above. Ensure that the _Team_ selection box is pointing to the development team under which you created your App ID and merchant ID.

Next, click the _Capabilities_ tab. Expand the _Apple Pay_ section and ensure that the switch on the right is set to _ON_. Then push the _refresh_ button below the merchant ID list; you should see the list populate with the merchant ID you added on the developer portal, if it wasn’t there already.

Finally, check the checkbox next to your merchant ID.

The three items in the _Steps_ section should all have checkmarks next to them to indicate that you’ve satisfied all of the requirements for using Apply Pay in your app. If one isn’t checked, retrace your steps to make sure you’ve taken care of all the details.

You now have Apple Pay enabled in your app.

### 5.  Add Apple pay button

Open _Main.storyboard_ and take a look at the the _Buy Product_ scene

![](../.gitbook/assets/screen-shot-2018-07-16-at-1.02.34-pm.png)

Apple has a very specific set of Apple Pay guidelines to adhere to, which extends to the buttons in your app. Take a few minutes to review these guidelines at the Apple Pay developer site [https://developer.apple.com/apple-pay/](https://developer.apple.com/apple-pay/).

_Note:_ The _Apple Pay Buttons and Resources_ link at the Apple Pay developer site above provides you with a zip file containing an extensive collection of approved button resources for Apple Pay.

You will find a set of Apple Pay images ready for use in the starter project’s _Image.xcassets_.

Using the _Buy Product_  Interface Builder, select the Apple Pay button and change the image to _ApplePay_. Give your button an empty title instead of the default “button” title. Your scene should now look as follows

![](../.gitbook/assets/screen-shot-2018-07-16-at-1.06.27-pm.png)

### 6.  Create Apple pay request

Open _BuyProductViewController.swift_ and add the following import to the top of the file:

```swift
import PassKit
```

Next, locate `applePayPressed(sender:)`; you execute this when the user attempts to purchase an item. To do this, you’ll need to create a `PKPaymentRequest` and a `PKPaymentAuthorizationViewController`.

Add the following code to the body of `applePayPressed(sender:)`:,

```swift
// TODO: - Fill in implementation
let request = PKPaymentRequest()
let applePayController = PKPaymentAuthorizationViewController(paymentRequest: request)
self.present(applePayController!, animated: true, completion: nil)
```

Add the following code just under the `IBOutlet` properties of `BuySwagViewController`:

```swift
let SupportedPaymentNetworks = [PKPaymentNetwork.visa, PKPaymentNetwork.masterCard, PKPaymentNetwork.amex]  // Add in any extra support payments.
let ApplePayMerchantID = "merchant.com.YOURDOMAIN.ApplePayMoltin" // Fill in your merchant ID here!
```

In the viewDidLoad check to see if the user can use Apple pay.

```swift
applePayButton.hidden = !PKPaymentAuthorizationViewController.canMakePaymentsUsingNetworks(SupportedPaymentNetworks)
```

Now start filling out the request in `applePayPressed(sender:)`, under the `let request = PKPaymentRequest()`

```swift
request.merchantIdentifier = ApplePayMerchantID
request.supportedNetworks = SupportedPaymentNetworks
request.merchantCapabilities = PKMerchantCapability.capability3DS
request.countryCode = "US"
request.currencyCode = "USD"
```

Create an array of `PKPaymentSummaryItem` objects that provide the user with a breakdown the items they’re purchasing.  We will add more detail to this array in the next step.

```swift
//Item information formatting
let productToBuy = PKPaymentSummaryItem(label: product?.name ?? "", amount: NSDecimalNumber(decimal:Decimal((self.product?.meta.displayPrice?.withoutTax.amount)!/100)), type: .final)
let total = PKPaymentSummaryItem(label: "Total with Tax", amount: NSDecimalNumber(decimal:Decimal((self.product?.meta.displayPrice?.withTax.amount)!/100)))
//PKPaymentSummaryItem Array, we will be adding too.
request.paymentSummaryItems = [productToBuy,total]
```

Run the app and confirm you are now seeing apple pay when you hit the apple pay button on the buy scene.

![](../.gitbook/assets/screen-shot-2018-07-16-at-2.04.21-pm.png)

### 7.  Handling Shipping

We will be adding a static billing amount to start.  Billing needs are going to differ greatly on what your store is selling.

```swift
let shippingPrice: NSDecimalNumber = NSDecimalNumber(string: "5.0")
let shipping = PKPaymentSummaryItem(label: "Shipping", amount: shippingPrice)
let totalPrice = PKPaymentSummaryItem(label: "Total amount", amount: NSDecimalNumber(decimal:Decimal((self.product?.meta.displayPrice?.withTax.amount)!)/100).adding(shippingPrice))
        
//PKPaymentSummaryItem Array
request.paymentSummaryItems = [productToBuy,shipping, totalPrice]
```



### 8.  Implement Apple pay delegates

In _BuyProductViewController.swift_, add the following extension to `BuyProductViewController` that implements `PKPaymentAuthorizationViewControllerDelegate`:

```swift
//Bottom of file
extension BuyProductViewController: PKPaymentAuthorizationViewControllerDelegate {
    func paymentAuthorizationViewController(_ controller: PKPaymentAuthorizationViewController, didAuthorizePayment payment: PKPayment, completion: @escaping ((PKPaymentAuthorizationStatus) -> Void)) {
        completion(PKPaymentAuthorizationStatus.success)
    }
    
    func paymentAuthorizationViewControllerDidFinish(_ controller: PKPaymentAuthorizationViewController) {
        controller.dismiss(animated: true, completion: nil)
    }
}
```

To be able to use the apply pay delegate class you need to set it in above`BuyProductViewController` class.  In the `applePayPressed(sender:)`, set it up using the below.

```swift
applePayController?.delegate = self
```

### 9.  Creating checkout in Moltin

In `BuyProductViewController`in the PKPaymentAuthorizationViewControllerDelegate you will be sending a successful apple pay order to moltin.

Getting customers information.  It will be returned from the PKPaymentAuthorizationViewControllerDelegate in the payment object.

Setting up customer.

```swift
let customer = Customer(withEmail: payment.billingContact?.emailAddress, withName: payment.shippingContact?.name?.familyName)
```

Setting up address.  \(note shipping and billing may vary\)

```swift
let address = Address(withFirstName: (payment.shippingContact?.name?.givenName)!, withLastName: payment.shippingContact?.name?.familyName ?? "")
address.line1 = payment.shippingContact?.postalAddress?.street
address.county = payment.shippingContact?.postalAddress?.city
address.country = payment.shippingContact?.postalAddress?.country
address.postcode = payment.shippingContact?.postalAddress?.postalCode
```

Processing an order with moltin

```swift
self.moltin.cart.checkout(cart: AppDelegate.cartID, withCustomer: customer, withBillingAddress: address, withShippingAddress: address)
    { (result) in
       switch result {
            case .success(let order):
                DispatchQueue.main.async {
                print(order)
                }
            default: break
            }
    }
```

Paying for the order can be done by varies payment gateways, [https://docs.moltin.com/payments/gateways](https://docs.moltin.com/payments/gateways).  In this example we will us manual, which will allow you complete the transaction than handle the payment processing any way you want.

In the moltin Dashboard you need to enable manual checkout.  In Gateways, click enable within the manual gateway and save, [https://dashboard.moltin.com/app/settings/gateways/manual](https://dashboard.moltin.com/app/settings/gateways/manual).

```swift
let paymentMethod = ManuallyAuthorizePayment()
self.moltin.cart.pay(forOrderID: order?.id ?? "", withPaymentMethod: paymentMethod) { (result) in
    switch result {
    case .success(let status):
        DispatchQueue.main.async {
            print("Paid for order: \(status)")
        }
    case .failure(let error):
        print("Could not pay for order: \(error)")
    }
}

```

Within the above checkout, add in the below to complete the transaction.

```swift
let paymentMethod = ManuallyAuthorizePayment()
    self.moltin.cart.pay(forOrderID: order.id, withPaymentMethod: paymentMethod) { (result) in
        switch result {
            case .success(let status):
                DispatchQueue.main.async {
                    controller.dismiss(animated: true, completion: nil)
                    print("Paid for order: \(status)")
                }
             case .failure(let error):
                  controller.dismiss(animated: true, completion: nil)
                  print("Could not pay for order: \(error)")
                 }
    }
```

Your PKPaymentAuthorizationViewControllerDelegate should look like the below.

```swift
extension BuyProductViewController: PKPaymentAuthorizationViewControllerDelegate
{
    
    func paymentAuthorizationViewController(_ controller: PKPaymentAuthorizationViewController, didAuthorizePayment payment: PKPayment, completion: @escaping
    ((PKPaymentAuthorizationStatus) -> Void))
    {
        completion(PKPaymentAuthorizationStatus.success)
        ///Customer
        let customer = Customer(withEmail: payment.billingContact?.emailAddress, withName: payment.shippingContact?.name?.familyName)
    
        //Address
        let address = Address(withFirstName: (payment.shippingContact?.name?.givenName)!, withLastName: payment.shippingContact?.name?.familyName ?? "")
        address.line1 = payment.shippingContact?.postalAddress?.street
        address.county = payment.shippingContact?.postalAddress?.city
        address.country = payment.shippingContact?.postalAddress?.country
        address.postcode = payment.shippingContact?.postalAddress?.postalCode
        
         self.moltin.cart.checkout(cart: AppDelegate.cartID, withCustomer: customer, withBillingAddress: address, withShippingAddress: address)
         { (result) in
            switch result {
            case .success(let order):
                DispatchQueue.main.async {
                    let paymentMethod = ManuallyAuthorizePayment()
                    self.moltin.cart.pay(forOrderID: order.id, withPaymentMethod: paymentMethod) { (result) in
                        switch result {
                            case .success(let status):
                                DispatchQueue.main.async {
                                    print("Paid for order: \(status)")
                                    controller.dismiss(animated: true, completion: nil)

                                }
                            case .failure(let error):
                                print("Could not pay for order: \(error)")
                                controller.dismiss(animated: true, completion: nil)
                            }
                        }
                    }
                    default: break
                }
            }
        }

    func paymentAuthorizationViewControllerDidFinish(_ controller: PKPaymentAuthorizationViewController)
    {
        controller.dismiss(animated: true, completion: nil)
        
    }
}

```

### Moving forward.

.....



### Completed example project





