.. _logging-in:

Logging in
==========

Microsoft account flow
~~~~~~~~~~~~~~~~~~~~~~

Authentication with either a Skype username or a Microsoft account requires calling out to the MS OAuth page, and retrieving the Skype token.

This is used to obtain a registration token for the messaging APIs, but is also used as-is in user and static endpoints.

.. http:get:: https://login.skype.com/login/oauth/microsoft

    This will redirect to ``login.live.com``.  Collect the value of the hidden field named ``PPFT``.

    :query client_id: ``578134``
    :query redirect_uri: ``https://web.skype.com``
    :resheader Cookie: contains ``MSPRequ`` and ``MSPOK``, both required for the next step

.. http:post:: https://login.live.com/ppsecure/post.srf

    If all is well, a hidden field with identifier ``t`` will contain a token for the last step.

    :query wa: ``wsignin1.0``
    :query wp: ``MBI_SSL``
    :query wreply: ``https://lw.skype.com/login/oauth/proxy?client_id=578134&site_name=lw.skype.com&redirect_uri=https%3A%2F%2Fweb.skype.com%2F``
    :reqheader Set-Cookie: include ``MSPRequ`` and ``MSPOK`` as obtained earlier, and ``CkTst`` with a timestamp in the standard format
    :form login: Skype username or Microsoft account email address
    :form passwd: corresponding account password
    :form PPFT: as obtained from the hidden field

.. http:post:: https://web.skype.com/login/microsoft

    The Skype token and expiry can be retrieved in the same fields as with a username/password login.

    :query client_id: ``578134``
    :query redirect_uri: ``https://web.skype.com``
    :form client_id: ``578134``
    :form redirect_uri: ``https://web.skype.com``
    :form oauthPartner: ``999``
    :form site_name: ``lw.skype.com``
    :form t: as obtained earlier

Guest authentication
~~~~~~~~~~~~~~~~~~~~

Skype also supports the notion of a guest, who can access a conversation from an invite, without a Skype account.

A guest account differs from regular accounts in that:

- They can only access a single group conversation.
- Their username is prefixed with ``guest:``.
- They have no profile information, just a display name.
- They expire after 24 hours.

.. http:get:: https://join.skype.com/(string:id)

    :param id: public join URL code
    :reqheader User-Agent: must be set to that of a supported device, e.g. Chrome
    :resheader Set-Cookie: CSRF token in ``csrf_token``, request identifier in ``launcher_session_id``

.. http:post:: https://join.skype.com/api/v1/users/guests

    :reqheader csrf_token: as above
    :reqheader X-Skype-Request-Id: session identifier from above
    :reqjson flowId: session identifier from above
    :reqjson shortId: public join URL code
    :reqjson longId: identifier retrieved from join.skype.com URL lookup
    :reqjson threadId: chat identifier (``19:<random>@thread.skype``)
    :reqjson name: guest display name
    :resheader Set-Cookie: token cookie named ``guest_token_<thread>`` containing the new token

.. _registration-token:

Registration token
------------------

.. http:post:: https://client-s.gateway.messenger.live.com/v1/users/ME/endpoints

    .. note:: A JSON object must be provided in the body of the request, even if empty.

    The non-standard header ``LockAndKey`` is required, and has the following format::

        appId=msmsgs@msnmsgr.com; time=<timestamp>; lockAndKeyResponse=...

    Here, ``time`` is a UNIX timestamp in the same format as before.  The actual response must be generated through some Skype-specific crypto -- see :meth:`skpy.conn.getMac256Hash` for the algorithm.

    In some cases, a call to this endpoint will return a ``Location`` header pointing to a different subdomain (e.g. ``https://db1-client-s.gateway.messenger.live.com``.  In this case, repeat the call using the new URL.  You should use this domain in place of the default one for all other gateway calls.

    :reqheader Authentication: Skype token in the form ``skypetoken=<token>``
    :reqheader LockAndKey: key response as above
    :resheader Location: URL to newly generated endpoint, or to required subdomain
    :resheader Set-RegistrationToken: token response in the form ``registrationToken=<token>; expires=<timestamp>; endpointId=<id>``
