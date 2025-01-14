Basic usage
===========

Installation
------------

The easiest way to get started is by installing with ``pip``::

    $ pip install skpy

SkPy can also be installed straight from the repo.  Once cloned, fetch the requirements if not already present, then run the setup script::

    $ git clone git@github.com:OllieTerrance/SkPy.git
    $ cd SkPy
    $ pip install -r requirements.txt
    $ python setup.py install

.. note:: For development of the library itself, you can use ``python setup.py develop``, which will hardlink the ``skpy`` module into your Python site-packages folder, keeping it up-to-date.

Alternatively, just go ahead and import :mod:`skpy` from the repo root, without any installation.

Connecting to Skype
-------------------

A connection to Skype is made when first creating a :class:`.Skype` instance::

    >>> from skpy import Skype
    >>> from getpass import getpass # You probably don't want your password shown in plain text!
    >>> Skype("fred.2", getpass())
    Password: 
    Skype(userId='fred.2')

By specifying a token file, session information is cached and can be reused without needing credentials again::

    >>> Skype("fred.2", getpass(), ".tokens-fred.2")
    Password: 
    Skype(userId='fred.2')

    ...

    >>> Skype(tokenFile=".tokens-fred.2") # No username/password needed this time.
    Skype(userId='fred.2')

.. warning::

    If you make too many authentication attempts, you may become temporarily rate limited by the Skype API, or be required to complete a captcha to continue.  For the latter, this needs to be done in a browser with a matching IP address.

    To avoid this, you should reuse the Skype token where possible.  A token will usually only last for 24 hours (even ``web.skype.com`` forces re-authentication after that time), though you can check the expiry with :attr:`.SkypeConnection.tokenExpiry`.

Many requests that retrieve data from the Skype API are cached for you, to avoid repeat requests.

Advanced connections
~~~~~~~~~~~~~~~~~~~~

Depending on the interface being provided by the user, it may not be desirable to require credentials when instantiating the :class:`.Skype` class.  In this case, pass ``connect=False`` to produce an as-yet-unconnected instance::

    >>> sk = Skype(connect=False)
    >>> sk.conn
    SkypeConnection(connected=False)

From there, the Skype connection object :attr:`.Skype.conn` can be manipulated directly, to set a token file or a username/password pair, or to sign in as a guest::

    >>> from skpy import SkypeAuthException
    >>> sk.conn.setTokenFile(".tokens-app")
    >>> try:
    ...     sk.conn.readToken()
    >>> except SkypeAuthException:
    ...     # Prompt the user for their credentials.
    ...     sk.conn.setUserPwd(username, password)
    ...     sk.conn.getSkypeToken()
    ...
    >>> sk.conn
    SkypeConnection(userId='fred.2', connected=True, guest=False)

Retrieving contacts
-------------------

Each :class:`.Skype` instance has a :attr:`contacts <.Skype.contacts>` field.  In most cases, it is sufficient to use key lookups with contact identifiers::

    >>> sk = Skype(...)
    >>> sk.contacts
    SkypeContacts()
    >>> sk.contacts["joe.4"] # Joe is a contact of Fred's.
    SkypeContact(id='joe.4', name=Name(first='Joe', last='Bloggs'), ..., authorised=True, blocked=False)
    >>> sk.contacts["anna.7"] # Here, Anna is not a contact.
    SkypeUser(id='anna.7', name=Name(first='Anna', last='Cooper'), ...)

Note also the special :attr:`.Skype.user` field, a contact object for the connected account::

    >>> sk.contacts["fred.2"] # It's you!
    SkypeContact(id='fred.2', name=Name(first='Fred', last='Adams'), ...)
    >>> sk.contacts["fred.2"] is sk.user
    True

Generally, you will get less information out of :class:`.SkypeUser` objects as they only access public info.

Incoming contact requests can be obtained through :meth:`requests() <.SkypeContacts.requests>`, which each have :meth:`accept() <.SkypeRequest.accept>` and :meth:`reject() <.SkypeRequest.reject>` methods.

Using conversations
-------------------

The :attr:`.Skype.chats` field provides a similar interface to conversations that :attr:`contacts <.Skype.contacts>` provides for users.

Each conversation has a unique identifier, :attr:`.SkypeChat.id`, of the form ``<type>:<id>``.  Single (one-to-one) conversations are usually identified with ``8:<username>``, where ``username`` is that of the other contact.  Some external protocols may use a different type number, for example ``1`` for Messenger contacts.  Group conversation identifiers looks like ``19:<random>@thread.skype``.

Each contact has a corresponding :attr:`.SkypeUser.chat` reference to their one-to-one conversation::

    >>> sk.contacts["joe.4"].chat
    SkypeSingleChat(id='8:joe.4', userId='joe.4')

Alternatively, single or group conversations with recent activity can be retrieved with :meth:`.SkypeChats.recent`.  This can be called multiple times to fetch the next batch.

:meth:`.SkypeChat.getMsgs` will similarly give you a batch of messages from that conversation::

    >>> sk.chats["8:joe.4"].getMsgs()
    [SkypeMsg(id='1453283895457', type='Text', time=datetime.datetime(2016, 1, 20, 9, 58, 15, 341000), ...),
     SkypeMsg(id='1452949957379', type='Text', time=datetime.datetime(2016, 1, 16, 13, 12, 37, 109000), ...), ...]

Send a message using :meth:`sendMsg() <.SkypeChat.sendMsg>`::

    >>> ch = sk.chats["8:joe.4"]
    >>> ch.sendMsg("Hello.")
    SkypeMsg(..., type='Text', ..., userId='fred.2', chatId='8:joe.4', content='Hello.')
    >>> ch.sendMsg(SkypeMsg.bold("Bold!"), rich=True)
    SkypeMsg(..., type='RichText', ..., userId='fred.2', chatId='8:joe.4', content='<b...>Bold!</b>')

A number of formatting helper methods are provided on the :class:`.SkypeMsg` class.

If the group conversation you want doesn't exist, you can create a new group chat with a list of participants::

    >>> sk.chats.create(members=("joe.4", "daisy.5"), admins=("joe.4",))
    SkypeGroupChat(id='19:...@thread.skype', creatorId='fred.2', userIds=['fred.2', 'joe.4', 'daisy.5'], ...)

Event processing
----------------

In order to react to incoming messages and event, an event loop is necessary.  The :class:`.SkypeEventLoop` class provides a base to write event processing programs::

    >>> from skpy import SkypeEventLoop
    >>> class MySkype(SkypeEventLoop):
    ...     def onEvent(self, event):
    ...         print(repr(event))
    ... 
    >>> MySkype(tokenFile=".tokens-fred.2", autoAck=True)
    MySkype(userId='fred.2')

From here, call :meth:`.SkypeEventLoop.loop()` to start receiving events::

    >>> sk = MySkype(tokenFile=".tokens-fred.2", autoAck=True)
    >>> sk.loop()
    SkypePresenceEvent(id=1000, ..., userId='joe.4', online=True)
    SkypeEndpointEvent(id=1001, ..., userId='joe.4')
    SkypePresenceEvent(id=1002, ..., userId='anna.7', online=True)
    SkypeEndpointEvent(id=1003, ..., userId='anna.7')
    SkypeEndpointEvent(id=1004, ..., userId='anna.7')
    ...
