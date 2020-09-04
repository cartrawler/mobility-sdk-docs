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

The deeplink type `cross-sell` is used for opening a specific screen in the SDK. To enable this functionality, you will need to pass in flight data using the following method:

```objectivec
//  Add this call after `initWithOptions` and before `openMobility`
[[MobilitySDKManager sharedManager] addFlight:@"<origin-IATA>" destinationIATA:@"<destination-IATA>" flightNumber:@"<flight-number>" flightDate:@"<flight-date-time>"];
```

All of the paramaters are strings. The `<flight-date-time>` is in the format YYYY-MM-DDThh:mm:ss.s.

### Example:

```objectivec
[[MobilitySDKManager sharedManager] initWithOptions:nil partnerId:@"<partner-id>"];

...

[[MobilitySDKManager sharedManager] addFlight:@"LGW" destinationIATA:@"DUB" flightNumber:@"FR121" flightDate:@"2020-09-11T22:08:50.001"];
...

// The type must be set here to cross-sell for the deeplink to be actioned
 [[MobilitySDKManager sharedManager] openMobility:self type:@"cross-sell" source:@"<partnerName>-app" campaign:@"48hrs" medium:@"push"];
```

#

## Additional Methods
