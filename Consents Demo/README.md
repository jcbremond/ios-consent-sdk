# SmartlookConsentSDK for iOS

Obtaining explicit user consent with gathering analytics data in an app, or with processing user’s personal data is important part of establishing user trust and seamless user experience.

It is also an obligation of an app developer stated in [App Store Guidelines](https://developer.apple.com/app-store/review/guidelines/) (2.5.14 and 5.1.2), necessary to fulfil in order to get your app approved for distribution. If your app e.g. uses a 3rd party tool for analytics, it is the sole responsibility of you as the app developer, not of the 3rd party tool provider, that the analytics tool does not start registering events in the app without user explicit consent.

Although implementing some dialog to obtain user consents and store them for further reference seems pretty straightforward, digging into it reveals (as usual with “_simple tasks_”) many programming and design details that must be implemented, which are not the core functionality of your app. 

So why not use or reuse some ready-made SDK?

[![iPhone Screenshot](readme-media/ConsentSDK-Screenshot-iPhone-thumbnail.png)](readme-media/ConsentSDK-Screenshot-iPhone.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [![iPad Screenshot](readme-media/ConsentSDK-Screenshot-iPad-thumbnail.png)](readme-media/ConsentSDK-Screenshot-iPad.png)

## What SmartlookConsentSDK does. 

This SDK:
- provides a configurable control panel where user selects her privacy options
- stores the selected user preferences to be used in your app
- works both with Swift and Objective-C apps
- all texts are fully localizable
- privacy policies may be provided by an external web page that is presented w/out leaving the app (see [localisation](#localisation).)

## Code examples
### Simple example 
The most straightforward use that utilises default settings:
```swift
SmartlookConsentSDK.check() {
      if SmartlookConsentSDK.consent(for: .analytics) == .provided {
          // Start analytics tools
      }
}
```

This method first checks if the user already provided/rejected consent for both `privacy` and `analytics`. If not, the privacy control panel is shown with build-in defaults (consent is provided for both). When user agrees, the callback is called to let you handle the user preferences. 

In the case user previously went though these settings, the control panel is not show and the callback is called straight away.

### Complex example 

In this example, only consent for analytics is sought for with the consent provided by default.

```swift
var consentsSettingsDefaults = SmartlookConsentSDK.ConsentsSettings()
//consentsSettingsDefaults.append((.privacy, .notProvided)) 
consentsSettingsDefaults.append((.analytics, .provided))

SmartlookConsentSDK.check(with: consentsSettingsDefaults) {
    if SmartlookConsentSDK.consentState(for: .analytics) == .provided {
        // start analytics tools
    }
}
```

## Objective-C
The SDK is fully compatible with `Objective-C`. For code examples see the respective demo app.

## Instalation

### Direct framework embedding
Framwork is ready for direct embedding in your app, e.g. the way it is embedded into the demo apps here. For detailed descriptions see this Apple Developer Portal document [Embedding Frameworks In An App](https://developer.apple.com/library/archive/technotes/tn2435/_index.html)

### Cocoapods
{TBD}

## API

### Consent
Consent is identified by a string constant. For convenience, SDK provides a `String` alias with two predefined constants, `.privacy` a `.analytics` together with the respective text placeholders in demo app `Localizable.strings`. However, any string works, and the name convention used in `Localizable.strings` is obvious.

### ConsentState
Is a standard enumaration and indicates whether user seen and provided consent to a policy.
- `.unknown` state indicates that the user did not reviewed the policy
- `.notProvided` state indicates that the user explicitely refused consent to the policy
- `.provided` state indicates that the user explicitely provided consent to the policy 
```swift
@objc(SLCConsentState) public enum ConsentState: Int {
      case unknown = -2
      case notProvided = -1
      case provided = 1
}
```
### SmartlookConsentSDK.check()
Is the key method of the SDK. It comes in two versions:

```swift
@objc public static func check(callback: @escaping RequestIdCallback)
``` 
a straigth one w/out consents configuration that can be used when both `.privacy` and `.analytics` policies consents are required with `.provided` as the default value. 

```swift
public static func check(with consentsSettings: ConsentsSettings, callback: @escaping RequestIdCallback)
```  
a version with configuration that allows fine-tuning required consents (adding, removing, chaning order of or their default values) 

### SmartlookConsentSDK.show()
Two variants much like `check()`, it **always** opens the Control Panle for the user to review her current privacy settings.

## Localisation
The texts shown in the control panel are configured using the standard `Localizable.strings` mechanism. `Localizable.strings`  are also used to provide an optional URL of a detailed policy information (thus the link is localised as well).

The keys used in the `Localizable.strings` are listed in the table below, or you can simply reuse [the file in our demo app](ConsentSDKDemo/ConsentSDKDemo/Base.lproj/Localizable.strings) .

Localization follows a name conventions. If a new consent type is added (on top of the predefined convenience types `.privacy` a `.analytics`), the respective keys must be added to localized files following the pattern

```
"smartlook-consent-sdk-*consent-key*-consent" = "My special consent...";
"smartlook-consent-sdk-*consent-key*-consent-url" = "https://www.my-company.com/consent-policy-details?lang=de"; //optional
```

where `*consent-key*` is simply the text string constant used to identify the policy.