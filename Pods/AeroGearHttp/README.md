# aerogear-ios-http  [![Build Status](https://travis-ci.org/aerogear/aerogear-ios-http.png)](https://travis-ci.org/aerogear/aerogear-ios-http)
Thin layer to take care of your http requests working with NSURLSession. 
Taking care of: 

* Json serializer
* Multipart upload 
* HTTP Basic/Digest authentication support
* Pluggable object serialization
* background processing support

100% Swift.

## Example Usage

To perform an HTTP request use the convenient methods found in the Http object. Here is an example usage:

```swift
let http = Http(baseURL: "http://server.com")

http.GET("/get", completionHandler: {(response, error) in
     // handle response
})

http.POST("/post",  parameters: ["key": "value"], 
                    completionHandler: {(response, error) in
     // handle response
})
...
```

### Authentication

The library also leverages the build-in foundation support for http/digest authentication and exposes a convenient interface by allowing the credential object to be passed on the request. Here is an example:

> **NOTE:**  It is advised that HTTPS should be used when performing authentication of this type

```swift
let credential = NSURLCredential(user: "john", 
                                 password: "pass", 
                                 persistence: .None)

http.GET("/protected/endpoint", credential: credential, 
                                completionHandler: {(response, error) in
   // handle response
})
```

You can also set a credential per protection space, so it's automatically picked up once http challenge is requested by the server, thus omitting the need to pass the credential on each request. In this case, you must initialize the ```Http``` object with a custom session configuration object, that has its credentials storage initialized with your credentials:

```swift
// create a protection space
var protectionSpace = NSURLProtectionSpace(host: "httpbin.org", 
                                    port: 443,
                                    protocol: NSURLProtectionSpaceHTTPS, 
                                    realm: "me@kennethreitz.com", 
                                    authenticationMethod: NSURLAuthenticationMethodHTTPDigest)

// setup credential
// notice that we use '.ForSession' type otherwise credential storage will discard and
// won't save it when doing 'credentialStorage.setDefaultCredential' later on
let credential = NSURLCredential(user: user, 
                                 password: password, 
                                 persistence: .ForSession)

// assign it to credential storage
var credentialStorage = NSURLCredentialStorage.sharedCredentialStorage()
credentialStorage.setDefaultCredential(credential, forProtectionSpace: protectionSpace);

// set up default configuration and assign credential storage
var configuration = NSURLSessionConfiguration.defaultSessionConfiguration()
configuration.URLCredentialStorage = credentialStorage

// assign custom configuration to Http
var http = Http(baseURL: "http://httpbin.org", sessionConfig: configuration)

http.GET("/protected/endpoint", completionHandler: {(response, error) in
   // handle response
})
```

### OAuth2 Protocol Support

To support the OAuth2 protocol, we have created a separate library [aerogear-ios-oauth2](https://github.com/aerogear/aerogear-ios-oauth2) that can be easily integrated, in order to provide  out-of-the-box support for communicated with OAuth2 protected endpoints. Please have a look at the "Http and OAuth2Module" section on our [documentation page](http://aerogear.org/docs/guides/aerogear-ios-2.X/Authorization/) for more information. 

Do you want to try it on your end? Follow next section steps.

> **NOTE:**  The library has been tested with Xcode 6.1.1

### Build, test and play with aerogear-ios-http

1. Clone this project

2. Get the dependencies

The project uses [aerogear-ios-httpstub](https://github.com/aerogear/aerogear-ios-httpstub) framework for stubbing its http network requests and utilizes [cocoapods](http://cocoapods.org) 0.36.0 pre-release for handling its dependencies. As a pre-requisite, install [cocoapods pre-release](http://blog.cocoapods.org/Pod-Authors-Guide-to-CocoaPods-Frameworks/) and then install the pod. On the root directory of the project run:
```bash
pod install
```
3. open AeroGearHttp.xcworkspace

## Adding the library to your project 
To add the library in your project, you can either use [Cocoapods](http://cocoapods.org) or manual install in your project. See the respective sections below for instructions:

### Using [Cocoapods](http://cocoapods.org)
At this time, Cocoapods support for Swift frameworks is supported in a [pre-release](http://blog.cocoapods.org/Pod-Authors-Guide-to-CocoaPods-Frameworks/). In your ```Podfile``` add:

```
pod 'AeroGearHttp'
```

and then:

```bash
pod install
```

to install your dependencies

### Manual Installation
Follow these steps to add the library in your Swift project:

1. Add AeroGearHttp as a [submodule](http://git-scm.com/docs/git-submodule) in your project. Open a terminal and navigate to your project directory. Then enter:
```bash
git submodule add https://github.com/aerogear/aerogear-ios-http.git
```
2. Open the `aerogear-ios-http` folder, and drag the `AeroGearHttp.xcodeproj` into the file navigator in Xcode.
3. In Xcode select your application target  and under the "Targets" heading section, ensure that the 'iOS  Deployment Target'  matches the application target of AeroGearHttp.framework (Currently set to 8.0).
5. Select the  "Build Phases"  heading section,  expand the "Target Dependencies" group and add  `AeroGearHttp.framework`.
7. Click on the `+` button at the top left of the panel and select "New Copy Files Phase". Rename this new phase to "Copy Frameworks", set the "Destination" to "Frameworks", and add `AeroGearHttp.framework`.


If you run into any problems, please [file an issue](http://issues.jboss.org/browse/AEROGEAR) and/or ask our [user mailing list](https://lists.jboss.org/mailman/listinfo/aerogear-users). You can also join our [dev mailing list](https://lists.jboss.org/mailman/listinfo/aerogear-dev).  

