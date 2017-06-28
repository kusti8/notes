React Native - Sidebar
=========================

Install
--------------

``npm install --save react-native-drawer``

Code
----------

https://github.com/root-two/react-native-drawer

.. code-block:: javascript

    import React, { Component } from 'react';
    import {
      AppRegistry,
      StyleSheet,
      Text,
      View,
      Button
    } from 'react-native';
    import Drawer from 'react-native-drawer'

    class ControlPanel extends Component {
        render() {
            return (
                <View>
                    <Text>Hi</Text>
                    <Button onPress={this.props.close} title="Close" />
                </View>
            )
        }
    }

    export default class Navigation extends Component {
        closeControlPanel = () => {
          this._drawer.close()
        };
        openControlPanel = () => {
          this._drawer.open()
        };
      render () {
        return (
            <Drawer
            ref={(ref) => this._drawer = ref}
            openDrawerOffset={0.6}
            type={"overlay"}
            content={<ControlPanel close={this.closeControlPanel}/>}
            >
                <View style={{backgroundColor: 'red'}}>
                    <Text>Hello</Text>
                    <Button onPress={this.openControlPanel} title="Test" />
                </View>
          </Drawer>
        )
      }
    }

    AppRegistry.registerComponent('Navigation', () => Navigation);
