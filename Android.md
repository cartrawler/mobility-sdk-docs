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
    compile 'com.github.ybq:Android-SpinKit:1.2.0'
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

## Mobility Events

The Mobility SDK has exposed some methods for sending events to the Mobility App. These can be used to update the user profile within the Mobility App.

The event methods are exposed on the MobilitySDKManager object. This object is a singleton, so just import the object and call the following methods.

```java
import com.cartrawler.mobilitysdk.MobilitySDKManager;
```

```java
MobilitySDKManager.updateUserProfile("John", "Smith", "johnsmith@gmail.com", "35312345689");
MobilitySDKManager.updateUserPreferences("EUR", "en_GB");
MobilitySDKManager.updatePartnerProgram("user1234", "password1234");
MobilitySDKManager.updatePartnerProgram("user1234");
MobilitySDKManager.updatePartnerProgramWithToken("token-rw4789yfuh-g85kj");
```

### updateUserProfile

Call `updateUserProfile` after login or registration in the parent app to sync user details across both applications.

```java
public static void updateUserProfile(String firstName, String lastName, String email, String phoneNumber)
```

### updateUserPreferences

Call `updateUserPreferences` after login, registration or when user preferences are updated to keep the Mobility App's settings in sync with the parent app.

```java
public static void updateUserPreferences(String currency, String language)
```

### updatePartnerProgram

Call `updatePartnerProgram` when a user logs into the loyalty program. The Mobility App can use these details to update the user's loyalty balance after transactions.

```java
public static void updatePartnerProgram(String userName, String password)
public static void updatePartnerProgram(String userName)
```

### updatePartnerProgramWithToken

Call `updatePartnerProgramWithToken` if there is a tokenised version of the user's loyalty program details. The Mobility App can use these details to update the user's loyalty balance after transactions.

```java
public static void updatePartnerProgramWithToken(String token)
```
