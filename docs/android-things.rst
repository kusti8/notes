Android Things
=================

Setup
---------

Add to `build.gradle (Module: app)`:

.. code-block:: gradle

    dependencies {
        ...
        provided 'com.google.android.things:androidthings:0.5.1-devpreview'
    }

Add to `AndroidManifest.xml`:

.. code-block:: xml

    <application ...>
        <uses-library android:name="com.google.android.things"/>
        ...
    </application>

Add to `AndroidManifest.xml`:

.. code-block:: xml

            <!-- Launch activity automatically on boot -->
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.IOT_LAUNCHER"/>
                <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>

To use Kotlin, go to activity, and *Code | Convert Java File to Kotlin File*.
Make sure the plugin is installed, if not:
*Go to File | Settings | Plugins | Install JetBrains plugin… and then search for and install Kotlin.*