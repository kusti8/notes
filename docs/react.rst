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

Provides share menu: https://github.com/meedan/react-native-share-menu

Provides image and video Modals: https://github.com/tibbus/react-native-fit

Useful imports I've used:

.. code-block:: javascript

    import VideoPlayer from 'react-native-video-controls';
    import Spinner from 'react-native-spinkit';
    import Izzati from 'react-native-izzati';
    import RNFetchBlob from 'react-native-fetch-blob';
    import store from 'react-native-simple-store';
    import Swipeout from 'react-native-swipeout';
    import FitVideo from 'react-native-fit/fitVideo';

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

React Native Elements Header
------------------------------

Remember to use position: relative for the header:

.. code-block:: javascript

    <Header
        centerComponent={{ text: 'YTDownload' }}
        rightComponent={{ icon: 'add', onPress: () => this.addVideo() }}
        statusBarProps={{ barStyle: 'light-content' }}
        outerContainerStyles={{ backgroundColor: 'red', zIndex: 1, position: 'relative' }} /> // <---------------------

FlatList
----------

.. code-block:: javascript

    <List>
        <FlatList
            data={this.state.list}
            extraData={this.state}
            renderItem={(item) => {
                    swipeoutBtns = [
                        {
                            text: 'Delete',
                            backgroundColor: 'red',
                            onPress: () => {
                                this.deleteVideo(item.index)
                            }
                        }
                    ]
                return (
                <Swipeout right={swipeoutBtns}>
                    <ListItem
                        roundAvatar
                        avatar={{uri:JSON.parse(this.state.list[item.index]).thumbnail}}
                        title={JSON.parse(this.state.list[item.index]).title}
                        key={JSON.parse(this.state.list[item.index]).thumbnail}
                        onPress={() => {
                            this.watc
                            h(JSON.parse(this.state.list[item.index]))
                        }}
                    />
                </Swipeout>
            )
            }}
            keyExtractor={(item, index) => index}
        />
    </List>

Navigation Button - Top Right
--------------------------------

.. code-block:: javascript

  static navigationOptions = ({navigation}) => ({
    title: 'YTDownload',
    headerRight: <Icon name="add" style={{paddingRight: 15}} size={35} onPress={() => navigation.navigate('DownloadScreen')} />
  })