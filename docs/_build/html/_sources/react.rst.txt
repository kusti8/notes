React Native - General
==========================

Setup
----------

Make a project: ``react-native init <name>`` and then start editing.

**Run ``npm install``**

Build by running ``react-native run-android`` and ``react-native start`` first.

Make sure ``*.keystore`` is in your .gitignore!!!

Essential Libraries
----------------------

Provides side menu, icons, and more: https://react-native-training.github.io/react-native-elements/

**Usable in Expo**

Provides side menu/drawer: https://github.com/root-two/react-native-drawer
**Usable in Expo**

Resizes images: https://github.com/bamlab/react-native-image-resizer

Android Emulator
------------------

On Arch: ``export ANDROID_EMULATOR_USE_SYSTEM_LIBS=1`` to .bashrc

Start emulator from /opt/android-sdk with ``emulator -avd "Pixel_API_23"``

Make sure to install from Android Studio with AVD API version 23.
Install Android Emulator package in SDK manager.


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

**Error when starting package server (react-native start) ENOSPC**
On Arch to fix:

.. code-block:: bash

    echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
    react-native start

**Error when running react-native-image-picker**

Right click in Android studio on the module and change API version to 23.
After, re-edit gradle file and change gradle version to 2.2.+
And edit gradle distro URL again also.
**Put all testCompiles on individual lines in build.gradle (app)**
