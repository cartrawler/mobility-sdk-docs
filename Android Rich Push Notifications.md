## Android Rich Push Notification Support

### Using Firebase messaging

We require your `applicationId` so we can register to your app to support Firebase notifications and create a `<mobility-sender-id>`.

Once you have our `<mobility-sender-id>` copy this method to retrieve a Firebase token specifically related to the `<mobility-sender-id>` and pass it to the Mobility SDK with `MobilitySDKManager.setPushToken(token)`.

```java
import com.cartrawler.mobilitysdk.MobilitySDKManager;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.messaging.FirebaseMessaging;

private void setMobilityPushToken() {
    // getToken() needs to be run on a background thread because it will break if ran on the main thread
    Thread thread = new Thread(new Runnable() {
        @Override
        public void run() {
            try {
                // Enter in the <mobility-sender-id> that we will provide
                String token = FirebaseInstanceId.getInstance().getToken("<mobility-sender-id>", FirebaseMessaging.INSTANCE_ID_SCOPE);
                // Pass the Firebase token to the Mobility SDK
                MobilitySDKManager.setPushToken(token);
            } catch (Exception e) {
                e.printStackTrace();
            }

        }
    });
    thread.start();
}
```

Call `setMobilityPushToken()` anywhere after you call init on Mobility SDK to pass the Firebase `token` to the Mobility SDK.

```java
setMobilityPushToken();
```
