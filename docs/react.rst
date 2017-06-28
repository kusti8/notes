React Native - General
==========================

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

Debugging
---------------------

**Error when starting package server (react-native start)**
On Arch to fix:

.. code-block:: bash

    echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
    react-native start
