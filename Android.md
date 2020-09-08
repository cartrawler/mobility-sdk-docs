# Android Integration

## Basic Mobility Integration

The Mobility SDK has been implemented as an Android aar library.

1. Add the Mobility App maven details to the project-level `build.gradle`.

```java
allprojects {
    repositories {
        maven { url "https://jitpack.io" }
        maven {
            url "http://artifactory.cartrawler.com/artifactory/neo-external"
            credentials {
                username = "<artifactory-user>"
                password = "<artifactory-password>"
            }
        }
    }
}
```

2. Minimum Android SDK requirements:

```java
android {
    compileSdkVersion 29
    defaultConfig {
        minSdkVersion 21
        targetSdkVersion 29
    }
}
```

3. In the app-level `build.gradle` add the following dependencies:

```java
dependencies {
    implementation('com.cartrawler:mobility-app:+@aar') {
        transitive = true
        // if app is using androidx include the following line.
        exclude group: 'androidx.core', module: '*'
    }
}
```

4. Add an Activity tag to the app's `AndroidManifest.xml`:

```xml
<!--we only support portrait mode-->

<manifest ...>
    <activity
            android:name="com.cartrawler.mobilitysdk.MobilitySDKActivity"
            android:screenOrientation="portrait" />
</manifest>
```

5. Initialise and render the Mobility App:

```java
import com.cartrawler.mobilitysdk.MobilitySDKManager;

// in the init method of your app, like the onCreate of the root activity. `this` should be an Android Activity.
MobilitySDKManager.init(this, "<partner-id>");

// To render the Mobility App view (for example on a button press). `this` should be an Android Activity.
MobilitySDKManager.showMobility(this, DeeplinkType.STANDARD, <source>, <campaign>, <medium>);
```

- The signature of the `showMobility` method is as follows:

```java
showMobility(Activity activity, DeeplinkType type, String source, String
            campaign, String medium)
```

- The DeeplinkType enum currently has two supported values:

```java
public enum DeeplinkType {
    CROSS_SELL("cross-sell"),
    STANDARD("standard");
}
```

- The `source` is a required field which will describe the context in which the `MobilitySdk` is opened.
  In an app integration **this value will always be \<partnerName\>-app.**

- The `campaign` field denotes the marketing campaign the touchpoint is associated with. For example "48hrs" for a timed push notification, or "homepage-banner" for a mobility campaign on the homepage.

- The `medium` field describes the marketing channel the user came from. Examples include `inapp-link` and `push` for push notifications.

#

## Additional Methods

<b>The MobilitySDK exposes additional methods to override any default behaviour.

Note: Ensure to call an additional method, outlined below, after initialising the SDK with `initWithOptions` and before running `openMobility`</b>

### Passing User Data

Each value of `setUserData` is a string but if a value is not needed then `null` can be passed in. The function to call is as follows:

```java
 MobilitySDKManager.setUserData("<first-name>", "<last-name>", "<email>", "<mobile-number>");
```

### Manually Removing User Data

To remove the user data manually (if a user logs out of their account on your app for instance), call `resetUserData`:

```java
MobilitySDKManager.resetUserData();
```
