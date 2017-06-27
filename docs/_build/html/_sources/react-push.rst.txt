React Native - Push Notifications
======================================

(Android only!!!)


I'm going to use Batch to make things easier.

Install
-----------

.. code-block:: bash

    npm install --save react-native-push-notification
    react-native link
    pip install pyfcm

Client Side
-----------------

Get the ID:


    We need to create a Google API project to enable GCM for our app.

    Visit the Google Developers Console.

    If you've never created a developer account there, you may need to fill out a few details.

    Click Create Project.

    Enter a project name, then click Create.

    New Google API project

    Wait a few seconds for the new project to be created. Then, view your Project ID and Project Number on the upper left of the project page.

    Project ID and number

    Make a note of the Project Number. You'll use it in your Android app client.

    Step Two - Enable GCM for Your Project
    Make sure your project is still selected in the Google Developers Console.

    In the sidebar on the left, select APIs & auth.

    Choose APIs.

    In the displayed list of APIs, turn the Google Cloud Messaging for Android toggle to ON. Accept the terms of service.

    Google Cloud Messaging for Android should now be in the list of enabled APIs for this project.

    Google Cloud Messaging for Android enabled

    In the sidebar on the left, select APIs & auth.

    Choose Credentials.

    Under Public API access, click Create new Key.

    Choose Server key.

    Enter your server's IP address.

    Server key IP

    Click Create.

    Copy the API KEY. You'll need to enter this on your server later.

    API KEY

Add the following to the client:

.. code-block:: javascript

    var PushNotification = require('react-native-push-notification');

    PushNotification.configure({

    // (optional) Called when Token is generated (iOS and Android)
    onRegister: function(t) {
        fetch('https://mywebsite.com/endpoint/', {
          method: 'POST',
          headers: {
            'Accept': 'application/json',
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({
            token: t
          })
        })
    },

    // (required) Called when a remote or local notification is opened or received
    onNotification: function(notification) {
        console.log( 'NOTIFICATION:', notification );
    },

    // ANDROID ONLY: GCM Sender ID (optional - not required for local notifications, but is need to receive remote push notifications)
    senderID: "YOUR GCM SENDER ID",

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

Server Side
--------------

That's it for the client side. Now for the server side:

.. code-block:: python

    from pyfcm import FCMNotification

    push_service = FCMNotification(api_key="<api-key>")

    # Single device
    registration_id = "<device registration_id>"
    message_title = "Uber update"
    message_body = "Hi john, your customized news for today is ready"
    result = push_service.notify_single_device(registration_id=registration_id, message_title=message_title, message_body=message_body)

    # Send to multiple devices by passing a list of ids.
    registration_ids = ["<device registration_id 1>", "<device registration_id 2>", ...]
    message_title = "Uber update"
    message_body = "Hope you're having fun this weekend, don't forget to check today's news"
    result = push_service.notify_multiple_devices(registration_ids=registration_ids, message_title=message_title, message_body=message_body)

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
