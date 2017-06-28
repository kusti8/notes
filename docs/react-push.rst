React Native - Push Notifications
======================================


I'm going to use Batch to make things easier.

Install
-----------

.. code-block:: bash

    npm install --save react-native-push-notification
    react-native link
    pip install python-gcm apns

Client Side
-----------------

Get the ID:

**Android**

https://developers.google.com/mobile/add


**iOS**

.. code-block:: bash

    sudo gem install fastlane
    fastlane pem

Add the following to the client:

.. code-block:: javascript

    var PushNotification = require('react-native-push-notification');

    // THIS PART GOES OUTSIDE ALL CLASSES !!!!!!!!!!
    export function postForm(path, form) {
      const str = [];
      for (let p in form) {
        str.push(encodeURIComponent(p) + "=" + encodeURIComponent(form[p]));
      }
      const body = str.join("&");
      const req = {
        method: 'post',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        },
        body
      };
      return fetch(path, req);
    }

    // THIS PART GOES IN constructor(props) !!!!!!!!!!!
    PushNotification.configure({
        // (optional) Called when Token is generated (iOS and Android)
        onRegister: function(t) {
            console.log(t)
            postForm('http://192.168.0.120:5020/', {token: t.token})
        },

        // (required) Called when a remote or local notification is opened or received
        onNotification: function(notification) {
            console.log( 'NOTIFICATION:', notification );
        },

        // ANDROID ONLY: GCM Sender ID (optional - not required for local notifications, but is need to receive remote push notifications)
        senderID: "",

        // IOS ONLY (optional): default: all - Permissions to register.
        permissions: {
            alert: true,
            badge: true,
            sound: true
        },

        // Should the initial notification be popped automatically
        // default: true
        popInitialNotification: true,

        /**
          * (optional) default: true
          * - Specified if permissions (ios) and token (android and ios) will requested or not,
          * - if not, you must call PushNotificationsHandler.requestPermissions() later
          */
        requestPermissions: true,
    });

**On Android, add the following to AndroidManifest.xml**

.. code-block:: xml

        <uses-permission android:name="android.permission.WAKE_LOCK" />
        <permission
            android:name="${applicationId}.permission.C2D_MESSAGE"
            android:protectionLevel="signature" />
        <uses-permission android:name="${applicationId}.permission.C2D_MESSAGE" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

And then in the application brackets:

.. code-block:: xml

    <application ....>
        <receiver
            android:name="com.google.android.gms.gcm.GcmReceiver"
            android:exported="true"
            android:permission="com.google.android.c2dm.permission.SEND" >
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="${applicationId}" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationPublisher" />
        <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationBootEventReceiver">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
            </intent-filter>
        </receiver>
        <service android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationRegistrationService"/>
        <service
            android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationListenerService"
            android:exported="false" >
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            </intent-filter>
        </service>

Server Side
--------------

That's it for the client side. Now for the server side:

.. code-block:: python

    from gcm import *
    from time import sleep
    #from apns import APNs, Frame, Payload
    from izzati import Backend

    gcm = GCM("AIzaSyBUXAxT9Nynnx6ZbQfjnZL9-Ep3d37wBxY")
    #apns = APNs(use_sandbox=True, cert_file='cert.pem')

    tokens = []

    def callback(data, files):
        global gcm, tokens
        print(data)
        token = data['token']
        tokens.append(token)
        tokens = list(set(tokens))
        data = {'title': 'Test Notifications', 'message': 'Hello!'}
        #payload = Payload(alert="Hello World!", sound="default", badge=1, mutable_content=True)

        #apns.gateway_server.send_notification(token, payload)
        gcm.plaintext_request(registration_id=token, data=data)
        sleep(10)
        gcm.plaintext_request(registration_id=token, data=data)

    b = Backend(callback)
    b.run()


For more info on the data input:

https://developers.google.com/cloud-messaging/http-server-ref#notification-payload-support

That's it!

Expo - Push Notifications
===========================

Install
--------------

Install Expo as you usually would.

**This will only work on a non-ejected Expo project!!!**

Install the Python server side.

.. code-block:: bash

    sudo pip install exponent_server_sdk izzati

Client Side
----------------

.. code-block:: javascript

    import { Permissions, Notifications } from 'expo';

    const PUSH_ENDPOINT = 'https://your-server.com/users/push-token';

    async function registerForPushNotificationsAsync() {
      const { existingStatus } = await Permissions.getAsync(Permissions.REMOTE_NOTIFICATIONS);
      let finalStatus = existingStatus;

      // only ask if permissions have not already been determined, because
      // iOS won't necessarily prompt the user a second time.
      if (existingStatus !== 'granted') {
        // Android remote notification permissions are granted during the app
        // install, so this will only ask on iOS
        const { status } = await Permissions.askAsync(Permissions.REMOTE_NOTIFICATIONS);
        finalStatus = status;
      }

      // Stop here if the user did not grant permissions
      if (finalStatus !== 'granted') {
        return;
      }

      // Get the token that uniquely identifies this device
      let t = await Notifications.getExponentPushTokenAsync();

      // POST the token to our backend so we can use it to send pushes from there
      return fetch(PUSH_ENDPOINT, {
        method: 'POST',
        headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          token: t
        }),
      });
    }

The Server
-----------------

.. code-block:: python

    from exponent_server_sdk import DeviceNotRegisteredError
    from exponent_server_sdk import PushClient
    from exponent_server_sdk import PushMessage
    from exponent_server_sdk import PushResponseError
    from exponent_server_sdk import PushServerError
    from requests.exceptions import ConnectionError
    from requests.exceptions import HTTPError

    from izzati import Backend


    # Basic arguments. You should extend this function with the push features you
    # want to use, or simply pass in a `PushMessage` object.
    tokens = []

    def send_push_message(token, message, extra=None):
        try:
            response = PushClient().publish(
                PushMessage(to=token,
                            body=message,
                            data=extra))
        except PushServerError as exc:
            # Encountered some likely formatting/validation error.
            raise
        except (ConnectionError, HTTPError) as exc:
            # Encountered some Connection or HTTP error - retry a few times in
            # case it is transient.
            raise self.retry(exc=exc)

        try:
            # We got a response back, but we don't know whether it's an error yet.
            # This call raises errors so we can handle them with normal exception
            # flows.
            response.validate_response()
        except DeviceNotRegisteredError:
            # Mark the push token as inactive
            from notifications.models import PushToken
            PushToken.objects.filter(token=token).update(active=False)
        except PushResponseError as exc:
            raise self.retry(exc=exc)

    def register_token(data, files):
        global tokens
        tokens.append(data['token'])

    b = Backend(register_token)
    b.run()
