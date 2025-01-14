Conversations
=============

.. http:get:: https://client-s.gateway.messenger.live.com/v1/users/ME/conversations

    This returns an array of conversations that the current user has most recently interacted with.  The ``lastMessage`` field holds a message object in the same format as retrieved from ``/v1/users/ME/conversations/(id)/messages``.

    .. note:: This endpoint is :ref:`paginated <pagination>`.

    :query startTime: ``0``
    :query view: ``msnp24Equivalent``
    :query targetType: ``Passport|Skype|Lync|Thread``

    .. code-block:: javascript

        {"_metadata": {"backwardLink": "https://client-s.gateway.messenger.live.com/v1/users/ME/conversations?syncState=...&view=msnp24Equivalent",
                       "forwardLink": "https://client-s.gateway.messenger.live.com/v1/users/ME/conversations?syncState=...&view=msnp24Equivalent",
                       "syncState": "https://client-s.gateway.messenger.live.com/v1/users/ME/conversations?syncState=...&view=msnp24Equivalent",
                       "totalCount": 10},
         "conversations": [{"id": "8:joe.4",
                            "lastMessage": {"clientmessageid": "1451606399999",
                                            "composetime": "2016-01-01T00:00:00.000Z",
                                            "content": "Hi!",
                                            "conversationLink": "https://client-s.gateway.messenger.live.com/v1/users/ME/conversations/8:joe.4",
                                            "from": "https://client-s.gateway.messenger.live.com/v1/users/ME/contacts/8:joe.4",
                                            "id": "1451606400000",
                                            "messagetype": "Text",
                                            "originalarrivaltime": "2016-01-01T00:00:00.000Z",
                                            "type": "Message",
                                            "version": "1451606400000"},
                            "messages": "https://client-s.gateway.messenger.live.com/v1/users/ME/conversations/8:joe.4/messages",
                            "properties": {"clearedat": "1451606400000", "consumptionhorizon": "..."},
                            "targetLink": "https://client-s.gateway.messenger.live.com/v1/users/ME/contacts/8:joe.4",
                            "type": "Conversation",
                            "version": 1451606400000},
                           {"id": "19:a0b1c2...d3e4f5@thread.skype",
                            "lastMessage": {"clientmessageid": "1451606399999",
                                            "composetime": "2016-01-01T00:00:00.000Z",
                                            "content": "A message for the team.",
                                            "conversationLink": "https://client-s.gateway.messenger.live.com/v1/users/ME/conversations/19:a0b1c2...d3e4f5@thread.skype",
                                            "from": "https://client-s.gateway.messenger.live.com/v1/users/ME/contacts/8:anna.7",
                                            "id": "1451606400000",
                                            "messagetype": "Text",
                                            "originalarrivaltime": "2016-01-01T00:00:00.000Z",
                                            "type": "Message",
                                            "version": "1451606400000"},
                            "messages": "https://client-s.gateway.messenger.live.com/v1/users/ME/conversations/19:a0b1c2...d3e4f5@thread.skype/messages",
                            "properties": {"consumptionhorizon": "..."},
                            "targetLink": "https://client-s.gateway.messenger.live.com/v1/threads/19:a0b1c2...d3e4f5@thread.skype",
                            "threadProperties": {"lastjoinat": "1451606400000", "topic": "Team chat", "version": "1451606400000"},
                            "type": "Conversation",
                            "version": 1451606400000},
                           ...]}

.. http:get:: https://client-s.gateway.messenger.live.com/v1/users/ME/conversations/(string:id)

    Retrieve details about a conversation.

    :param id: chat thread identifier

    .. code-block:: javascript

        {"id": "19:a0b1c2...d3e4f5@thread.skype",
         "lastMessage": {"clientmessageid": "1451606399999",
                         "composetime": "2016-01-01T00:00:00.000Z",
                         "content": "A message for the team.",
                         "conversationLink": "https://client-s.gateway.messenger.live.com/v1/users/ME/conversations/19:a0b1c2...d3e4f5@thread.skype",
                         "from": "https://client-s.gateway.messenger.live.com/v1/users/ME/contacts/8:anna.7",
                         "id": "1451606400000",
                         "messagetype": "Text",
                         "originalarrivaltime": "2016-01-01T00:00:00.000Z",
                         "type": "Message",
                         "version": "1451606400000"},
         "messages": "https://client-s.gateway.messenger.live.com/v1/users/ME/conversations/19:a0b1c2...d3e4f5@thread.skype/messages",
         "properties": {"consumptionhorizon": "..."},
         "targetLink": "https://client-s.gateway.messenger.live.com/v1/threads/19:a0b1c2...d3e4f5@thread.skype",
         "threadProperties": {"lastjoinat": "1451606400000", "topic": "Team chat", "version": "1451606400000"},
         "type": "Conversation",
         "version": 1451606400000}

Group conversations
-------------------

