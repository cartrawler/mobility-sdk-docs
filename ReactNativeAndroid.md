## React Native Android Integration

The Mobility SDK has been implemented as an Android aar library.

1.  Add the `react-native-mobility-sdk` node_module with `yarn add` or `npm install`.

**NB: This module is yet to be publicly hosted.**

2. Link this native dependency.

- Manual linking:

```groovy
include ':react-native-mobility-sdk'
project(':react-native-mobility-sdk').projectDir = new File(rootProject.projectDir, '../node_modules/@cartrawler/react-native-mobility-sdk/android')
```

- Auto linking:
  In versions of RN after `0.60`, gradle should automatically link your dependencies.

3. Add `react-native-mobility-sdk` to your app level `build.gradle` dependencies.

```groovy
implementation project(':react-native-mobility-sdk')
```

4. Add the Mobility App maven details to the project-level `build.gradle`.

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

5. Minimum Android SDK requirements:

```java
android {
    compileSdkVersion 28
    defaultConfig {
        minSdkVersion 19
        targetSdkVersion 26
    }
}
```

6. In the app-level `build.gradle` add the following dependencies:

```java
dependencies {
    implementation('com.cartrawler:mobility-app:+@aar') {
        transitive = true
        // if app is using androidx include the following line.
        exclude group: 'androidx.core', module: '*'
    }
}
```

7. Add an Activity tag to the app's `AndroidManifest.xml`:

```xml
<manifest ...>
    <activity android:name="com.cartrawler.mobilitysdk.MobilitySDKActivity"/>
</manifest>
```

8. Initialise and render the Mobility App:

```java
import com.cartrawler.mobilitysdk.MobilitySDKManager;

// in the init method of your app, like the onCreate of the root activity. `this` should be an Android Activity.
MobilitySDKManager.init(this, "<partner-id>");
```

9. To display the `Mobility` view from JS, import `react-native-mobility-sdk` from `NativeModules`

```javascript
import { NativeModules } from "react-native";
const { RNMobilitySdk } = NativeModules;
```

and call the `startMobility` method.

```javascript
RNMobilitySdk.startMobility();
```
