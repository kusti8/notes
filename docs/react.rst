React Native - General
==========================

Setup
----------

Make a project: ``react-native init`` and then start editing.

Build by running ``react-native run-android`` and ``react-native start`` first.

Make sure ``*.keystore`` is in your .gitignore!!!

Essential Libraries
----------------------

Provides side menu, icons, and more: https://react-native-training.github.io/react-native-elements/
**Usable in Expo**


Network POST w/ parameters
-----------------------------

.. code-block:: javascript

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

Install Standalone
------------------------

**React Native**

Android: https://facebook.github.io/react-native/docs/signed-apk-android.html
iOS: https://facebook.github.io/react-native/docs/running-on-device.html#building-your-app-for-production

**Expo**

https://docs.expo.io/versions/v18.0.0/guides/building-standalone-apps.html

Debugging
---------------------

**Error when starting package server (react-native start)**
On Arch to fix:

.. code-block:: bash

    echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
    react-native start