.. http:get:: https://client-s.gateway.messenger.live.com/v1/threads/(string:id)

    Fetch additional group-specific information, including the members and admins of the chat, topic, and join permissions.

    :param id: chat thread identifier

    .. code-block:: javascript

        {"id": "19:a0b1c2...d3e4f5@thread.skype",
         "members": [{"capabilities": [],
                      "cid": 0,
                      "friendlyName": "",
                      "id": "8:anna.7",
                      "linkedMri": "",
                      "role": "Admin",
                      "type": "ThreadMember",
                      "userLink": "https://client-s.gateway.messenger.live.com/v1/users/8:anna.7",
                      "userTile": ""},
                     {"capabilities": [],
                      "cid": 0,
                      "friendlyName": "",
                      "id": "8:joe.4",
                      "linkedMri": "",
                      "role": "User",
                      "type": "ThreadMember",
                      "userLink": "https://client-s.gateway.messenger.live.com/v1/users/8:joe.4",
                      "userTile": ""},
                     ...],
         "messages": "https://client-s.gateway.messenger.live.com/v1/users/ME/conversations/19:a0b1c2...d3e4f5@thread.skype/messages",
         "properties": {"capabilities": ["AddMember",
                                         "ChangeTopic",
                                         "ChangePicture",
                                         "EditMsg",
                                         "CallP2P",
                                         "SendText",
                                         "SendSms",
                                         "SendFileP2P",
                                         "SendContacts",
                                         "SendVideoMsg",
                                         "SendMediaMsg",
                                         "ChangeModerated"],
                        "createdat": "1451606400000",
                        "creator": "8:anna.7",
                        "creatorcid": "0",
                        "historydisclosed": "true",
                        "joiningenabled": "true",
                        "picture": "URL@https://api.asm.skype.com/v1/objects/0-.../views/avatar_fullsize",
                        "topic": "Team chat"},
         "type": "Thread",
         "version": 1451606400000}

.. http:post:: https://client-s.gateway.messenger.live.com/v1/threads

    Create a new group conversation.

    Each member object consists of an ``id`` (user thread identifier), and role (either ``Admin`` or ``User``).

    :reqjson members: array of member objects
    :resheader Location: URL for the new conversation

.. http:put:: https://client-s.gateway.messenger.live.com/v1/threads/(string:id)/properties

    Update properties of a group conversation.  Only one property can be set at a time, which should be the value of the ``name`` field, and key for the field holding the new value.

    :param id: chat thread identifier
    :reqjson name: name of parameter to be updated (from the rest of this list)
    :reqjson topic: new conversation topic
    :reqjson joiningenabled: whether users can join by URL
    :reqjson historydisclosed: whether newly-joining users can see past message history

Join URLs
---------

.. http:post:: https://api.scheduler.skype.com/threads

    Retrieve the join URL for a group conversation, if it is currently public.

    :reqjson baseDomain: ``https://join.skype.com/launch/``
    :reqjson threadId: chat thread identifier

    .. code-block:: javascript

        {"Blob": "AzByCx...XcYbZa",
         "Id": "Za0Yb1...By2Az3",
         "JoinUrl": "https://join.skype.com/<join-code>",
         "ThreadId": "19:a0b1c2...d3e4f5@thread.skype"}

.. http:post:: https://join.skype.com/api/v2/conversation/

    Convert a join URL into standard identifiers.

    .. note:: No authentication is required for this endpoint.

    :reqjson shortId: join identifier from the URL
    :reqjson type: ``wl``

    .. code-block:: javascript

        {"Action": "Chat",
         "ChatBlob": "AzByCx...XcYbZa",
         "FlowId": "1",
         "Id": "Za0Yb1...By2Az3",
         "Resource": "19:a0b1c2...d3e4f5@thread.skype"}

Messages
--------

.. http:get:: https://client-s.gateway.messenger.live.com/v1/users/ME/conversations/(string:id)/messages

    Retrieve the most recent messages from the conversation.

    .. note:: This endpoint is :ref:`paginated <pagination>`.

    :param id: chat thread identifier

    .. code-block:: javascript

        {"_metadata": {"backwardLink": "https://client-s.gateway.messenger.live.com/v1/users/ME/conversations/19:a0b1c2...d3e4f5@thread.skype/messages?syncState=...&view=msnp24Equivalent",
                       "forwardLink": "https://client-s.gateway.messenger.live.com/v1/users/ME/conversations/19:a0b1c2...d3e4f5@thread.skype/messages?syncState=...&view=msnp24Equivalent",
                       "syncState": "https://client-s.gateway.messenger.live.com/v1/users/ME/conversations/19:a0b1c2...d3e4f5@thread.skype/messages?syncState=...&view=msnp24Equivalent",
                       "totalCount": 10},
         "messages": [{"clientmessageid": "1451606399999",
                       "composetime": "2016-01-01T00:00:00.000Z",
                       "content": "A message for the team.",
                       "conversationLink": "https://client-s.gateway.messenger.live.com/v1/users/ME/conversations/19:a0b1c2...d3e4f5@thread.skype",
                       "from": "https://client-s.gateway.messenger.live.com/v1/users/ME/contacts/8:anna.7",
                       "id": "1451606400000",
                       "messagetype": "Text",
                       "originalarrivaltime": "2016-01-01T00:00:00.000Z",
                       "type": "Message",
                       "version": "1451606400000"},
                      ...]}

