## IOS Integration

MobilitySDK has been implemented as an iOS dynamic Framework that is installed using CocoaPods.

1.  Add the following line to your `Podfile` and run `pod install` or `pod update`:

```
pod 'MobilitySDK', :git => 'https://github.com/cartrawler/mobility-sdk-pods'
```

Please note that you may need to install `git-lfs`. On Mac, install this with `brew install git-lfs`. Then, initialise your repo for `lfs` by using the command `git lfs install`.

2. Add the following property to your `Info.plist` file (This is necessary for the Mobility App's location based features):

```
    <key>NSLocationWhenInUseUsageDescription</key>
    <string>This app needs access to your location to provide ride hailing services.</string>
```

3. `MobilitySDKManager` should be initialised in your application delegate's init method or similar. This is required to send messages to the Mobility App such as profile updates.

```objectivec
#import <MobilitySDK/MobilitySDK.h>

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [[MobilitySDKManager sharedManager] initWithOptions:nil partnerId:@"<partner-id>"];
    ...
}
```

4. To display the Mobility App view, send the `openMobility` message to the instance of `MobilitySdkManager` in a `ViewController`.

```objectivec
#import <MobilitySDK/MobilitySDK.h>

// when you want to open mobility ViewController (where self is a ViewController):
[[MobilitySDKManager sharedManager] openMobility:self type:@"cross-sell" source:@"deeplink source" campaign:@"deeplink campaign" medium:@"deeplink medium"];
```

These paramaters will be used for tracking purposes, any string will do for testing.

5. To enable the opening and closing of Mobility App, the delegate protocol `MobilityDelegate` should be applied to the class which presents `MobilityViewController`.

Mandatory delegate methods:

- `-(void)shouldCloseMobilityApp` - Implement this method to enable the `MobilityViewController` to close itself.

* `-(void)shouldOpenMobilityApp` - Implement this method to enable the `MobilityViewController` to open itself (eg on click of a deep linking push notification). This is used extremely sparingly by the MobilitySdk, and only on user interaction.

```objectivec
@interface ViewController: UIViewController<MobilityDelegate>

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    [MobilitySDKManager sharedManager].delegate = self;
}

- (void)shouldCloseMobilityApp {
    [self dismissViewControllerAnimated:NO completion:nil];
};

- (void)shouldOpenMobilityApp {
    [[MobilitySDKManager sharedManager] openMobility:self type:@"cross-sell" source:@"<partnerName>-app" campaign:@"48hrs" medium:@"push"];
};
@end
```

The signature for `openMobility` is as follows:

```objectivec
- (void)openMobility:(UIViewController *)vc type:(NSString *)type source:(NSString *)source campaign:(NSString *)campaign medium:(NSString *)medium;
```

- The type field currently supports two values, "cross-sell" and "standard".

- The `source` is a required field which will describe the context in which the `MobilitySdk` is opened.
  In an app integration **this value will always be \<partnerName\>-app.**

- The `campaign` field denotes the marketing campaign the touchpoint is associated with. For example "48hrs" for a timed push notification, or "homepage-banner" for a mobility campaign on the homepage.

- The `medium` field describes the marketing channel the user came from. Examples include `inapp-link` and `push` for push notifications.

#

## Deeplinking

Passing deeplink data into the `MobilitySdk` would allow a user to navigate into the app to a particular screen.

### `standard`

The deeplink type `standard` is used for a regular opening of the SDK. This is implemented by running `openMobility` as usual:

```objectivec
[[MobilitySDKManager sharedManager] openMobility:<view-controller> type:@"<deeplink-type>" source:@"<source>" campaign:@"<campaign>" medium:@"<medium>"];
```

The first parameter `<view-controller>` is the view controller in which the SDK will be displayed. The rest of the paramaters are strings.

### Example:

```objectivec
[[MobilitySDKManager sharedManager] openMobility:self type:@"cross-sell" source:@"<partnerName>-app" campaign:@"48hrs" medium:@"push"];
```

### `cross-sell`

The deeplink type `cross-sell` is used for shortening a users search by pre-populating trip data from the flight details provided. To enable this functionality, you will need to pass in flight data using the following method:

```objectivec
//  Add this call after `initWithOptions` and before `openMobility`
[[MobilitySDKManager sharedManager] addFlight:@"<origin-IATA>" destinationIATA:@"<destination-IATA>" flightNumber:@"<flight-number>" flightDepartureDate:@"<flight-departure-date>" flightArrivalDate:@"<flight-arrival-date>" originAirportName:@"<origin-airport-name>" destinationAirportName:@"<destination-airport-name>" direction:@"<direction>"];
```

All of the parameters are strings except for `<direction>`.

`<direction>` is an **enum** with the values of either `DROPOFF_TO_ORIGIN` or `PICKUP_FROM_DESTINATION`. `DROPOFF_TO_ORIGIN` is used when the user is to be dropped off at the airport they are flying out of from the flight details provided. `PICKUP_FROM_DESTINATION` is used when the user is to be picked up at the airport they are landing at from the flight details provided.

`<flight-departure-date-time>` and `<flight-arrival-date-time>` are both in the format YYYY-MM-DDThh:mm:ss.s.

### Example:

```objectivec
[[MobilitySDKManager sharedManager] initWithOptions:nil partnerId:@"<partner-id>"];

...

[[MobilitySDKManager sharedManager] addFlight:@"DUB" destinationIATA:@"BCN" flightNumber:@"FR6875" flightDepartureDate:@"2021-12-31T06:15" flightArrivalDate:@"2021-12-31T09:40" originAirportName:@"Dublin" destinationAirportName:@"Barca Airport" direction:PICKUP_FROM_DESTINATION];

// OR

[[MobilitySDKManager sharedManager] addFlight:@"DUB" destinationIATA:@"BCN" flightNumber:@"FR6875" flightDepartureDate:@"2021-12-31T06:15" flightArrivalDate:@"2021-12-31T09:40" originAirportName:@"Dublin" destinationAirportName:@"Barca Airport" direction:DROPOFF_TO_ORIGIN];

...

// The type must be set here to cross-sell for the deeplink to be actioned
 [[MobilitySDKManager sharedManager] openMobility:self type:@"cross-sell" source:@"<partnerName>-app" campaign:@"48hrs" medium:@"push"];
```

#

## Additional Methods

The MobilitySDK exposes additional methods to override any default behaviour.

<b>Note:</b> Ensure to call an additional method after initialising the SDK with `initWithOptions` and before running `openMobility`.

### Passing User Data

Each value of `setUserData` is a string but if a value is not needed then `nil` can be passed in. The function to call is as follows:

```objectivec
[[MobilitySDKManager sharedManager] setUserData:@"<first-name>" lastName:@"<last-name>" email:@"<email>" mobileNumber: @"<mobile-number>"];
```

### Manually Removing User Data

To remove the user data manually (if a user logs out of their account on your app for instance), call `resetUserData`:

```objectivec
[[MobilitySDKManager sharedManager] resetUserData];
```

### Setting a Locale

A locale can be sent into the `MobilitySDK` as a string ("en-GB" for instance) as follows:

```objectivec
[[MobilitySDKManager sharedManager] setLocale:@"<locale>"];
```

List of supported locales:

<table>
<tr>
    <th>Code</th>
    <th>Languages</th>
</tr>
<tr>
    <td>bg-BG</td>
    <td>Bulgarian</td>
</tr>
<tr>
    <td>en-GB</td>
    <td>English</td>
</tr>
<tr>
    <td>en-US</td>
    <td>American English</td>
</tr>
<tr>
    <td>de-DE</td>
    <td>German</td>
</tr>
<tr>
    <td>es-ES</td>
    <td>Spanish</td>
</tr>
<tr>
    <td>fr-FR</td>
    <td>French</td>
</tr>
<tr>
    <td>it-IT</td>
    <td>Italian</td>
</tr>
<tr>
    <td>lt-LT</td>
    <td>Lithuanian</td>
</tr>

<tr>
    <td>hu-HU</td>
    <td>Hungarian</td>
</tr>
<tr>
    <td>nl-NL</td>
    <td>Dutch</td>
</tr>
<tr>
    <td>nb-NO</td>
    <td>Norwegian</td>
</tr>
<tr>
    <td>pt-PT</td>
    <td>Portuguese</td>
</tr>
</tr>
<tr>
    <td>pt-BR</td>
    <td>Brazilian Portuguese</td>
</tr>
</tr>
<tr>
    <td>pl-PL</td>
    <td>Polish</td>
</tr>
</tr>
<tr>
    <td>ro-RO</td>
    <td>Romanian</td>
</tr>
</tr>
<tr>
    <td>sv-SE</td>
    <td>Swedish</td>
</tr>
</tr>
<tr>
    <td>uk-UA</td>
    <td>Ukrainian</td>
</tr>
<tr>
    <td>ru-RU</td>
    <td>Russian</td>
</tr>
</table>

### Passing the flight booking reference

A (flight) booking reference can be sent into the `MobilitySDK` as a string ("bookRef123" for instance) as follows:

```java
[[MobilitySDKManager sharedManager] setBookingReference:@"<bookingReference>"];
```

### Passing the trip type

A (flight) trip type can be sent into the `MobilitySDK` as a `TripTypeEnum` as follows:

```java
[[MobilitySDKManager sharedManager] setTripType:LEISURE];
```

- The `TripTypeEnum` enum currently has two supported values: `LEISURE` and `BUSINESS`.

### Passing the number of passengers

The number of passengers (for the flight) can be sent to the `MobilitySDK` as 3 numbers (`adults`, `children` and `infants`, use `0` if there is no passenger in this category) as follows:

```java
[[MobilitySDKManager sharedManager] setPassengers:<adults> children:<children> infants:<infants>];
[[MobilitySDKManager sharedManager] setPassengers:2 children:1 infants:0];
```
