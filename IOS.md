## IOS Integration

MobilitySDK has been implemented as iOS dynamic Framework that is installed using CocoaPods.

1.  Add the following line to your `Podfile` and run `pod install`:

```
pod 'MobilitySDK', :git => 'https://github.com/cartrawler/mobility-sdk-pods'
```

Please note that you may need to install `git-lfs`. On Mac, install this with `brew install git-lfs`. Then, initialise your repo for `lfs` by using the command `git lfs install`.

2. Add the following property to your `Info.plist` file (This is necessary for the Mobility App's location based features):

```
    <key>NSLocationWhenInUseUsageDescription</key>
    <string>This app needs access to your location to provide ride hailing services.</string>
```

3. `MobilitySDKManager` should be initialised in your application delegate's init method or similar. This is required to send messages to the Mobility App such as Profile Updates.

```objectivec
#import <MobilitySDK/MobilitySDK.h>

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [[MobilitySDKManager sharedManager] initWithOptions:launchOptions partnerId:@"<partner-id>"];
    ...
}
```

4. To display the Mobility App view, send the `getMobilitySdkVeiw:initialProperties` message to the instance of MobilitySdkManager.

```objectivec
    MobilityViewController * vc = [[MobilitySDKManager sharedManager] getMobilitySdkView:nil];
    [self presentViewController:(ViewController *)vc animated:YES completion:nil];
```

5. To enable the oepning and closing of Mobility App, the delegate protocol `MobilityDelegate` should be applied to the class which presents `MobilityViewController`. Then implement the method `-(void)closeMobilityApp`, and dismiss `MobilityViewController` in the implementation. For example:

```objectivec
@interface ViewController: UIViewController<MobilityDelegate>

@end

@implementation ViewController

- (void)closeMobilityApp {
    [self dismissViewControllerAnimated:NO completion:nil];
};

- (void)openMobilityApp {
    MobilityViewController *vc = [[MobilitySDKManager sharedManager] getMobilitySdkView:nil];
    [self presentViewController:(ViewController *)vc animated:YES completion:nil];
};
```

## Mobility Events

**NOTE: Events are a no-op up to version 0.0.13, they will become active in a later version**

The Mobility SDK has exposed some methods for sending events to the Mobility App. These can be used to update the user profile within the Mobility App.

Import the Mobility SDK to access the MobilityEvents object.

```objectivec
#import <MobilitySDK/MobilitySDK.h>
```

After instantiating the `MobilityEvents` object, call the public methods to send events to the Mobility App.

```objectivec
    MobilityEvents *mobilityEvents = [MobilityEvents allocWithZone:nil];
    [mobilityEvents updateUserProfile:@"353123456789" firstName:@"John" lastName:@"Smith" email:@"johnsmith@gmail.com"];
    [mobilityEvents updateUserPreferences:@"EUR" language:@"en_gb"];
    [mobilityEvents updatePartnerProgramWithToken:@"jk48fuir99j4rk4ti0"];
    [mobilityEvents updatePartnerProgram:@"<username>" password:@"password1"];
    [mobilityEvents updatePartnerProgram:@"<username>"];
```

### updateUserProfile

Call `updateUserProfile` after login or registration in the parent app to sync user details across both applications.

```objectivec
- (void) updateUserProfile:(NSString *)userMobile
                            firstName:(NSString *)firstName
                             lastName:(NSString *)lastName
                                email:(NSString *)email;
```

### updateUserPreferences

Call `updateUserPreferences` after login, registration or when user preferences are updated to keep the Mobility App's settings in sync with the parent app.

```objectivec
- (void) updateUserPreferences:(NSString *)currency
                      language:(NSString *)language;
```

### updatePartnerProgram

Call `updatePartnerProgram` when a user logs into the loyalty program. The Mobility App can use these details to update the user's loyalty balance after transactions.

```objectivec
- (void) updatePartnerProgram:(NSString *)userName
                     password:(NSString *)password;

- (void) updatePartnerProgram:(NSString *)userName;
```

### updatePartnerProgramWithToken

Call `updatePartnerProgramWithToken` if there is a tokenised version of the user's loyalty program details. The Mobility App can use these details to update the user's loyalty balance after transactions.

```objectivec
- (void) updatePartnerProgramWithToken:(NSString *)token;
```
