React Native - Image Resize
==============================

Install
-----------

.. code-block:: bash

    npm install --save react-native-image-resizer
    react-native link react-native-image-resizer

Note: on latest versions of React Native, you may have an error during the
Gradle build on Android
(``com.android.dex.DexException: Multiple dex files define Landroid/support/v7/appcompat/R$anim``).
Run ``cd android && ./gradlew clean`` to fix this.

Usage
--------

.. code-block:: javascript

    import ImageResizer from 'react-native-image-resizer';

    let compressFormat = 'JPEG' // or 'PNG'
    let quality = 80 // out of 100

    ImageResizer.createResizedImage(imageUri, newWidth, newHeight, compressFormat, quality).then((resizedImageUri) => {
      // resizeImageUri is the URI of the new image that can now be displayed, uploaded...
    }).catch((err) => {
      // Oops, something went wrong. Check that the filename is correct and
      // inspect err to get more details.
    });

**WARNING:** On Android, ``file:`` will be prepended to the returned
    string. This allows it to be displayed as an image, but it also
    means you `won’t be able to access the file directly`_ when using a
    utility like ``fs``. To do so, you can simply use
    ``rawPath = originalPath.replace('file:', '')`` to get the raw path.

    +----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | Option         | Description                                                                                                                                                                             |
    +================+=========================================================================================================================================================================================+
    | path           | Path of image file, or a base64 encoded image string prefixed with 'data:image/imagetype;base64,' where imagetype is jpeg or png.                                                       |
    +----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | maxWidth       | Image max width (ratio is preserved)                                                                                                                                                    |
    +----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | maxHeight      | Image max height (ratio is preserved)                                                                                                                                                   |
    +----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | compressFormat | JPEG, PNG, or WEBP (Android only)                                                                                                                                                       |
    +----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | quality        | A number between 0 and 100 used for JPEG compression                                                                                                                                    |
    +----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | rotation       | Rotation to apply to the image, in degrees, for android. On iOS, rotation is limited (and rounded) to multiples of 90 degrees.                                                          |
    +----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | outputPath     | The resized image path. If null, resized image will be stored in cache folder. To set outputPath make sure to add option for rotation too (if no rotation is needed, just set it to 0). |
    +----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Example
-----------

.. code-block:: javascript

    ImageResizer.createResizedImage(datauri, 640, 480, 'JPEG', 80).then((resizedImageUri) => {
        let i = new Izzati("http://192.168.1.17:5020/")
        i.send({text: {hello: 'me'}, file: {uri: resizedImageUri, filename: 'photo.jpg'}, response: {base64: false}}, (out) => {
            this.setState(previous => {
                return {uri: i.prefixPath(out.path)}
          })
        })
    }).catch((err) => {
        console.log(err)
    })

.. _won’t be able to access the file directly: https://github.com/bamlab/react-native-image-resizer/issues/50
