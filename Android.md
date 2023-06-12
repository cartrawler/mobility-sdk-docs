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
    compileSdkVersion 31
    defaultConfig {
        minSdkVersion 21
        targetSdkVersion 31
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

// in the init method of your app, like the onCreate of the root activity. `this` should be an Android Activity. `init` should only be called once.
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

## Deeplinking

Passing deeplink data into the `MobilitySdk` would allow a user to navigate into the app to a particular screen.

### `DeeplinkType.STANDARD`

The deeplink type `STANDARD` is used for a regular opening of the SDK. This is implemented by running `MobilitySDKManager.showMobility` as usual:

```java
showMobility(<activity>, <deeplink-type>, <source>, <campaign>, <medium>)
```

### Example:

```java
MobilitySDKManager.showMobility(this, DeeplinkType.STANDARD, "partner", "standard","menu");
```

### `DeeplinkType.CROSS_SELL`

The deeplink type `cross-sell` is used for shortening a users search by pre-populating trip data from the flight details provided. To enable this functionality, you will need to pass in flight data using the following method:

```java
//  Add this call after `MobilitySDKManager.init` and before `MobilitySDKManager.showMobility`
MobilitySDKManager.addFlight("<origin-IATA>", "<destination-IATA>", "<flight-number>", "<flight-departure-date-time>", "<flight-arrival-date-time>", "<origin-airport-name>", "<destination-airport-name>","<direction>");
```

All of the parameters are strings except for `<direction>`.

`<direction>` is an **enum** with the values of either `DeeplinkDirection.DROPOFF_TO_ORIGIN` or `DeeplinkDirection.PICKUP_FROM_DESTINATION`.

`DeeplinkDirection.DROPOFF_TO_ORIGIN` is used when the user needs a ride to the airport they are flying out of, using the flight details provided.

`DeeplinkDirection.PICKUP_FROM_DESTINATION` is used when the user needs a ride from the airport they are landing at, using the flight details provided.

`<flight-departure-date-time>` and `<flight-arrival-date-time>` are both in the format YYYY-MM-DDThh:mm:ss.s.

### Example:

```java
MobilitySDKManager.init(this, "<partner-id>");

...

MobilitySDKManager.addFlight("DUB", "BCN", "FR6875", "2021-12-31T06:15:00.001", "2021-12-31T09:40:00.001", "Dublin Airport (Terminal 1)", "Barcelona Airport", DeeplinkDirection.DROPOFF_TO_ORIGIN);

// OR

MobilitySDKManager.addFlight("DUB", "BCN", "FR6875", "2021-12-31T06:15:00.001", "2021-12-31T09:40:00.001", "Dublin Airport (Terminal 1)", "Barcelona Airport", DeeplinkDirection.PICKUP_FROM_DESTINATION);
...

// DeeplinkType of CROSS_SELL must be set here for the deeplink to be actioned
MobilitySDKManager.showMobility(this, DeeplinkType.CROSS_SELL, "partner", "standard","menu");
```

#

## Additional Methods

The MobilitySDK exposes additional methods to override any default behaviour.

<b>Note:</b> Ensure to call an additional method after initialising the SDK with `init` and before running `showMobility`.

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

### Setting a Locale

A locale can be sent into the `MobilitySDK` as a string ("en-GB" for instance) as follows:

```java
MobilitySDKManager.setLocale("<locale>");
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

A flight booking reference can be sent to the `MobilitySDK` as a string ("bookRef123" for instance) as follows:

```java
MobilitySDKManager.setBookingReference("<bookingReference>");
```

### Passing the trip type

A (flight) trip type can be sent into the `MobilitySDK` as a `TripType` as follows:

```java
MobilitySDKManager.setTripType(TripType <tripType>);
```

- The `TripType` enum currently supports two values:

```java
public enum TripType {
    LEISURE("LEISURE"),
    BUSINESS("BUSINESS");
}
```

### Passing the number of passengers

The number of passengers (for the flight) can be sent to the `MobilitySDK` as 3 numbers (Use `0` if there is no passenger in this category) as follows:

```java
MobilitySDKManager.setPassengers(<adults>, <children>, <infants>);
MobilitySDKManager.setPassengers(2, 1, 0);
```