.. http:post:: https://client-s.gateway.messenger.live.com/v1/users/ME/conversations/(string:id)/messages

    Send a message to the conversation.  There are several additional parameters that can be passed in for different message types.

    :param id: chat thread identifier
    :reqjson contenttype: ``text``
    :reqjson messagetype: base message type
    :reqjson content: raw content for the message

    .. code-block:: javascript

        {"OriginalArrivalTime": 1451606400000}

.. http:delete:: https://client-s.gateway.messenger.live.com/v1/users/ME/conversations/(string:id)/messages

    Delete all message history for this client.

    :param id: chat thread identifier

Message types
~~~~~~~~~~~~~

- ``Text``: A plain text message.

- ``RichText``: A text message with rich formatting.

- ``RichText/Contacts``: A message containing an embedded contact.

  .. code-block:: html

      <contacts><c t="s" s="joe.4" f="Joe Bloggs"/></contacts>

- ``RichText/Location``: A message containing the user's location.

  .. code-block:: html

      <location latitude="51000000" longitude="-10000" altitude="0"
                horizontalAccuracy="0" verticalAccuracy="0"
                speed="0" course="0" timeStamp="1451606400"
                address="Unknown address" pointOfInterest="">
          <a href="https://www.bing.com/maps/...">Unknown address</a>
      </location>

- ``RichText/Media_GenericFile``: A message representing a shared file.

  .. code-block:: html

      <URIObject type="File.1" uri="https://api.asm.skype.com/v1/objects/0-..."
                 url_thumbnail="https://api.asm.skype.com/v1/objects/0-.../views/thumbnail">
          <Title>Title: file.txt</Title>
          <Description>Description: file.txt</Description>
          <FileSize v="84"/>
          <OriginalName v="file.txt"/>
          <a href="https://login.skype.com/...">https://login.skype.com/...</a>
      </URIObject>

- ``RichText/UriObject``: A shared image file.

  .. code-block:: html

        <URIObject type="Picture.1" uri="https://api.asm.skype.com/v1/objects/0-..."
                   url_thumbnail="https://api.asm.skype.com/v1/objects/0-.../views/imgt1">
            <Title/><Description/>
            <OriginalName v="photo.jpg"/>
            <a href="https://api.asm.skype.com/s/i?0-...">https://api.asm.skype.com/s/i?0-...</a>
            <meta type="photo" originalName="photo.jpg"/>
        </URIObject>

- ``RichText/Files``: Multiple file attachments in one message.

  .. code-block:: html

        <files alt="sent files &quot;file1.txt&quot;, &quot;file2.txt&quot;">
            <file size="84" index="0" tid="1000000000">file1.txt</file>
            <file size="105" index="1" tid="2000000000">file2.txt</file>
        </files>

- ``Control/Typing``, ``Control/ClearTyping``: A user starts or stops typing in a conversation.

- ``Event/Call``: Call-related notifications.

  .. code-block:: html

        <partlist type="started" alt="">
            <part identity="joe.4"><name>Joe Bloggs</name></part>
        </partlist>

- ``ThreadActivity/TopicUpdate``: A group conversation topic change.

  .. code-block:: html

        <topicupdate>
            <eventtime>1451606400000</eventtime>
            <initiator>8:joe.4</initiator>
            <value>Team chat</value>
        </topicupdate>

- ``ThreadActivity/JoiningEnabledUpdate``: A change to joining a group conversation by link.

  .. code-block:: html

        <joiningenabledupdate>
            <eventtime>1451606400000</eventtime>
            <initiator>8:anna.7</initiator>
            <value>true</value>
        </joiningenabledupdate>

- ``ThreadActivity/HistoryDisclosedUpdate``: A group conversation topic change.

  .. code-block:: html

        <historydisclosedupdate>
            <eventtime>1451606400000</eventtime>
            <initiator>8:joe.4</initiator>
            <value>false</value>
        </historydisclosedupdate>

- ``ThreadActivity/AddMember``: A user was added to the group conversation.

  .. code-block:: html

        <addmember>
            <eventtime>1451606400000</eventtime>
            <initiator>8:anna.7</initiator>
            <target>8:joe.4</target>
        </addmember>

- ``ThreadActivity/RoleUpdate``: A user's admin status was changed.

  .. code-block:: html

        <roleupdate>
            <eventtime>1451606400000</eventtime>
            <initiator>8:anna.7</initiator>
            <target><id>8:joe.4</id><role>admin</role></target>
        </roleupdate>

- ``ThreadActivity/DeleteMember``: A user was removed from the group conversation.

  .. code-block:: html

        <deletemember>
            <eventtime>1451606400000</eventtime>
            <initiator>8:anna.7</initiator>
            <target>8:joe.4</target>
        </deletemember>
