React Native - Image Picker
=============================

Install
-----------

.. code-block:: bash

    npm install react-native-image-picker@latest --save
    react-native link

**iOS**

1. For iOS 10+, Add the ``NSPhotoLibraryUsageDescription``, ``NSCameraUsageDescription``,
and ``NSMicrophoneUsageDescription`` (if allowing video) keys to your ``Info.plist``
with strings describing why your app needs these permissions.

**Android**

1. Update ``android/build.gradle``

.. code-block:: gradle

    buildscript {
        ...
        dependencies {
            classpath 'com.android.tools.build:gradle:2.2.+' // <- USE 2.2.+ version
        }
        ...
    }
    ...

2. Update URL in ``android/gradle/wrapper/gradle-wrapper.properties``
to ``distributionUrl=https\://services.gradle.org/distributions/gradle-2.14.1-all.zip``

3. Add permissions to AndroidManifest.xml

.. code-block:: xml

    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>

Usage
--------

.. code-block:: javascript

    import ImagePicker from 'react-native-image-picker';

    let options = {
        title: 'Select Image',
        storageOptions: {
            skipBackup: true,
            path: 'images'
        }
    };

    ImagePicker.showImagePicker(options, (response) => {
      console.log('Response = ', response);

      if (response.didCancel) {
        console.log('User cancelled image picker');
      }
      else if (response.error) {
        console.log('ImagePicker Error: ', response.error);
      }
      else if (response.customButton) {
        console.log('User tapped custom button: ', response.customButton);
      }
      else {
        let source = { uri: response.uri };

        // You can also display the image using data:
        // let source = { uri: 'data:image/jpeg;base64,' + response.data };

        this.setState({
          avatarSource: source
        });
      }
    });

Resources
-------------

- https://github.com/react-community/react-native-image-picker
- https://github.com/ivpusic/react-native-image-crop-picker
