---
title: Flutter SDK
description: The Amplitude Flutter SDK installation and quick start guide. 
icon: simple/flutter
---

![pub package](https://img.shields.io/pub/v/amplitude_flutter.svg)

This is the official documentation for the Amplitude Analytics Flutter SDK.

!!!info "SDK Resources"
    - [Flutter SDK Reference :material-book:](https://pub.dev/documentation/amplitude_flutter/latest/)
    - [Flutter SDK Repository :material-github:](https://github.com/amplitude/Amplitude-Flutter)
    - [Flutter SDK Releases :material-code-tags-check:](https://github.com/amplitude/Amplitude-Flutter/releases)

--8<-- "includes/no-ampli.md"

## Installation

### Add dependencies

1. Go to the `pubspec.yaml` file and add Amplitude SDK as a dependency.

    ```yml
    dependencies:
      amplitude_flutter: ^3.7.0
    ```

2. Run `flutter pub get` in the terminal to install the SDK.

### iOS installation

Add `platform :ios, '10.0'` to your Podfile.

To enable Bitcode, follow Flutter's [documentation](https://github.com/flutter/flutter/wiki/Creating-an-iOS-Bitcode-enabled-app).

## Usage and examples

### Initialization

Before you can instrument, you must initialize the SDK using the API key for your Amplitude project.

```dart
import 'package:amplitude_flutter/amplitude.dart';
import 'package:amplitude_flutter/identify.dart';

class YourClass {
  Future<void> exampleForAmplitude() async {
    // Create the instance
    final Amplitude analytics = Amplitude.getInstance(instanceName: "project");

    // Initialize SDK
    analytics.init(widget.apiKey);

    // Log an event
    analytics.logEvent('MyApp startup', eventProperties: {
      'friend_num': 10,
      'is_heavy_user': true
    });
  }
}
```

### EU data residency

Beginning with version 3.6.0, you can configure the server zone after initializing the client for sending data to Amplitude's EU servers. The SDK sends data based on the server zone if it's set.
 The server zone configuration supports dynamic configuration as well.

For earlier versions, you need to configure the `serverURL` property after initializing the client.

!!!note
    For EU data residency, the project must be set up inside Amplitude EU. You must initialize the SDK with the API key from Amplitude EU.

```dart
// For versions starting from 3.6.0
// No need to call setServerUrl for sending data to Amplitude's EU servers
Amplitude.getInstance().setServerZone("EU");

// For earlier versions
Amplitude.getInstance().setServerUrl("https://api.eu.amplitude.com")
```

### Sending events

--8<-- "includes/sdk-httpv2-notice.md"

#### Basic events

Events represent how users interact with your application. For example, "button clicked" may be an action you want to track.

```dart
Amplitude.getInstance().logEvent('BUTTON_CLICKED');
```

#### Events with properties

Events can also contain properties. They provide context about the event taken. For example, "hover time" may be a relevant event property to "button click"

```dart
Amplitude.getInstance().logEvent('BUTTON_CLICKED', {"Hover Time": "100ms"});
```

#### Flush Events

Events are typically stored in a buffer and flushed periodically. This behavior is configurable. You can also flush events manually

```dart
Amplitude.getInstance().uploadEvents();
```

### User properties

User properties help you understand your users at the time they performed some action within your app such as their device details, their preferences, or language.
 Amplitude-Flutter's `identify` manages this feature. You need to import `identify` before using it.

```dart
import 'package:amplitude_flutter/identify.dart';
```

!!!warning "User privacy warning"

    Don't track any user data that may be against your privacy terms.

#### Setting a user property

#### `set`

`set` sets the value of a user property. You can also chain together multiple identify calls.

```dart
final Identify identify = Identify()
                          ..set('gender','female')
                          ..set('age',20);
Amplitude.getInstance().identify(identify);
```

#### `setOnce`

`setOnce` sets the value of a user property one time. Later calls using `setOnce` are ignored.

```dart
final Identify identify1 = Identify();
identify1.setOnce('sign_up_date', '2015-08-24');
Amplitude.getInstance().identify(identify1);
final Identify identify2 = Identify();
identify2.setOnce('sign_up_date', '2015-08-24');
Amplitude.getInstance().identify(identify2);// is ignored
```

#### `add`

`add` increments a user property by some numerical value. If the user property doesn't have a value set yet, it's initialized to 0 before being incremented.

```dart
final Identify identify = Identify().add('karma', 0.123);
Amplitude.getInstance().identify(identify);
```

#### `preInsert`

Adds a value or values to a user property at the beginning of the list, if the value doesn't exist in the user property yet.
 If the user property doesn't have a value set yet, it's initialized to an empty list before the new values are pre-inserted. If the user property has an existing value, nothing happens.

```dart
final Identify identify = Identify()
                          ..preInsert('existing_list', 'some_property')
Amplitude.getInstance().identify(identify);
```

#### `postInsert`

Adds a value or values to a user property at the end of the list, if the value doesn't exist in the user property yet. If the user property doesn't have a value set yet,
 it's initialized to an empty list before the new values are inserted. If the user property has an existing value, nothing happens.

```dart
final Identify identify = Identify()
                          ..postInsert('existing_list','some_property')
Amplitude.getInstance().identify(identify);
```

### Setting multiple user properties

You can use `setUserProperties` as a shorthand to set multiple user properties at one time. This method is a wrapper around `Identify.set` and `identify`.

```dart
Map<String, dynamic> userProps = {
  'KEY': 'VALUE',
  'OTHER_KEY': 'OTHER_VALUE'
};
Amplitude.getInstance().setUserProperties(userProperties);
```

### Arrays in user properties

Arrays can be used as user properties. You can directly set arrays or use append to generate an array.

```dart
const colors = ["rose", "gold"];
const numbers = [4, 5];
final Identify identify = Identify()
                          ..set("colors", colors)
                          ..append("ab-tests", "campaign_a")
                          ..prepend("existing_list", numbers);
Amplitude.getInstance().identify(identify);
```

#### `prepend` and `append`

- `prepend` prepends a value or values to a user property.
- `append` appends a value or values to a user property array.
    If the user property doesn't have a value set yet, it's initialized to an empty list before the new values are added. If the user property has an existing value and it's not a list, it's converted
     into a list with the new value added.

```dart
const array = ["some_string", 56];
final Identify identify = Identify()
                          ..append("ab-tests", "new-user-test")
                          ..preappend("some_list", array)
Amplitude.getInstance().identify(identify);
```

### Removing user properties

#### Clearing all user properties

`clearUserProperties` clears all the current user's user properties.

!!!warning

    The result is irreversible! Amplitude can't sync the user's user property values from before the wipe to any future events.

```dart
Amplitude.getInstance().clearUserProperties();
```

#### `remove`

`remove` removes a value or values from a user property. If the item doesn't exist in the user property, nothing happens.

```dart
const array = ["some_string", 56];
final Identify identify = Identify()
                          ..remove("ab-tests", "new-user-test")
                          ..remove("some_list",array);
Amplitude.getInstance().identify(identify);
```

#### `unset`

`unset` unsets and removes a user property.

```dart
final Identify identify = Identify()
                          ..unset("ab-tests", "new-user-test")
                          ..unset("some_list",array);
Amplitude.getInstance().identify(identify)
```

### Track revenue

Amplitude can track revenue generated by a user. Revenue is tracked through distinct revenue objects, which have special fields that are used in Amplitude's Event Segmentation and Revenue LTV charts.
 This allows Amplitude to automatically display data relevant to revenue in the platform.

!!!note

    Amplitude doesn't support currency conversion. All revenue data should be normalized to your currency of choice before being sent.

```dart
String productId = "product001";
int quantity = 2;
double price = 20;
double amount = 35;
Amplitude.getInstance().logRevenue(productId, quantity, price);
Amplitude.getInstance().logRevenueAmount(amount);
```

!!!info

    Price can be negative, which may be useful for tracking revenue lost (such as refunds or costs).

### Group user properties

--8<-- "includes/editions-growth-enterprise-with-accounts.md"

--8<-- "includes/group-identify-considerations.md"

```dart
final Identify identify = Identify()
                          ..set("gender", "female")
                          ..set("age", 20);
Amplitude.getInstance().groupIdentify("groupType", "groupValue", identify);
```

### User sessions

A session is a period of time that a user has the app in the foreground. Events that are logged within the same session have the same `session_id`.
 Sessions are handled automatically so you don't have to manually call an API like `startSession()` or `endSession()`.
Amplitude groups events together by session. A session represents a single period of user activity, with a start and end time.
 Different SDKs track sessions differently, depending on the requirements of the platform.
You can choose to automatically log start and end session events corresponding to the start and end of a user's session.

```dart
//Enable automatically log start and end session events
Ampiltidue.getInstance().trackingSessionEvents(true);
//Disable automatically log start and end session events
Amplitidue.getInstance().trackingSessionEvents(false);
```

### Set custom user ID

If your app has its own login system that you want to track users with, you can call `setUserId` at any time.

```dart
Amplitude.instance().setUserId("test_user_id");
```

## Advanced topics

### COPPA Control

COPPA (Children's Online Privacy Protection Act) restrictions on IDFA, IDFV, city, IP address and location tracking can be enabled or disabled all at once.
 Remember that apps asking for information from children under 13 years of age must comply with COPPA.

```dart
//Enable COPPA Control
Amplitude.getInstance().enableCoppaControl();
//Disable COPPA Control
Amplitude.getInstance().disableCoppaControl();
```

### Advertising ID

Advertiser ID (also referred to as IDFA) is a unique identifier provided by the iOS and Google Play stores. As it's unique to every person and not just their devices, it's useful for mobile attribution.
 [Mobile attribution](https://www.adjust.com/blog/mobile-ad-attribution-introduction-for-beginners/) is the attribution of an installation of a mobile app to its original
 source (for example, and ad campaign or app store search).

 Mobile apps need permission to ask for IDFA, and apps targeted to children can't track at all. Consider IDFV, device id, or an email login system as alternatives when IDFA isn't available.

See [iOS Advertising ID](../ios/#advertising-id) or the [Android Advertising ID](../android-kotlin/#advertiser-id) for more information.

### Opt out of tracking

Users may wish to opt out of tracking entirely, which means no events and no records of their browsing history are tracked. `setOptOut` provides a way to fulfill user requests for privacy.

```dart
//Disables instrumentation
Amplitude.getInstance().setOptOut(true);
//Enables instrumentation
Amplitude.getInstance().setOptOut(false);
```

### Dynamic configuration

Flutter SDK lets users configure their apps to use [dynamic configuration](../../dynamic-configuration). This feature finds the best server URL automatically based on app users' location.

- If you have your own proxy server and use `setServerUrl` API, don't use dynamic configuration.
- If you have users in Mainland China, we recommend that you use dynamic configuration.
- By default, this feature is off. You must explicitly enable it to use it.
- By default, this feature returns server URLs for Amplitude's US servers. If you need to send data to Amplitude's EU servers,  use `setServerZone` to set it to EU zone.

```dart
Amplitude.getInstance().setUseDynamicConfig(true);
```

### Flutter veb support

Flutter web support delivers the same experiences on the web as on mobile. Amplitude-Flutter starts to support flutter web from v3.8.0.

These features aren't supported in Flutter web:

- `enableCoppaControl`
- `disableCoppaControl`
- `trackingSessionEvents`
- `useAppSetIdForDeviceId`

#### Usage

Append the following Amplitude-JavaScript snippet into `web/index.html` in your Flutter project. The Amplitude-JavaScript version must be v8.12.0 and higher.

```js
<script type="text/javascript" defer>
   (function(e,t){var n=e.amplitude||{_q:[],_iq:{}};var r=t.createElement("script")
    ;r.type="text/javascript"
    ;r.integrity="sha384-UcvEbHmT0LE2ZB30Y3FmY3Nfw6puAKXz/LpCFuoywywYikMOr/519Uu1yNq2nL9w"
    ;r.crossOrigin="anonymous";r.async=true
    ;r.src="https://cdn.amplitude.com/libs/amplitude-8.12.0-min.gz.js"
    ;r.onload=function(){if(!e.amplitude.runQueuedFunctions){
    console.log("[Amplitude] Error: could not load SDK")}}
    ;var s=t.getElementsByTagName("script")[0];s.parentNode.insertBefore(r,s)
    ;function i(e,t){e.prototype[t]=function(){
    this._q.push([t].concat(Array.prototype.slice.call(arguments,0)));return this}}
    var o=function(){this._q=[];return this}
    ;var a=["add","append","clearAll","prepend","set","setOnce","unset","preInsert","postInsert","remove"]
    ;for(var c=0;c<a.length;c++){i(o,a[c])}n.Identify=o;var u=function(){this._q=[]
    ;return this}
    ;var l=["setProductId","setQuantity","setPrice","setRevenueType","setEventProperties"]
    ;for(var p=0;p<l.length;p++){i(u,l[p])}n.Revenue=u
    ;var d=["init","logEvent","logRevenue","setUserId","setUserProperties","setOptOut","setVersionName","setDomain","setDeviceId","enableTracking","setGlobalUserProperties","identify","clearUserProperties","setGroup","logRevenueV2","regenerateDeviceId","groupIdentify","onInit","logEventWithTimestamp","logEventWithGroups","setSessionId","resetSessionId","getDeviceId","getUserId","setMinTimeBetweenSessionsMillis","setEventUploadThreshold","setUseDynamicConfig","setServerZone","setServerUrl","sendEvents","setLibrary","setTransport"]
    ;function v(e){function t(t){e[t]=function(){
    e._q.push([t].concat(Array.prototype.slice.call(arguments,0)))}}
    for(var n=0;n<d.length;n++){t(d[n])}}v(n);n.getInstance=function(e){
    e=(!e||e.length===0?"$default_instance":e).toLowerCase()
    ;if(!Object.prototype.hasOwnProperty.call(n._iq,e)){n._iq[e]={_q:[]};v(n._iq[e])
    }return n._iq[e]};e.amplitude=n})(window,document);
</script>
```

## Troubleshooting

If you have issues turning on Bitcode in iOS follow Flutter's [documentation](https://github.com/flutter/flutter/wiki/Creating-an-iOS-Bitcode-enabled-app)

## More resources

If you have any problems with the SDK, [create a GitHub issue](https://github.com/amplitude/Amplitude-Flutter/issues/new) or submit a request on [Amplitude Help](https://help.amplitude.com/hc/en-us/requests/new).

--8<-- "includes/abbreviations.md"
