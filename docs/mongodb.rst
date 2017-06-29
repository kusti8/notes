MongoDB
=========

Install
------------

.. code-block:: bash

    sudo pacman -S mongodb
    sudo pip install pymongo
    mkdir -p /data/db
    sudo chown kusti8 -R /data
    mongod

Quickstart
---------------

http://api.mongodb.com/python/current/tutorial.html

.. code-block:: python

    from pymongo import MongoClient
    client = MongoClient() # Args: (host, port)
    db = client.test
    collection = db.test_collection
    data = {"username": "kusti8", "token": "testing"}
    data_id = collection.insert_one(data).inserted_id

    datas = [{"username": "kusti8", "token": "testing"}, {"username": "kusti7", "token": "testing123"}]
    data_ids = collection.insert_many(datas).inserted_ids

    collection.find_one()
    collection.find_one({"username": "kusti8"})

    for post in posts.find():
        print(post)

Example - Push Notification Tokens
----------------------------------------

.. code-block:: python

    from gcm import *
    from time import sleep
    #from apns import APNs, Frame, Payload
    from izzati import Backend
    from pymongo import MongoClient

    gcm = GCM("AIzaSyBUXAxT9Nynnx6ZbQfjnZL9-Ep3d37wBxY")
    #apns = APNs(use_sandbox=True, cert_file='cert.pem')

    client = MongoClient()
    db = client.pushapp
    tokens = db.tokens

    def callback(data, files):
        global gcm, tokens
        print(data)
        token = {'token': data['token']}
        tokens.update_one(token, token, upsert=True) # Makes sure they are unique
        data = {'title': 'Test Notifications', 'message': 'Hello!'}
        #payload = Payload(alert="Hello World!", sound="default", badge=1, mutable_content=True)

        #apns.gateway_server.send_notification(token, payload)
        gcm.plaintext_request(registration_id=token, data=data)
        sleep(10)
        gcm.plaintext_request(registration_id=token, data=data)

    b = Backend(callback)
    b.run()
