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
MobilitySDKManager.showMobility(this, DeeplinkType.STANDARD, "source", "campaign", "medium");
```
