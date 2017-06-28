React Native - General
==========================

On Arch:

.. code-block:: bash

    echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
    react-native start
