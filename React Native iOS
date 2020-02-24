## React Native iOS Integration

The `MobilitySDK` has been implemented as an iOS dynamic Framework that is installed using CocoaPods and `npm` or `yarn`.

1.  Add the `react-native-mobility-sdk` node_module with `yarn add` or `npm install`.

**NB: This module is yet to be publicly hosted.**

2.  Add the following lines to your `Podfile` and run `pod install` or `pod update`:

```
pod 'MobilitySDK', :git => 'https://github.com/cartrawler/mobility-sdk-pods'

pod 'RNMobilitySdk', :path => '../node_modules/react-native-mobility-sdk/ios/RNMobilitySdk.podspec'
```

**NB: This may take a few minutes**

Please note that you may need to install `git-lfs`. On Mac, install this with `brew install git-lfs`. Then, initialise your repo for `lfs` by using the command `git lfs install`.

3. Add the following property to your `Info.plist` file (This is necessary for the Mobility App's location based features):

```
    <key>NSLocationWhenInUseUsageDescription</key>
    <string>This app needs access to your location to provide ride hailing services.</string>
```

4. To enable the opening and closing of Mobility App, the delegate protocol `MobilityDelegate` should be applied to the class which presents `MobilityViewController`.

In the `AppDelegate.h` file, import `MobilitySDK`

```
#import <MobilitySDK/MobilitySDK.h>
```

and add `MobilityDelegate` to the definition of your `AppDelegate` class.

```objectivec
@interface AppDelegate : UIResponder <UIApplicationDelegate, RCTBridgeDelegate, MobilityDelegate>
```

Then, add a property that will represent the `MobilityViewController`:

```objectivec
@property (nonatomic, strong) MobilityViewController *mvc;
```

5. In the implementation file of the AppDelegate, `AppDelegate.m`, initialise the `MobilitySDK` in the `- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions` method.

```objectivec
#import <MobilitySDK/MobilitySDK.h>

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [[MobilitySDKManager sharedManager] initWithOptions:nil partnerId:@"<partner-id>";
}
```

6. Implement the following mandatory delegate methods:

- `-(void)shouldCloseMobilityApp` - Implement this method to enable the `MobilityViewController` to close itself.

```objectivec
- (void)shouldCloseMobilityApp {
    self.window.rootViewController = self.vc;
    [self.mvc dismissViewControllerAnimated:NO completion:nil];
}
```

- `-(void)startMobilitySdk` - Implement this method to enable the `MobilityViewController` to open itself (eg on click of a deep linking push notification). This is used extremely sparingly by the `MobilitySDK`, and only on user interaction.

```objectivec
-(void)startMobilitySdk{
  self.mvc = [[MobilitySDKManager sharedManager] getMobilitySdkView: nil];
  self.window.rootViewController = self.mvc;
  [self.window makeKeyAndVisible];
}
```

7. To display the `Mobility` view from JS, import `react-native-mobility-sdk` from `NativeModules`

```javascript
import { NativeModules } from "react-native";
const { RNMobilitySdk } = NativeModules;
```

and call the `startMobility` method.

```javascript
RNMobilitySdk.startMobility();
```
