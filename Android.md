## Android Integration

The Mobility SDK has been implemented as an Android aar library.

1. Add the Mobility App maven details to the project-level `build.gradle`.

```java
allprojects {
    repositories {
        maven { url "https://jitpack.io" }
        maven {
            url "http://artifactory.cartrawler.com/artifactory/neo-external"
            credentials {
                username = "<supplied by CarTrawler>"
                password = "<supplied by CarTrawler>"
            }
        }
    }
}
```

2. Minimum Android SDK requirements:

```java
android {
    compileSdkVersion 28
    defaultConfig {
        minSdkVersion 16
        targetSdkVersion 26
    }
}
```

3. In the app-level `build.gradle` add the following dependencies:

```java
dependencies {
    compile 'com.cartrawler:mobility-app:+'
    compile 'com.facebook.react:react-native:+'
    compile 'com.github.ybq:Android-SpinKit:1.1.0'
}
```

4. Add an Activity tag to the app's `AndroidManifest.xml`, and add permission tags:

```xml
<manifest ...>
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

    <activity android:name="com.cartrawler.mobilitysdk.MobilitySDKActivity"/>
</manifest>
```

5. Initialise and render the Mobility App:

```java
import com.cartrawler.mobilitysdk.MobilitySDKManager;

// in the init method of your app, like the onCreate of the root activity. `this` should be an Android Activity.
MobilitySDKManager.init(this, "<partner-id>");

// To render the Mobility App view (for example on a button press). `this` should be an Android Activity.
MobilitySDKManager.showActivity(this);
```
