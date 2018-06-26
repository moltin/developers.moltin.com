---
description: Integrating the moltin Swift SDK into your existing iOS application
---

# Swift SDK

## Create a moltin project

[Sign up](https://dashboard.moltin.com/signup) or [login](https://dashboard.moltin.com/login) to the moltin dashboard to obtain your API keys. If you already have an existing moltin store, you can create additional stores inside the [accounts dashboard](https://accounts.moltin.com/).

#### Install with CocoaPods

The recommended way to install the SDK is via CocoaPods. Add Moltin version 2 to your existing project’s Podfile.

```text
pod 'Moltin', '~> 3.0.7'
```

Then In terminal install the pod

```text
Pod install
```

#### Import

Import the Moltin SDK into your Swift file

```text
import Moltin
```

#### Authenticate

```text
let moltin: Moltin = Moltin(withClientID: "XXXX")
```

Initialize the Moltin SDK. You will need a public ID to authenticate with our API. You can find your store keys in your [store dashboard](https://dashboard.moltin.com/).

#### Next steps

Now that you have the SDK installed within your project, it's time to go further. You can follow along with our basic checkout flow that uses the Swift SDK or you could dive into the SDK README.

