React Native - Share Extension
------------------------------------

Install
---------------

Follow this for Android: https://github.com/meedan/react-native-share-menu

And this for iOS: https://github.com/alinz/react-native-share-extension # Really hard to get working.

Remember to link libReactNativeShareExtension.a to the main target in iOS.

Script
---------------

.. code-block:: javascript

    // share.ios.js

    import React, { Component } from 'react';
    import ShareExtension from 'react-native-share-extension';

    export default class Share {
        static getSharedText(callback) {
            ShareExtension.data().then((type, value) => {
                callback(value)
            })
        }
        static clearSharedText() {
        }
    }

.. code-block:: javascript

    // share.android.js

    import React, { Component } from 'react';
    import ShareMenu from 'react-native-share-menu';

    export default class Share {
        static getSharedText(callback) {
            ShareMenu.getSharedText(callback)
        }
        static clearSharedText() {
            ShareMenu.clearSharedText()
        }
    }