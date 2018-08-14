# SDKs

Moltin officially maintains and supports both a JavaScript SDK and Swift SDK. Other SDKs are community driven and happily hosted within the [Moltin GitHub](https://github.com/moltin) organization.

See: [Your First API Request](../your-first-api-request.md) for full details on installing the SDKs and making your first API request.

## [JavaScript SDK](https://github.com/moltin/js-sdk)

Supporting both [Node.js](https://nodejs.org) and the client-side, the JS SDK silently handles the following:

* refreshing authentication tokens,
* generating automatic cart reference,
* supporting a simple DSL for accessing resources.

The JS SDK is available via [Yarn](https://yarnpkg.com/en/package/@moltin/sdk) and [npm](https://www.npmjs.com/package/@moltin/sdk) to install.

```bash
yarn add @moltin/sdk # npm install --save @moltin/sdk
```

Browse the [README](https://github.com/moltin/js-sdk#moltin-javascript-sdk) for further usage details.

## [JS Request Library](https://github.com/moltin/moltin-request)

`@moltin/request` is a minimal Moltin API request library available via [npm](https://www.npmjs.com/package/@moltin/request). 

It supports creating a client with `implicit` and `client_credential` permissions and exposes `GET`, `POST`, `PUT` and `DELETE` HTTP methods.

This library is useful, if you don't require all of the helpers the JS SDK provides.

```bash
yarn add @moltin/request # npm install @moltin/request
```

Browse the [README](https://github.com/moltin/moltin-request#moltinrequest) for further usage details.

## [Swift SDK](https://github.com/moltin/ios-sdk)

You'll want to install the Swift SDK to support your iOS, macOS, watchOS and tvOS applications.

The Swift SDK is available to install via Cocoapods, Carthage and Swift Package Manager.

```swift
//In your podfile
pod 'Moltin', '~> 3.0.11'

//Then run pod install in your terminal to install
```

Once the SDK is installed to your workspace you are ready to use it anywhere you want.  

```swift
import moltin

let moltin: Moltin = Moltin(withClientID: 'xxxx')
```

Browse the [README](https://github.com/moltin/ios-sdk#moltin-ios-sdk) for further usage details.

## Other SDKs

We happily provide community-driven SDKs that provide easy access to the Moltin API using your favorite language.

Not all API features are available but feel free to contribute via a Pull Request.

* [Ruby](https://github.com/moltin/ruby-sdk)
* [PHP](https://github.com/moltin/php-sdk)
* [Python](https://github.com/moltin/python-sdk)

