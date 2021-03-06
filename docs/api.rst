=======
API
=======

Agora ciudadana exports a REST API with full access to the data model and user actions.

Format
======

Access to /api/v1/xxxx to use the API resources, using the corresponding HTTP methods (GET, POST, PUT, DELETE...).

Requests and answers may use any of the standard formats: xml, json or yaml. The format may be specified via "Accept"
HTTP headers or with ?format=json (or xml or yaml) parameter in the URL.

**Status reporting**

API calls use the standard HTTP codes for status reporting (200 OK, 404 Not Found, 403 Forbidden, etc.). In case of
error, the returned data may have fields with additional info (see each individual call for more explanations).


Authentication
==============

The API accepts two modes of authentication:

**Readonly unathenticated queries**

Some read-only queries can be requested with no authentication mechanism at all. For example the listing of agoras supports this. This is useful and handy if you just want to request some generic information.

**Session authentication**

The session cookie used in the normal login is accepted and identifies the user that is using the API. This is useful
mainly for the javascript embedded in the own Agora pages.

**Token authentication**

For access the API with an external application, you need the auth token. When you call the login API function, the
user token is returned, and you must supply it in the Authorization header in all next HTTP calls:

.. code-block:: http

    # As a header
    # Format is ''Authorization: ApiKey <username>:<api_key>''
    Authorization: ApiKey daniel:204db7bcfafb2deb7506b89eb3b9b715b09905c8

Currently the user token is currently only accesible through administrators that can access to the django shell:

.. code-block:: python

    In [1]: from tastypie.models import ApiKey

    In [2]: ApiKey.objects.get(user__username="user1")
    Out[2]: <ApiKey: b133b30b2348d7ba8ac6cb63b7aefb382c0804d2 for user1>



Lists, Pagination and filtering
===============================

When a list of resources is needed, Agora API always paginates the results. One can set a specific offset and limit the number of items. Also some listings allow filtering by some fields, for example a list of users might filter by username.

Filter fields sometimes are said to support django field lookups. In this case if this happens when filtering by username, you can also do a more advanced filtering by filtering only usernames that start with a given string with `username__startswith` parameter. See the different django field lookups available in https://docs.djangoproject.com/en/dev/ref/models/querysets/

Resource: Agora
===============

List Agoras
-----------

.. http:get:: /agora/

   List agoras

   :query offset: offset number. default is 0
   :query limit: limit number. default is 20
   :query name: filter by `name` of the agora. It allows all django filter types.
   :statuscode 200 OK: no error

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/agora/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

        {
        "meta": {
            "previous": null, 
            "total_count": 2, 
            "offset": 0, 
            "limit": 20, 
            "next": null
        }, 
        "objects": 
        [
            {
                "membership_policy": "ANYONE_CAN_JOIN", 
                "mugshot_url": "/static/img/agora_default_logo.png", 
                "name": "agoraone", 
                "creator": {
                    "username": "david", 
                    "first_name": "", 
                    "last_name": "", 
                    "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=http%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
                    "url": "/user/david", 
                    "is_active": true, 
                    "last_login": "2012-11-29T18:18:46.837000", 
                    "full_name": "", 
                    "short_description": "Is a member of 2 agoras and has emitted  0 direct votes.", 
                    "id": 0, 
                    "date_joined": "2012-11-29T16:08:43.874000"
                }, 
                "eligibility": null, 
                "comments_policy": "ANYONE_CAN_COMMENT", 
                "id": 1, 
                "pretty_name": "AgoraOne", 
                "url": "/david/agoraone", 
                "created_at_date": "2013-05-13T22:03:30.119472", 
                "archived_at_date": null, 
                "full_name": "david/agoraone", 
                "short_description": "AgoraOne", 
                "image_url": "", 
                "extra_data": null, 
                "is_vote_secret": false, 
                "election_type": "ONCE_CHOICE", 
                "biography": ""
            }, 
            {
                "membership_policy": "ANYONE_CAN_JOIN", 
                "mugshot_url": "/static/img/agora_default_logo.png", 
                "name": "agoratwo", 
                "creator": {
                    "username": "david", 
                    "first_name": "", 
                    "last_name": "", 
                    "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=http%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
                    "url": "/user/david", 
                    "is_active": true, 
                    "last_login": "2012-11-29T18:18:46.837000", 
                    "full_name": "", 
                    "short_description": "Is a member of 2 agoras and has emitted  0 direct votes.", 
                    "id": 0, 
                    "date_joined": "2012-11-29T16:08:43.874000"
                }, 
                "eligibility": null, 
                "comments_policy": "ANYONE_CAN_COMMENT", 
                "id": 2, 
                "pretty_name": "AgoraTwo", 
                "url": "/david/agoratwo", 
                "created_at_date": "2013-05-13T22:03:30.134413", 
                "archived_at_date": null, 
                "full_name": "david/agoratwo", 
                "short_description": "AgoraTwo", 
                "image_url": "", 
                "extra_data": null, 
                "is_vote_secret": true, 
                "election_type": "ONCE_CHOICE", 
                "biography": ""
            }
        ]
    }

Retrieve an agora
-----------------

.. http:get:: /agora/(int:agora_id)

   Retrieves an agora (`agora_id`).

   :param agora_id: agora's unique id
   :type agora_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the agora is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/agora/5/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "membership_policy": "ANYONE_CAN_JOIN", 
        "mugshot_url": "/static/img/agora_default_logo.png", 
        "name": "agoraone", 
        "creator": 
        {
            "username": "david", 
            "first_name": "", 
            "last_name": "", 
            "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=http%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
            "url": "/user/david", 
            "is_active": true, 
            "last_login": "2012-11-29T18:18:46.837000", 
            "full_name": "", 
            "short_description": "Is a member of 2 agoras and has emitted  0 direct votes.", 
            "id": 0, 
            "date_joined": "2012-11-29T16:08:43.874000"
        }, 
        "eligibility": null, 
        "comments_policy": "ANYONE_CAN_COMMENT", 
        "id": 1, 
        "pretty_name": "AgoraOne", 
        "url": "/david/agoraone", 
        "created_at_date": "2013-05-13T22:22:11.790946", 
        "archived_at_date": null, 
        "full_name": "david/agoraone", 
        "short_description": "AgoraOne", 
        "image_url": "", 
        "extra_data": null, 
        "is_vote_secret": false, 
        "election_type": "ONCE_CHOICE", 
        "biography": ""
    }

Create new agora
----------------

.. http:post:: /agora/

   Create a new agora. Requires agora creation permissions.

   Agora creation permissions are specified in ``settings.py`` with the
   ``AGORA_CREATION_PERMISSIONS`` setting. By default it's set to ``any-user``
   which means any authenticated user can create a new agora. But it can also
   be set to ``superusers-only`` which means only site admins can create new
   agoras.

   :form pretty_name: readable agora name. Required.
   :form short_description: short description text. Required.
   :form is_vote_secret: whether the vote is secret in this agora. Optional. False by default.
   :status 201 CREATED: when agora is created correctly
   :status 403 FORBIDDEN: when the user has no agora creation permissions
   :status 400 BAD REQUEST: when the form parameters are invalid

   **Example request**:

   .. sourcecode:: http

    POST /api/v1/agora/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

    {
        "pretty_name": "created-agora",
        "short_description": "created agora description",
        "is_vote_secret": false
    }

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 201 CREATED
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "membership_policy": "ANYONE_CAN_JOIN", 
        "mugshot_url": "/static/img/agora_default_logo.png", 
        "name": "created-agora", 
        "creator": {
            "username": "david", 
            "first_name": "", 
            "last_name": "", 
            "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=http%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
            "url": "/user/david", 
            "is_active": true, 
            "last_login": "2013-05-13T22:25:15.264859", 
            "full_name": "", 
            "short_description": "Is a member of 3 agoras and has emitted  0 direct votes.", 
            "id": 0, 
            "date_joined": "2012-11-29T16:08:43.874000"
        }, 
        "eligibility": null, 
        "comments_policy": "ANYONE_CAN_COMMENT", 
        "id": 3, 
        "pretty_name": "created agora", 
        "url": "/david/created-agora", 
        "created_at_date": "2013-05-13T22:25:29.613300", 
        "archived_at_date": null, 
        "full_name": "david/created-agora", 
        "short_description": "created agora description", 
        "image_url": "", 
        "extra_data": null, 
        "is_vote_secret": false, 
        "election_type": "SIMPLE_DELEGATION", 
        "biography": ""
    }

Delete an agora
---------------

.. http:delete:: /agora/(int:agora_id)

   Deletes the agora (`agora_id`). Requires to be authentication with the user
   that created that agora.

   :param agora_id: agora's unique id
   :type agora_id: int
   :statuscode 204 HTTP_NO_CONTENT: agora was deleted
   :status 403 FORBIDDEN: when the user has no agora delete permissions

   **Example request**:

   .. sourcecode:: http

    DELETE /api/v1/agora/39/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 204 NO CONTENT
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

Modify agora
------------

.. http:put:: /agora/(int:agora_id)

   Modifies an agora (`agora_id`). Requires the authenticated user to be an
   administrator of the agora.

   :form pretty_name: readable agora name. Required.
   :form short_description: short description text. Required.
   :form is_vote_secret: whether the vote is secret in this agora. Optional. False by default.
   :form biography: longer description text. Optional. Empty by default.
   :form membership_policy: membership policy. Optional. Possible values are: ``ANYONE_CAN_JOIN``, ``JOINING_REQUIRES_ADMINS_APPROVAL_ANY_DELEGATE``, ``JOINING_REQUIRES_ADMINS_APPROVAL``. ``ANYONE_CAN_JOIN`` by default.
   :form comments_policy: comments policy. Optional. Possible values are: ``ANYONE_CAN_COMMENT``, ``ONLY_MEMBERS_CAN_COMMENT``, ``ONLY_ADMINS_CAN_COMMENT``, ``NO_COMMENTS``. ``ANYONE_CAN_COMMENT`` by default.
   :status 202 CREATED: when agora is modified correctly
   :status 403 FORBIDDEN: when the user has no agora administration permissions
   :status 400 BAD REQUEST: when the form parameters are invalid

   .. sourcecode:: http

    PUT /api/v1/agora/5/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

    {
        "pretty_name": "updated name",
        "short_description": "new desc",
        "is_vote_secret": false,
        "comments_policy": "ANYONE_CAN_COMMENT",
        "membership_policy": "ANYONE_CAN_JOIN",
        "biography": "bio",
    }

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 202 ACCEPTED
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "membership_policy": "ANYONE_CAN_JOIN", 
        "mugshot_url": "/static/img/agora_default_logo.png", 
        "name": "agoraone", 
        "creator": 
        {
            "username": "david", 
            "first_name": "", 
            "last_name": "", 
            "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=http%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
            "url": "/user/david", 
            "is_active": true, 
            "last_login": "2013-05-13T22:36:27.249371", 
            "full_name": "", 
            "short_description": "Is a member of 2 agoras and has emitted  0 direct votes.", 
            "id": 0, 
            "date_joined": "2012-11-29T16:08:43.874000"
        }, 
        "eligibility": null, 
        "comments_policy": "ANYONE_CAN_COMMENT", 
        "id": 1, 
        "pretty_name": "updated name", 
        "url": "/david/agoraone", 
        "created_at_date": "2013-05-13T22:36:35.484807", 
        "archived_at_date": null, 
        "full_name": "david/agoraone", 
        "short_description": "new desc", 
        "image_url": "", 
        "extra_data": null, 
        "is_vote_secret": false, 
        "election_type": "ONCE_CHOICE", 
        "biography": "bio"
    }

Execute an action
-----------------

.. http:post:: /agora/(int:agora_id)/action

   Request to execute an action in the agora (`agora_id`).

   The available actions are:

   **get_permissions**

   Returns a list of the permissions that the authenticated user has over the specified agora.

   **request_membership**

   The authenticated user requests membership in the specified agora. The authenticated user must have the ``request_membership`` permission on the agora to succeed.

   **cancel_membership**

   The authenticated user cancels its membership request in an agora. The authenticated user must have ``cancel_membership_request`` permission on the agora to succeed.

   **join**

   The authenticated user joins the specified agora. The authenticated user must have the ``join`` permission on the agora to succeed.

   **leave**
   The authenticated user leaves the specified agora. The authenticated user must have the ``leave`` permission on the agora to succeed. The creator of an agora can leave it.

   **accept_membership**

   The authenticated user accepts the membership of the specified user (in the field "username") in an agora. The authenticated user must have ``admin`` permission on the agora and the specified user must have a pending membership request to succeed.

   **deny_membership**

   The authenticated user denies the membership of the specified user (in the field "username") in an agora. The authenticated user must have ``admin`` permission on the agora and the specified user must have a pending membership request to succeed.

   **add_membership**

   The authenticated user adds membership to the specified user (in the field "username") in an agora. The authenticated user must have ``admin`` permission on the agora and the specified username must be not a member of the given agora to succeed.

   **remove_membership**

   The authenticated user removes membership to the specified user (in the field "username") in an agora. The authenticated user must have ``admin`` permission on the agora and the specified username must be a member of the given agora to succeed.

   **request_admin_membership**

   The authenticated user requests admin membership in an agora. The authenticated user must have ``request_admin_membership`` permission on the agora to succeed.

   **cancel_admin_membership**

   The authenticated user cancels its admin membership request in an agora. The authenticated user must have ``cancel_admin_membership_request`` permission on the agora to succeed.

   **accept_admin_membership**

   The authenticated user accepts admin membership to the specified user (in the field "username") in an agora. The authenticated user must have ``admin`` permission on the agora and the specified username must have a pending admin membership request in the given agora to succeed.

   **deny_admin_membership**

   The authenticated user denies admin membership to the specified user (in the field "username") in an agora. The authenticated user must have ``admin`` permission on the agora and the specified username must have a pending admin membership request in the given agora to succeed.

   **add_admin_membership**

   The authenticated user adds admin membership to the specified user (in the field "username") in an agora. The authenticated user must have ``admin`` permission on the agora and the specified username must be a member in the given agora to succeed.

   **remove_admin_membership**

   The authenticated user removes admin membership from the specified user (in the field "username") in an agora. The authenticated user must have ``admin`` permission on the agora and the specified username must be a member in the given agora to succeed.

   **leave_admin_membership**

   The authenticated user leaves admin membership in an agora. The authenticated user must have ``admin`` permission on the given agora to succeed.

   **create_election**

   The authenticated creates an election in the given agora, providing the following fields:
    * **question**: a text with the main question in the election. Required.
    * **answers**: a list with at least two possible answers to the question. required.
    * **pretty_name**: A title for the election. Required.
    * **description**: A description text for the election. It can follow restructured text format and be as large as needed. Required.
    * **is_vote_secret**: A boolean specifiying if the direct votes must all be secret or not. Required.
    * **from_date**: A string representing the starting date of the election, in format '%Y-%m-%dT%H:%M:%S'. Optional. 
    * **to_date**: A string representing the end date of the election, in format '%Y-%m-%dT%H:%M:%S'. Optional. 

   **delegate_vote**

   The authenticated user stablishes the delegation to an user specified in the field "username", cancelling the previous delegation if any from now on. The authenticated user must have  ``delegate`` permission on the agora and the specified username must exists to succeed.

   **cancel_vote_delegation**

   The authenticated user cancels its delegation on the specified agora from now on. The authenticated user must have  ``delegate`` permission on the agora and have a current delegate on the specified agora to succeed.

   :param agora_id: agora's unique id
   :type agora_id: int
   :form action: name of the action. Required.
   :status 200 OK: no error
   :statuscode 403 FORBIDDEN: when the user has not the required permissions
   :status 404 NOT FOUND: when the agora is not found

   **Example request**:

   .. sourcecode:: http

    POST /api/v1/agora/1/action/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript
    Authorization: ApiKey linus:204db7bcfafb2deb7506b89eb3b9b715b09905c8

    {
       "action": "add_membership",
       "username": "user1",
       "welcome_message": "weeEeEeelcome!"
    }

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "status": "success"
    }


   **Example request**:

   .. sourcecode:: http

    POST /api/v1/agora/1/action/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript
    Authorization: ApiKey linus:204db7bcfafb2deb7506b89eb3b9b715b09905c8

    {
        'action': "create_election",
        'pretty_name': "foo bar",
        'description': "foo bar foo bar",
        'questions': 
        [
            {
                'a': 'ballot/question',
                'tally_type': 'ONE_CHOICE',
                'max': 1,
                'min': 0,
                'question': 'Do you prefer foo or bar?',
                'randomize_answer_order': True,
                'answers': 
                [
                    {
                        'a': 'ballot/answer',
                        'url': '',
                        'details': '',
                        'value': 'fo\"o'
                    },
                    {
                        'a': 'ballot/answer',
                        'url': '',
                        'details': '',
                        'value': 'bar'
                    }
                ]
            }
        ],
        'is_vote_secret': True,
        'from_date': '',
        'to_date': '',
    }

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "creator": "/api/v1/user/0/", 
        "comments_policy": "ANYONE_CAN_COMMENT", 
        "result_tallied_at_date": null, 
        "user_perms": 
        [
            "edit_details", 
            "begin_election", 
            "freeze_election", 
            "archive_election", 
            "comment", 
            "vote_counts"
        ], 
        "result": null, 
        "questions": 
        [
            {
                "a": "ballot/question", 
                "min": 0, 
                "max": 1, 
                "tally_type": "ONE_CHOICE", 
                "question": "Do you prefer foo or bar?", 
                "answers": [
                    {
                        "a": "ballot/answer", 
                        "url": "", 
                        "details": "", 
                        "value": "fo\\"o"
                    }, 
                    {
                        "a": "ballot/answer", 
                        "url": "", 
                        "details": "", 
                        "value": "bar"
                    }
                ], 
                "randomize_answer_order": true
            }
        ], 
        "mugshot_url": "/static/img/election_new_form_info.png", 
        "id": 6, 
        "voting_extended_until_date": null, 
        "is_approved": true, 
        "last_modified_at_date": "2013-05-19T22:36:57.563538", 
        "direct_votes_count": 0, 
        "user_has_delegated": false, 
        "short_description": "foo bar foo bar", 
        "is_vote_secret": true, 
        "voters_frozen_at_date": null, 
        "hash": null, 
        "description": "foo bar foo bar", 
        "frozen_at_date": null, 
        "eligibility": null, 
        "parent_election": null, 
        "pretty_name": "foo bar", 
        "archived_at_date": null, 
        "uuid": "d302fd84-a6ba-4956-a2dc-dc60bb4ebc6f", 
        "delegated_votes_count": 0, 
        "percentage_of_participation": 0.0, 
        "name": "foo-bar", 
        "delegated_votes_frozen_at_date": null, 
        "url": "/david/agoraone/election/foo-bar", 
        "voting_ends_at_date": null, 
        "approved_at_date": "2013-05-19T22:36:57.561286", 
        "tiny_hash": null, 
        "created_at_date": "2013-05-19T22:36:56.753200", 
        "agora": 
        {
            "mugshot_url": "/static/img/agora_default_logo.png", 
            "name": "agoraone", 
            "url": "/david/agoraone", 
            "pretty_name": "AgoraOne", 
            "content_type": "agora", 
            "full_name": "david/agoraone", 
            "short_description": "AgoraOne", 
            "id": 1
        }, 
        "voting_starts_at_date": null, 
        "election_type": "ONE_CHOICE"
    }

   **Example request**:

   .. sourcecode:: http

    POST /api/v1/agora/1/action/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript
    Authorization: ApiKey linus:204db7bcfafb2deb7506b89eb3b9b715b09905c8

    {
            'action': "create_election",
            'pretty_name': "foo bar",
            'description': "foo bar foo bar",
            'questions': 
            [
                {
                    'a': 'ballot/question',
                    'tally_type': 'MEEK-STV',
                    'max': 3,
                    'min': 0,
                    'question': 'Who should be the next presidentá unicode chars ñè?',
                    'randomize_answer_order': True,
                    'num_seats': 2,
                    'answers': 
                    [
                        {
                            'a': 'ballot/answer',
                            'url': '',
                            'details': '',
                            'value': 'Florentino'
                        },
                        {
                            'a': 'ballot/answer',
                            'url': '',
                            'details': '',
                            'value': 'Jack'
                        },
                        {
                            'a': 'ballot/answer',
                            'url': '',
                            'details': '',
                            'value': 'Marie'
                        }
                    ]
                }
            ],
            'is_vote_secret': True,
            'from_date': '',
            'to_date': '',
        }

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

        {
        "creator": "/api/v1/user/0/", 
        "comments_policy": "ANYONE_CAN_COMMENT", 
        "result_tallied_at_date": null, 
        "user_perms": 
        [
            "edit_details", 
            "begin_election", 
            "freeze_election", 
            "archive_election", 
            "comment", 
            "vote_counts"
        ], 
        "result": null, 
        "questions": 
        [
            {
                "a": "ballot/question", 
                "min": 0, 
                "max": 3, 
                "tally_type": "MEEK-STV", 
                "question": "Who should be the next president\\u00e1 unicode chars \\u00f1\\u00e8?", 
                "answers": 
                [
                    {
                        "a": "ballot/answer", 
                        "url": "", 
                        "details": "", 
                        "value": "Florentino"
                    }, 
                    {
                        "a": "ballot/answer", 
                        "url": "", 
                        "details": "", 
                        "value": "Jack"
                    }, 
                    {
                        "a": "ballot/answer", 
                        "url": "", 
                        "details": "", 
                        "value": "Marie"
                    }
                ], 
                "num_seats": 2, 
                "randomize_answer_order": true
            }
        ], 
        "mugshot_url": "/static/img/election_new_form_info.png", 
        "id": 6, 
        "voting_extended_until_date": null, 
        "is_approved": true, 
        "last_modified_at_date": "2013-05-19T22:41:46.701673", 
        "direct_votes_count": 0, 
        "user_has_delegated": false, 
        "short_description": "foo bar foo bar", 
        "is_vote_secret": true, 
        "voters_frozen_at_date": null, 
        "hash": null, 
        "description": "foo bar foo bar", 
        "frozen_at_date": null, 
        "eligibility": null, 
        "parent_election": null, 
        "pretty_name": "foo bar", 
        "archived_at_date": null, 
        "uuid": "e5067217-2eff-4ae7-8f97-500ebb966e5f", 
        "delegated_votes_count": 0, 
        "percentage_of_participation": 0.0, 
        "name": "foo-bar", 
        "delegated_votes_frozen_at_date": null, 
        "url": "/david/agoraone/election/foo-bar", 
        "voting_ends_at_date": null, 
        "approved_at_date": "2013-05-19T22:41:46.699415", 
        "tiny_hash": null, 
        "created_at_date": "2013-05-19T22:41:45.904296", 
        "agora": 
        {
            "mugshot_url": "/static/img/agora_default_logo.png", 
            "name": "agoraone", 
            "url": "/david/agoraone", 
            "pretty_name": "AgoraOne", 
            "content_type": "agora", 
            "full_name": "david/agoraone", 
            "short_description": "AgoraOne", 
            "id": 1
        }, 
        "voting_starts_at_date": null, 
        "election_type": "MEEK-STV"
    }


Retrieve agora members
----------------------

.. http:get:: /agora/(int:agora_id)/members

   Retrieves all the users that are members of agora (`agora_id`).

   :param agora_id: agora's unique id
   :type agora_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the agora is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/agora/1/members/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 2, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "username": "david", 
                "first_name": "", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
                "url": "/user/david", 
                "is_active": true, 
                "short_description": "Is a member of 2 agoras and has emitted  0 direct votes.", 
                "last_login": "2013-05-14T18:36:45.100082", 
                "full_name": "", 
                "agora_permissions": 
                [
                    "admin", 
                    "delete", 
                    "comment", 
                    "create_election", 
                    "delegate"
                ], 
                "id": 0, 
                "date_joined": "2012-11-29T16:08:43.874000"
            }, 
            {
                "username": "user1", 
                "first_name": "Juana Molero", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/cc721459f5b77680bc6a8ba6c9681c46?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FJM.png", 
                "url": "/user/user1", 
                "is_active": true, 
                "short_description": "ultricies. semper vel et, eu laoreet Quisque odio semper ornare. elementum elementum tristique pretium ornare", 
                "last_login": "2012-11-29T19:37:36.263000", 
                "full_name": "Juana Molero", 
                "agora_permissions": 
                [
                    "request_admin_membership", 
                    "leave", 
                    "comment", 
                    "create_election", 
                    "delegate"
                ], 
                "id": 1, 
                "date_joined": "2012-11-29T19:37:36.263000"
            }
        ]
    }

List administrators
-------------------

.. http:get:: /agora/(int:agora_id)/admins

   Retrieves the users that are admin members of agora (`agora_id`).

   :param agora_id: agora's unique id
   :type agora_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the agora is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/agora/1/admins/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 1, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "username": "david", 
                "first_name": "", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
                "url": "/user/david", 
                "is_active": true, 
                "short_description": "Is a member of 2 agoras and has emitted  0 direct votes.", 
                "last_login": "2013-05-14T18:56:38.574606", 
                "full_name": "", 
                "agora_permissions": 
                [
                    "admin", 
                    "delete", 
                    "comment", 
                    "create_election", 
                    "delegate"
                ], 
                "id": 0, 
                "date_joined": "2012-11-29T16:08:43.874000"
            }
        ]
    }

List membership requests
------------------------

.. http:get:: /agora/(int:agora_id)/membership_requests

   Retrieves the users that have pending requests to become members of agora (`agora_id`).

   :param agora_id: agora's unique id
   :type agora_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the agora is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/agora/1/membership_requests/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 1, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "username": "user2", 
                "first_name": "Jose Lopez", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/ecdecef1814126d1846e600cbef37a24?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FJL.png", 
                "url": "/user/user2", 
                "is_active": true, 
                "short_description": "justo. consectetur ultricies ullamcorper nisi, volutpat porta. Sed dui. non Nam Ut volutpat Maecenas orci", 
                "last_login": "2013-05-14T18:59:51.810766", 
                "full_name": "Jose Lopez", 
                "agora_permissions": [], 
                "id": 2, 
                "date_joined": "2012-11-29T19:37:36.987000"
            }
        ]
    }

Retrieve agora admin membership requests
----------------------------------------

.. http:get:: /agora/(int:agora_id)/admin_membership_requests

   Retrieves the users that have pending requests to become admins of agora (`agora_id`). The authenticated user must be an administrator.

   :param agora_id: agora's unique id
   :type agora_id: int
   :status 200 OK: no error
   :status 403 FORBIDDEN: when the user has no admin permissions
   :status 404 NOT FOUND: when the agora is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/agora/1/admin_membership_requests/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 1, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "username": "user1", 
                "first_name": "Juana Molero", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/cc721459f5b77680bc6a8ba6c9681c46?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FJM.png", 
                "url": "/user/user1", 
                "is_active": true, 
                "short_description": "ultricies. semper vel et, eu laoreet Quisque odio semper ornare. elementum elementum tristique pretium ornare", 
                "last_login": "2013-05-14T19:02:37.785145", 
                "full_name": "Juana Molero", 
                "agora_permissions": 
                [
                    "cancel_admin_membership_request", 
                    "leave", 
                    "comment", 
                    "create_election", 
                    "delegate"
                ], 
                "id": 1, 
                "date_joined": "2012-11-29T19:37:36.263000"
            }
        ]
    }

List active delegates
---------------------

.. http:get:: /agora/(int:agora_id)/active_delegates

   Retrieves active delegates of agora (`agora_id`): users that have emitted any valid
   and public vote in any election of this agora.

   :param agora_id: agora's unique id
   :type agora_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the agora is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/agora/1/active_delegates/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 4, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "username": "user2", 
                "first_name": "Jose Lopez", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/ecdecef1814126d1846e600cbef37a24?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FJL.png", 
                "url": "/user/user2", 
                "is_active": true, 
                "short_description": "justo. consectetur ultricies ullamcorper nisi, volutpat porta. Sed dui. non Nam Ut volutpat Maecenas orci", 
                "last_login": "2013-05-17T15:52:42.563169", 
                "full_name": "Jose Lopez", 
                "agora_permissions": [], 
                "id": 2, 
                "date_joined": "2012-11-29T19:37:36.987000"
            }, 
            {
                "username": "user4", 
                "first_name": "Antonio Bonilla", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/562c89a550b820fe2f63365202f07eb0?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FAB.png", 
                "url": "/user/user4", 
                "is_active": true, 
                "short_description": "Nullam fringilla ut ultricies elit. semper erat, gravida fermentum Quisque nec. gravida elit. nulla nec", 
                "last_login": "2013-05-17T15:52:44.018377", 
                "full_name": "Antonio Bonilla", 
                "agora_permissions": [], 
                "id": 4, 
                "date_joined": "2012-11-29T19:37:37.662000"
            }, 
            {
                "username": "user5", 
                "first_name": "Maria Ariza", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/4363106ea31735235c6c6b911ab460c2?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FMA.png", 
                "url": "/user/user5", 
                "is_active": true, 
                "short_description": "Nullam vestibulum bibendum Maecenas eu, Curabitur sit erat congue amet a ultricies velit gravida dictum,", 
                "last_login": "2013-05-17T15:52:44.500338", 
                "full_name": "Maria Ariza", 
                "agora_permissions": [], 
                "id": 5, 
                "date_joined": "2012-11-29T19:37:37.993000"
            }, 
            {
                "username": "user6", 
                "first_name": "Mariano Ariz\\u00f3n", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/013c1f55acdb49dfe716cacc231c8c47?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FMA.png", 
                "url": "/user/user6", 
                "is_active": true, 
                "short_description": "Is a member of 1 agoras and has emitted  1 direct votes.", 
                "last_login": "2013-05-17T15:52:45.920995", 
                "full_name": "Mariano Ariz\\u00f3n", 
                "agora_permissions": [], 
                "id": 6, 
                "date_joined": "2012-11-29T19:37:37.993000"
            }
        ]
    }

List all elections
------------------

.. http:get:: /agora/(int:agora_id)/all_elections

   Retrieves all elections in agora (`agora_id`).

   :param agora_id: agora's unique id
   :type agora_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the agora is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/agora/1/all_elections/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 1, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "creator": "/api/v1/user/0/", 
                "comments_policy": "ANYONE_CAN_COMMENT", 
                "result_tallied_at_date": null, 
                "user_perms": 
                [
                    "comment"
                ], 
                "result": null, 
                "questions": 
                [
                    {
                        "a": "ballot/question", 
                        "min": 0, 
                        "max": 1, 
                        "tally_type": "simple", 
                        "question": "question one", 
                        "answers": 
                        [
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "one"
                            }, 
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "two"
                            }, 
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "three"
                            }
                        ], 
                        "randomize_answer_order": true
                    }
                ], 
                "mugshot_url": "/static/img/election_new_form_info.png", 
                "id": 3, 
                "voting_extended_until_date": null, 
                "is_approved": true, 
                "last_modified_at_date": "2012-12-06T19:16:14.014000", 
                "direct_votes_count": 0, 
                "user_has_delegated": false, 
                "short_description": "election one", 
                "is_vote_secret": true, 
                "voters_frozen_at_date": null, 
                "hash": null, 
                "description": "election one", 
                "frozen_at_date": null, 
                "eligibility": null, 
                "parent_election": null, 
                "pretty_name": "electionone", 
                "archived_at_date": null, 
                "uuid": "a36b385a-8f20-4730-a92e-0b9968adac18", 
                "delegated_votes_count": 0, 
                "percentage_of_participation": 0.0, 
                "name": "electionone", 
                "delegated_votes_frozen_at_date": null, 
                "url": "/david/agoraone/election/electionone", 
                "voting_ends_at_date": null, 
                "approved_at_date": null, 
                "tiny_hash": null, 
                "created_at_date": "2012-12-06T19:16:14.004000", 
                "agora": 
                {
                    "mugshot_url": "/static/img/agora_default_logo.png", 
                    "name": "agoraone", 
                    "url": "/david/agoraone", 
                    "pretty_name": "AgoraOne", 
                    "content_type": "agora", 
                    "full_name": "david/agoraone", 
                    "short_description": "AgoraOne", 
                    "id": 1
                }, 
                "voting_starts_at_date": null, 
                "election_type": "ONCE_CHOICE"
            }
        ]
    }

List tallied elections
----------------------

.. http:get:: /agora/(int:agora_id)/tallied_elections

   Retrieves tallied elections in agora (`agora_id`): past elections that are
   closed and with a result.

   :param agora_id: agora's unique id
   :type agora_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the agora is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/agora/1/tallied_elections/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

     {
        "meta": 
        {
            "total_count": 1, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "creator": "/api/v1/user/0/", 
                "comments_policy": "ANYONE_CAN_COMMENT", 
                "result_tallied_at_date": "2013-05-17T16:03:34.082719", 
                "user_perms": 
                [
                    "archive_election", 
                    "comment", 
                    "vote_counts"
                ], 
                "result": 
                {
                    "a": "result", 
                    "counts": 
                    [
                        {
                            "a": "question/result/ONE_CHOICE", 
                            "min": 0, 
                            "max": 1, 
                            "tally_type": "ONE_CHOICE", 
                            "question": "Do you prefer foo or bar?", 
                            "answers": 
                            [
                                {
                                    "a": "answer/result/ONE_CHOICE", 
                                    "by_delegation_count": 0, 
                                    "url": "", 
                                    "total_count": 0, 
                                    "by_direct_vote_count": 0, 
                                    "value": "fo\\"o", 
                                    "details": "", 
                                    "total_count_percentage": 0.0
                                }, 
                                {
                                    "a": "answer/result/ONE_CHOICE", 
                                    "by_delegation_count": 0, 
                                    "url": "", 
                                    "total_count": 1, 
                                    "by_direct_vote_count": 1, 
                                    "value": "bar", 
                                    "details": "", 
                                    "total_count_percentage": 100.0
                                }
                            ], 
                            "winners": 
                            [
                                "bar"
                            ], 
                            "dirty_votes": 0, 
                            "randomize_answer_order": true, 
                            "total_votes": 1
                        }
                    ], 
                    "total_votes": 1, 
                    "electorate_count": 1, 
                    "total_delegated_votes": 0
                }, 
                "questions": 
                [
                    {
                        "a": "ballot/question", 
                        "min": 0, 
                        "max": 1, 
                        "tally_type": "ONE_CHOICE", 
                        "question": "Do you prefer foo or bar?", 
                        "answers": 
                        [
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "fo\\"o"
                            }, 
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "bar"
                            }
                        ], 
                        "randomize_answer_order": true
                    }
                ], 
                "mugshot_url": "/static/img/election_new_form_info.png", 
                "id": 6, 
                "voting_extended_until_date": "2013-05-17T16:03:34.059170", 
                "is_approved": true, 
                "last_modified_at_date": "2013-05-17T16:03:34.053587", 
                "direct_votes_count": 1, 
                "user_has_delegated": false, 
                "short_description": "foo bar foo bar", 
                "is_vote_secret": true, 
                "voters_frozen_at_date": "2013-05-17T16:03:34.082719", 
                "hash": "c709bbfd3c618468396e5c2694ba3646898560e4db971f1108ba207f041c0d20", 
                "description": "foo bar foo bar", 
                "frozen_at_date": "2013-05-17T16:03:33.475545", 
                "eligibility": null, 
                "parent_election": null, 
                "pretty_name": "foo bar", 
                "archived_at_date": null, 
                "uuid": "9cb86cbf-2916-437b-89da-70a7ffebb010", 
                "delegated_votes_count": 0, 
                "percentage_of_participation": 100.0, 
                "name": "foo-bar", 
                "delegated_votes_frozen_at_date": "2013-05-17T16:03:34.082719", 
                "url": "/david/agoraone/election/foo-bar", 
                "voting_ends_at_date": "2013-05-17T16:03:34.053628", 
                "approved_at_date": "2013-05-17T16:03:33.378015", 
                "tiny_hash": null, 
                "created_at_date": "2013-05-17T16:03:33.298794", 
                "agora": 
                {
                    "mugshot_url": "/static/img/agora_default_logo.png", 
                    "name": "agoraone", 
                    "url": "/david/agoraone", 
                    "pretty_name": "AgoraOne", 
                    "content_type": "agora", 
                    "full_name": "david/agoraone", 
                    "short_description": "AgoraOne", 
                    "id": 1
                }, 
                "voting_starts_at_date": "2013-05-17T16:03:33.480100", 
                "election_type": "ONE_CHOICE"
            }
        ]
    }

List open elections
-------------------

.. http:get:: /agora/(int:agora_id)/open_elections

   Retrieves open elections in agora (`agora_id`): elections that are currently
   taking place in the agora.

   :param agora_id: agora's unique id
   :type agora_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the agora is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/agora/1/open_elections/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 1, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "creator": "/api/v1/user/0/", 
                "comments_policy": "ANYONE_CAN_COMMENT", 
                "result_tallied_at_date": null, 
                "user_perms": 
                [
                    "end_election", 
                    "archive_election", 
                    "comment", 
                    "emit_direct_vote", 
                    "emit_delegate_vote", 
                    "vote_counts"
                ], 
                "result": null, 
                "questions": 
                [
                    {
                        "a": "ballot/question", 
                        "min": 0, 
                        "max": 1, 
                        "tally_type": "ONE_CHOICE", 
                        "question": "Do you prefer foo or bar?", 
                        "answers": 
                        [
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "fo\\"o"
                            }, 
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "bar"
                            }
                        ], 
                        "randomize_answer_order": true
                    }
                ], 
                "mugshot_url": "/static/img/election_new_form_info.png", 
                "id": 6, 
                "voting_extended_until_date": null, 
                "is_approved": true, 
                "last_modified_at_date": "2013-05-17T16:08:34.046707", 
                "direct_votes_count": 0, 
                "user_has_delegated": false, 
                "short_description": "foo bar foo bar", 
                "is_vote_secret": true, 
                "voters_frozen_at_date": null, 
                "hash": "7a5d754655de9c4bf7e787398e3fe14694826ffe0b7a02548be7a7d385ceea32", 
                "description": "foo bar foo bar", 
                "frozen_at_date": "2013-05-17T16:08:34.046707", 
                "eligibility": null, 
                "parent_election": null, 
                "pretty_name": "foo bar", 
                "archived_at_date": null, 
                "uuid": "93a058aa-5f60-48a9-bddb-df0b56e4f88a", 
                "delegated_votes_count": 0, 
                "percentage_of_participation": 0.0, 
                "name": "foo-bar", 
                "delegated_votes_frozen_at_date": null, 
                "url": "/david/agoraone/election/foo-bar", 
                "voting_ends_at_date": null, 
                "approved_at_date": "2013-05-17T16:08:32.990663", 
                "tiny_hash": null, 
                "created_at_date": "2013-05-17T16:08:32.914089", 
                "agora": 
                {
                    "mugshot_url": "/static/img/agora_default_logo.png", 
                    "name": "agoraone", 
                    "url": "/david/agoraone", 
                    "pretty_name": "AgoraOne", 
                    "content_type": "agora", 
                    "full_name": "david/agoraone", 
                    "short_description": "AgoraOne", 
                    "id": 1
                }, 
                "voting_starts_at_date": "2013-05-17T16:08:34.051455", 
                "election_type": "ONE_CHOICE"
            }
        ]
    }


List requested elections
------------------------

.. http:get:: /agora/(int:agora_id)/requested_elections

   Retrieves requested elections in agora (`agora_id`): elections that have
   been created and requested to take place in the agora but have not been
   approved yet.

   :param agora_id: agora's unique id
   :type agora_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the agora is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/agora/1/requested_elections/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 1, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "creator": "/api/v1/user/1/", 
                "comments_policy": "ANYONE_CAN_COMMENT", 
                "result_tallied_at_date": null, 
                "user_perms": 
                [
                    "edit_details", 
                    "freeze_election", 
                    "archive_election", 
                    "comment"
                ], 
                "result": null, 
                "questions": 
                [
                    {
                        "a": "ballot/question", 
                        "min": 0, 
                        "max": 1, 
                        "tally_type": "ONE_CHOICE", 
                        "question": "Do you prefer foo or bar?", 
                        "answers": 
                        [
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "foo"
                            }, 
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "bar"
                            }
                        ], 
                        "randomize_answer_order": true
                    }
                ], 
                "mugshot_url": "/static/img/election_new_form_info.png", 
                "id": 6, 
                "voting_extended_until_date": null, 
                "is_approved": false, 
                "last_modified_at_date": "2013-05-17T16:13:43.281816", 
                "direct_votes_count": 0, 
                "user_has_delegated": false, 
                "short_description": "foo bar foo bar", 
                "is_vote_secret": true, 
                "voters_frozen_at_date": null, 
                "hash": null, 
                "description": "foo bar foo bar", 
                "frozen_at_date": null, 
                "eligibility": null, 
                "parent_election": null, 
                "pretty_name": "foo bar", 
                "archived_at_date": null, 
                "uuid": "7f820e29-831d-4ff8-a40f-a09cb98396ee", 
                "delegated_votes_count": 0, 
                "percentage_of_participation": 0.0, 
                "name": "foo-bar", 
                "delegated_votes_frozen_at_date": null, 
                "url": "/david/agoraone/election/foo-bar", 
                "voting_ends_at_date": null, 
                "approved_at_date": null, 
                "tiny_hash": null, 
                "created_at_date": "2013-05-17T16:13:43.201443", 
                "agora": 
                {
                    "mugshot_url": "/static/img/agora_default_logo.png", 
                    "name": "agoraone", 
                    "url": "/david/agoraone", 
                    "pretty_name": "AgoraOne", 
                    "content_type": "agora", 
                    "full_name": "david/agoraone", 
                    "short_description": "AgoraOne", 
                    "id": 1
                }, 
                "voting_starts_at_date": null, 
                "election_type": "ONE_CHOICE"
            }
        ]
    }

List archived elections
-----------------------

.. http:get:: /agora/(int:agora_id)/archived_elections

   Retrieves archived elections in agora (`agora_id`): elections that have
   been archived/discarded in the agora.

   :param agora_id: agora's unique id
   :type agora_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the agora is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/agora/1/archived_elections/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 1, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "creator": "/api/v1/user/0/", 
                "comments_policy": "ANYONE_CAN_COMMENT", 
                "result_tallied_at_date": "2013-05-17T16:28:31.501997", 
                "user_perms": [], 
                "result": 
                {
                    "a": "result", 
                    "counts": 
                    [
                        {
                            "a": "question/result/ONE_CHOICE", 
                            "min": 0, 
                            "max": 1, 
                            "tally_type": "ONE_CHOICE", 
                            "question": "Do you prefer foo or bar?", 
                            "answers": 
                            [
                                {
                                    "a": "answer/result/ONE_CHOICE", 
                                    "by_delegation_count": 0, 
                                    "url": "", 
                                    "total_count": 0, 
                                    "by_direct_vote_count": 0, 
                                    "value": "fo\\"o", 
                                    "details": "", 
                                    "total_count_percentage": 0.0
                                }, 
                                {
                                    "a": "answer/result/ONE_CHOICE", 
                                    "by_delegation_count": 0, 
                                    "url": "", 
                                    "total_count": 1, 
                                    "by_direct_vote_count": 1, 
                                    "value": "bar", 
                                    "details": "", 
                                    "total_count_percentage": 100.0
                                }
                            ], 
                            "winners": 
                            [
                                "bar"
                            ], 
                            "dirty_votes": 0, 
                            "randomize_answer_order": true, 
                            "total_votes": 1
                        }
                    ], 
                    "total_votes": 1, 
                    "electorate_count": 1, 
                    "total_delegated_votes": 0
                }, 
                "questions": 
                [
                    {
                        "a": "ballot/question", 
                        "min": 0, 
                        "max": 1, 
                        "tally_type": "ONE_CHOICE", 
                        "question": "Do you prefer foo or bar?", 
                        "answers": 
                        [
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "fo\\"o"
                            }, 
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "bar"
                            }
                        ], 
                        "randomize_answer_order": true
                    }
                ], 
                "mugshot_url": "/static/img/election_new_form_info.png", 
                "id": 6, 
                "voting_extended_until_date": "2013-05-17T16:28:31.478280", 
                "is_approved": true, 
                "last_modified_at_date": "2013-05-17T16:28:31.593120", 
                "direct_votes_count": 1, 
                "user_has_delegated": false, 
                "short_description": "foo bar foo bar", 
                "is_vote_secret": true, 
                "voters_frozen_at_date": "2013-05-17T16:28:31.501997", 
                "hash": "50be7dce7f12d3f81177695a15f1f4a5a1fe36ac4b7dbc66974ea5805291ef49", 
                "description": "foo bar foo bar", 
                "frozen_at_date": "2013-05-17T16:28:30.873941", 
                "eligibility": null, 
                "parent_election": null, 
                "pretty_name": "foo bar", 
                "archived_at_date": "2013-05-17T16:28:31.593120", 
                "uuid": "c82c3fd6-93ba-45e1-8c76-9f4f613170b4", 
                "delegated_votes_count": 0, 
                "percentage_of_participation": 100.0, 
                "name": "foo-bar", 
                "delegated_votes_frozen_at_date": "2013-05-17T16:28:31.501997", 
                "url": "/david/agoraone/election/foo-bar", 
                "voting_ends_at_date": "2013-05-17T16:28:31.472584", 
                "approved_at_date": "2013-05-17T16:28:30.768339", 
                "tiny_hash": null, 
                "created_at_date": "2013-05-17T16:28:30.686342", 
                "agora": 
                {
                    "mugshot_url": "/static/img/agora_default_logo.png", 
                    "name": "agoraone", 
                    "url": "/david/agoraone", 
                    "pretty_name": "AgoraOne", 
                    "content_type": "agora", 
                    "full_name": "david/agoraone", 
                    "short_description": "AgoraOne", 
                    "id": 1
                }, 
                "voting_starts_at_date": "2013-05-17T16:28:30.878968", 
                "election_type": "ONE_CHOICE"
            }
        ]
    }

List approved elections
-----------------------

.. http:get:: /agora/(int:agora_id)/approved_elections

   Retrieves approved elections in agora (`agora_id`): elections that have
   administrators" approval to take place in the agora.

   :param agora_id: agora's unique id
   :type agora_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the agora is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/agora/1/approved_elections/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 1, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "creator": "/api/v1/user/0/", 
                "comments_policy": "ANYONE_CAN_COMMENT", 
                "result_tallied_at_date": null, 
                "user_perms": 
                [
                    "edit_details", 
                    "begin_election", 
                    "freeze_election", 
                    "archive_election", 
                    "comment", 
                    "vote_counts"
                ], 
                "result": null, 
                "questions": 
                [
                    {
                        "a": "ballot/question", 
                        "min": 0, 
                        "max": 1, 
                        "tally_type": "simple", 
                        "question": "question one", 
                        "answers": 
                        [
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "one"
                            }, 
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "two"
                            }, 
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "three"
                            }
                        ], 
                        "randomize_answer_order": true
                    }
                ], 
                "mugshot_url": "/static/img/election_new_form_info.png", 
                "id": 3, 
                "voting_extended_until_date": null, 
                "is_approved": true, 
                "last_modified_at_date": "2012-12-06T19:16:14.014000", 
                "direct_votes_count": 0, 
                "user_has_delegated": false, 
                "short_description": "election one", 
                "is_vote_secret": true, 
                "voters_frozen_at_date": null, 
                "hash": null, 
                "description": "election one", 
                "frozen_at_date": null, 
                "eligibility": null, 
                "parent_election": null, 
                "pretty_name": "electionone", 
                "archived_at_date": null, 
                "uuid": "a36b385a-8f20-4730-a92e-0b9968adac18", 
                "delegated_votes_count": 0, 
                "percentage_of_participation": 0.0, 
                "name": "electionone", 
                "delegated_votes_frozen_at_date": null, 
                "url": "/david/agoraone/election/electionone", 
                "voting_ends_at_date": null, 
                "approved_at_date": null, 
                "tiny_hash": null, 
                "created_at_date": "2012-12-06T19:16:14.004000", 
                "agora": 
                {
                    "mugshot_url": "/static/img/agora_default_logo.png", 
                    "name": "agoraone", 
                    "url": "/david/agoraone", 
                    "pretty_name": "AgoraOne", 
                    "content_type": "agora", 
                    "full_name": "david/agoraone", 
                    "short_description": "AgoraOne", 
                    "id": 1
                }, 
                "voting_starts_at_date": null, 
                "election_type": "ONCE_CHOICE"
            }
        ]
    }


Comments
--------

.. http:get:: /agora/(int:agora_id)/comments

   Retrieves comments in agora (`agora_id`)

   :param agora_id: agora's unique id
   :type agora_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the agora is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/agora/1/comments/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 1, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "geolocation": "[0, 0]", 
                "description": "", 
                "timestamp": "2013-05-17T16:57:08.670832", 
                "type_name": "target_agora_action_object_comment", 
                "actor": 
                {
                    "username": "david", 
                    "first_name": "", 
                    "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
                    "content_type": "user", 
                    "url": "/user/david", 
                    "full_name": "", 
                    "id": 0
                }, 
                "public": true, 
                "verb": "commented", 
                "vote": {}, 
                "action_object": 
                {
                    "comment": "\\t<p>blah blah blah blah.</p>", 
                    "id": 1, 
                    "content_type": "comment"
                }, 
                "id": 1, 
                "target": 
                {
                    "mugshot_url": "/static/img/agora_default_logo.png", 
                    "name": "agoraone", 
                    "url": "/david/agoraone", 
                    "pretty_name": "AgoraOne", 
                    "content_type": "agora", 
                    "full_name": "david/agoraone", 
                    "short_description": "AgoraOne", 
                    "id": 1
                }
            }
        ]
    }

Add comment
-----------

.. http:post:: /agora/(int:agora_id)/add_comment

   Adds a new comment in agora (`agora_id`) with the authenticated user. The user must be authenticated and have ``comment`` permission.

   :param agora_id: agora's unique id
   :type agora_id: int
   :status 200 OK: no error
   :status 403 FORBIDDEN: when the user is not authenticated or has not permission
   :status 404 NOT FOUND: when the agora is not found

   **Example request**:

   .. sourcecode:: http

    POST /api/v1/agora/1/add_comment/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript
    Authorization: ApiKey daniel:204db7bcfafb2deb7506b89eb3b9b715b09905c8

    {
        "comment": "blah blah blah blah."
    }

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8


    {
         ": blah blah blah blah...."
    }

List members
----------

.. http:get:: /agora/(int:agora_id)/members/

   List members of agora (`agora_id`)

   :query offset: offset number. default is 0
   :query limit: limit number. default is 20
   :statuscode 200 OK: no error

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/agora/1/members/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 7, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "username": "david", 
                "first_name": "", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
                "url": "/user/david", 
                "is_active": true, 
                "short_description": "Is a member of 2 agoras and has emitted  0 direct votes.", 
                "last_login": "2013-05-17T17:21:24.492134", 
                "full_name": "", 
                "agora_permissions": [], 
                "id": 0, 
                "date_joined": "2012-11-29T16:08:43.874000"
            }, 
            {
                "username": "user1", 
                "first_name": "Juana Molero", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/cc721459f5b77680bc6a8ba6c9681c46?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FJM.png", 
                "url": "/user/user1", 
                "is_active": true, 
                "short_description": "ultricies. semper vel et, eu laoreet Quisque odio semper ornare. elementum elementum tristique pretium ornare", 
                "last_login": "2013-05-17T17:21:24.975390", 
                "full_name": "Juana Molero", 
                "agora_permissions": [], 
                "id": 1, 
                "date_joined": "2012-11-29T19:37:36.263000"
            }, 
            {
                "username": "user2", 
                "first_name": "Jose Lopez", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/ecdecef1814126d1846e600cbef37a24?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FJL.png", 
                "url": "/user/user2", 
                "is_active": true, 
                "short_description": "justo. consectetur ultricies ullamcorper nisi, volutpat porta. Sed dui. non Nam Ut volutpat Maecenas orci", 
                "last_login": "2013-05-17T17:21:25.928757", 
                "full_name": "Jose Lopez", 
                "agora_permissions": [], 
                "id": 2, 
                "date_joined": "2012-11-29T19:37:36.987000"
            }, 
            {
                "username": "user3", 
                "first_name": "David Perez", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/783711dc4d57fe8542857211b9064d42?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FDP.png", 
                "url": "/user/user3", 
                "is_active": true, 
                "short_description": "dictum, orci Fusce Nullam et Nunc convallis Curabitur ante semper convallis euismod et a purus", 
                "last_login": "2013-05-17T17:21:22.651395", 
                "full_name": "David Perez", 
                "agora_permissions": [], 
                "id": 3, 
                "date_joined": "2012-11-29T19:37:37.332000"
            }, 
            {
                "username": "user4", 
                "first_name": "Antonio Bonilla", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/562c89a550b820fe2f63365202f07eb0?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FAB.png", 
                "url": "/user/user4", 
                "is_active": true, 
                "short_description": "Nullam fringilla ut ultricies elit. semper erat, gravida fermentum Quisque nec. gravida elit. nulla nec", 
                "last_login": "2013-05-17T17:21:23.111299", 
                "full_name": "Antonio Bonilla", 
                "agora_permissions": [], 
                "id": 4, 
                "date_joined": "2012-11-29T19:37:37.662000"
            }, 
            {
                "username": "user5", 
                "first_name": "Maria Ariza", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/4363106ea31735235c6c6b911ab460c2?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FMA.png", 
                "url": "/user/user5", 
                "is_active": true, 
                "short_description": "Nullam vestibulum bibendum Maecenas eu, Curabitur sit erat congue amet a ultricies velit gravida dictum,", 
                "last_login": "2013-05-17T17:21:23.573394", 
                "full_name": "Maria Ariza", 
                "agora_permissions": [], 
                "id": 5, 
                "date_joined": "2012-11-29T19:37:37.993000"
            }, 
            {
                "username": "user6", 
                "first_name": "Mariano Ariz\\u00f3n", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/013c1f55acdb49dfe716cacc231c8c47?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FMA.png", 
                "url": "/user/user6", 
                "is_active": true, 
                "short_description": "Is a member of 1 agoras and has emitted  0 direct votes.", 
                "last_login": "2013-05-17T17:21:24.032687", 
                "full_name": "Mariano Ariz\\u00f3n", 
                "agora_permissions": [], 
                "id": 6, 
                "date_joined": "2012-11-29T19:37:37.993000"
            }
        ]
    }

Resource: User
==============

Get User settings
-----------------

.. http:get:: /user/settings/

   Shows authenticated user information

   :statuscode 200 OK: no error

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/user/settings/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "username": "david", 
        "email": "david@example.com",
        "first_name": "", 
        "last_name": "", 
        "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
        "url": "/user/david", 
        "is_active": true, 
        "last_login": "2013-05-17T17:45:01.597510", 
        "full_name": "", 
        "biography": "blah blah",
        "short_description": "Is a member of 2 agoras and has emitted  0 direct votes.", 
        "id": 0, 
        "date_joined": "2012-11-29T16:08:43.874000"
    }


Put User settings
-----------------

.. http:put:: /user/settings/

   Modifies the settings of the authenticated user. It modifies only the set
   parameters.

   :form use_gravatar: If you want the user mugshot to be a gravatar
   :form use_gravatar: If you want the user mugshot to be user initials
   :form biography: Users' long description
   :form email: User's email
   :form email_updates: True if the uswer wants to receive mail updates
   :form old_password: current password. needed to change password
   :form password1: new password
   :form password2: new password
   :status 202 CREATED: when user is modified correctly
   :status 400 BAD REQUEST: wen the form parameters are invalid

   **Example request**:

   .. sourcecode:: http

    PUT /api/v1/user/settings/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

    {
        "username": "new_username"
    }

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

User register
-------------

.. http:post:: /user/register/

   Registers a new user.

   :form email: New user email address. Required.
   :form password1: New user password. Required.
   :form password2: New user password again. It must be equal to passwors1. Required.
   :form username: The new user identifier, It should be unique in the application. Required.
   :status 200 OK: when the user is registered correctly
   :status 400 BAD REQUEST: when the form parameters are invalid

   **Example request**:

   .. sourcecode:: http

    POST /api/v1/user/register/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

    {
        "username": "danigm",
        "password1": "my super secret password",
        "password2": "my super secret password",
        "email": "danigm@wadobo.com"
    }

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

User login
----------

.. http:post:: /user/login/

   Login in the application using session. It's only used in web requests.

   :form identification: The user username to login. Required.
   :form password: The user password. Required.
   :status 200 OK: when the user is loged in correctly
   :status 400 BAD REQUEST: when the form parameters are invalid

   **Example request**:

   .. sourcecode:: http

    POST /api/v1/user/login/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

    {
        "identification": "danigm",
        "password": "my super secret password"
    }

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

User logout
-----------

.. http:post:: /user/logout/

   Logout in the application.

   :status 200 OK: when the user is loged in correctly

   **Example request**:

   .. sourcecode:: http

    POST /api/v1/user/logout/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

Check username is available
---------------------------

.. http:get:: /user/username_available/

   Checks if a username is available

   :status 200 OK: when the username is available
   :status 400 BAD REQUEST: when the username isn"t available

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/user/username_available/?username=danigm HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

Reset Password 
--------------

.. http:post:: /user/password_reset/

   Given the email of an user, sends an email with a reset password link to that user.

   :status 200 OK: when everything is ok
   :status 400 BAD REQUEST: when email given is not one of an existing user

   **Example request**:

   .. sourcecode:: http

    POST /api/v1/user/password_reset/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

    {
        "email": "david@david.com"
    }

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        
    }

Disable current user
--------------------

.. http:post:: /user/disable/

   Disable the currently authenticated user.

   :status 200 OK: when everything is ok
   :status 400 METHOD NOT ALLOWED: when email given is not one of an existing user

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/user/disable/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

List current user agoras
------------------------

.. http:get:: /user/agoras/

   List authenticated user's agoras. Requires an user to be authenticated.

   :query offset: offset number. default is 0
   :query limit: limit number. default is 20
   :statuscode 200 OK: no error
   :statuscode 403 FORBIDDEN: when the user is not authenticated

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/user/agoras/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 2, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "membership_policy": "ANYONE_CAN_JOIN", 
                "mugshot_url": "/static/img/agora_default_logo.png", 
                "name": "agoraone", 
                "creator": 
                {
                    "username": "david", 
                    "first_name": "", 
                    "last_name": "", 
                    "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
                    "url": "/user/david", 
                    "is_active": true, 
                    "last_login": "2013-05-17T18:14:38.647876", 
                    "full_name": "", 
                    "short_description": "Is a member of 2 agoras and has emitted  0 direct votes.", 
                    "id": 0, 
                    "date_joined": "2012-11-29T16:08:43.874000"
                }, 
                "eligibility": null, 
                "comments_policy": "ANYONE_CAN_COMMENT", 
                "id": 1, 
                "pretty_name": "AgoraOne", 
                "url": "/david/agoraone", 
                "created_at_date": "2013-05-17T18:14:37.839919", 
                "archived_at_date": null, 
                "full_name": "david/agoraone", 
                "short_description": "AgoraOne", 
                "image_url": "", 
                "extra_data": null, 
                "is_vote_secret": false, 
                "election_type": "ONCE_CHOICE", 
                "biography": ""
            }, 
            {
                "membership_policy": "ANYONE_CAN_JOIN", 
                "mugshot_url": "/static/img/agora_default_logo.png", 
                "name": "agoratwo", 
                "creator": 
                {
                    "username": "david", 
                    "first_name": "", 
                    "last_name": "", 
                    "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
                    "url": "/user/david", 
                    "is_active": true, 
                    "last_login": "2013-05-17T18:14:38.647876", 
                    "full_name": "", 
                    "short_description": "Is a member of 2 agoras and has emitted  0 direct votes.", 
                    "id": 0, 
                    "date_joined": "2012-11-29T16:08:43.874000"
                }, 
                "eligibility": null, 
                "comments_policy": "ANYONE_CAN_COMMENT", 
                "id": 2, 
                "pretty_name": "AgoraTwo", 
                "url": "/david/agoratwo", 
                "created_at_date": "2013-05-17T18:14:37.855397", 
                "archived_at_date": null, 
                "full_name": "david/agoratwo", 
                "short_description": "AgoraTwo", 
                "image_url": "", 
                "extra_data": null, 
                "is_vote_secret": true, 
                "election_type": "ONCE_CHOICE", 
                "biography": ""
            }
        ]
    }


List agoras of a given user
---------------------------

.. http:get:: /user/(int:userid)/agoras/

   List agoras of the user with (`userid`).

   :query offset: offset number. default is 0
   :query limit: limit number. default is 20
   :statuscode 200 OK: no error
   :statuscode 404 NOT FOUND: the requested user does not exist

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/user/(int:userid)/agoras/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 2, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "membership_policy": "ANYONE_CAN_JOIN", 
                "mugshot_url": "/static/img/agora_default_logo.png", 
                "name": "agoraone", 
                "creator": 
                {
                    "username": "david", 
                    "first_name": "", 
                    "last_name": "", 
                    "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
                    "url": "/user/david", 
                    "is_active": true, 
                    "last_login": "2012-11-29T18:18:46.837000", 
                    "full_name": "", 
                    "short_description": "Is a member of 2 agoras and has emitted  0 direct votes.", 
                    "id": 0, 
                    "date_joined": "2012-11-29T16:08:43.874000"
                }, 
                "eligibility": null, 
                "comments_policy": "ANYONE_CAN_COMMENT", 
                "id": 1, 
                "pretty_name": "AgoraOne", 
                "url": "/david/agoraone", 
                "created_at_date": "2013-05-17T18:17:04.412905", 
                "archived_at_date": null, 
                "full_name": "david/agoraone", 
                "short_description": "AgoraOne", 
                "image_url": "", 
                "extra_data": null, 
                "is_vote_secret": false, 
                "election_type": "ONCE_CHOICE", 
                "biography": ""
            }, 
            {
                "membership_policy": "ANYONE_CAN_JOIN", 
                "mugshot_url": "/static/img/agora_default_logo.png", 
                "name": "agoratwo", 
                "creator": 
                {
                    "username": "david", 
                    "first_name": "", 
                    "last_name": "", 
                    "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
                    "url": "/user/david", 
                    "is_active": true, 
                    "last_login": "2012-11-29T18:18:46.837000", 
                    "full_name": "", 
                    "short_description": "Is a member of 2 agoras and has emitted  0 direct votes.", 
                    "id": 0, 
                    "date_joined": "2012-11-29T16:08:43.874000"
                }, 
                "eligibility": null, 
                "comments_policy": "ANYONE_CAN_COMMENT", 
                "id": 2, 
                "pretty_name": "AgoraTwo", 
                "url": "/david/agoratwo", 
                "created_at_date": "2013-05-17T18:17:04.428307", 
                "archived_at_date": null, 
                "full_name": "david/agoratwo", 
                "short_description": "AgoraTwo", 
                "image_url": "", 
                "extra_data": null, 
                "is_vote_secret": true, 
                "election_type": "ONCE_CHOICE", 
                "biography": ""
            }
        ]
    }


List elections this user can vote in
------------------------------------

.. http:get:: /user/open_elections/

   List elections the authenticated  user can vote in. These are the elections
   that are open in the agoras in which this user is a member. Requires an user
   to be authenticated.

   :query offset: offset number. default is 0
   :query limit: limit number. default is 20
   :query q: search string, not required. filters in the election name and description
   :statuscode 200 OK: no error
   :statuscode 403 FORBIDDEN: when the user is not authenticated

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/user/open_elections/?q=vota HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 1, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "creator": "/api/v1/user/0/", 
                "comments_policy": "ANYONE_CAN_COMMENT", 
                "result_tallied_at_date": null, 
                "user_perms": 
                [
                    "end_election", 
                    "archive_election", 
                    "comment", 
                    "emit_direct_vote", 
                    "emit_delegate_vote", 
                    "vote_counts"
                ], 
                "result": null, 
                "questions": 
                [
                    {
                        "a": "ballot/question", 
                        "min": 0, 
                        "max": 1, 
                        "tally_type": "ONE_CHOICE", 
                        "question": "Do you prefer foo or bar?", 
                        "answers": [
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "fo\\"o"
                            }, 
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "bar"
                            }
                        ], 
                        "randomize_answer_order": true
                    }
                ], 
                "id": 6, 
                "hash": "f5d139f50af19b500ef7089fef7cae6ebd9e221af6d287dc207cf6ae7264d740", 
                "has_user_voted_via_a_delegate": false, 
                "voting_extended_until_date": null, 
                "is_approved": true, 
                "last_modified_at_date": "2013-05-17T18:24:29.388901", 
                "direct_votes_count": 0, 
                "user_has_delegated": false, 
                "has_user_voted": false, 
                "short_description": "foo bar foo bar", 
                "is_vote_secret": true, 
                "voters_frozen_at_date": null, 
                "mugshot_url": "/static/img/election_new_form_info.png", 
                "description": "foo bar foo bar", 
                "frozen_at_date": "2013-05-17T18:24:29.388901", 
                "eligibility": null, 
                "parent_election": null, 
                "pretty_name": "foo bar", 
                "archived_at_date": null, 
                "uuid": "9eedf9f3-4e9e-45d2-a9f0-28a80b4cc244", 
                "delegated_votes_count": 0, 
                "percentage_of_participation": 0.0, 
                "name": "foo-bar", 
                "delegated_votes_frozen_at_date": null, 
                "url": "/david/agoraone/election/foo-bar", 
                "voting_ends_at_date": null, 
                "approved_at_date": "2013-05-17T18:24:28.307743", 
                "tiny_hash": null, 
                "created_at_date": "2013-05-17T18:24:28.223238", 
                "agora": 
                {
                    "mugshot_url": "/static/img/agora_default_logo.png", 
                    "name": "agoraone", 
                    "url": "/david/agoraone", 
                    "pretty_name": "AgoraOne", 
                    "content_type": "agora", 
                    "full_name": "david/agoraone", 
                    "short_description": "AgoraOne", 
                    "id": 1
                }, 
                "voting_starts_at_date": "2013-05-17T18:24:29.394027", 
                "election_type": "ONE_CHOICE"
            }
        ]
    }


List of valid usernames
-----------------------

.. http:get:: user/set_username/(string:username1);(string:username2);.../

   List of users from the requested list of usernames (`username1`, `username2`, etc). Only usernames referring to existing users will be listed.

   :statuscode 200 OK: no error
   :statuscode 403 FORBIDDEN: when the user is not authenticated

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/user/set_username/david;user1/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "objects": 
        [
            {
                "username": "david", 
                "first_name": "", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
                "url": "/user/david", 
                "is_active": true, 
                "last_login": "2012-11-29T18:18:46.837000", 
                "full_name": "", 
                "short_description": "Is a member of 2 agoras and has emitted  0 direct votes.", 
                "id": 0, 
                "date_joined": "2012-11-29T16:08:43.874000"
            }, 
            {
                "username": "user1", 
                "first_name": "Juana Molero", 
                "last_name": "", 
                "mugshot_url": "http://www.gravatar.com/avatar/cc721459f5b77680bc6a8ba6c9681c46?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FJM.png", 
                "url": "/user/user1", 
                "is_active": true, 
                "last_login": "2012-11-29T19:37:36.263000", 
                "full_name": "Juana Molero", 
                "short_description": "ultricies. semper vel et, eu laoreet Quisque odio semper ornare. elementum elementum tristique pretium ornare", 
                "id": 1, 
                "date_joined": "2012-11-29T19:37:36.263000"
            }
        ]
    }





Resource: Search
================

This resource allows searches, using Haystack


.. http:get:: /search/

   Searches objects using haystack. It can return agoras, elections and profiles.

   :query offset: offset number. default is 0
   :query limit: limit number. default is 20
   :query q: search string, not required
   :query model: filtering by object type, not required. Possible values are: ``agora``, ``election``, ``castvote``.
   :statuscode 200 OK: no error

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/search/?q=agoraone HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "previous": null, 
            "total_count": 3, 
            "offset": 0, 
            "limit": 20, 
            "next": null
        }, 
        "objects": 
        [
            {
                "obj": 
                {
                    "mugshot_url": "/static/img/agora_default_logo.png", 
                    "name": "agoraone", 
                    "url": "/david/agoraone", 
                    "pretty_name": "AgoraOne", 
                    "content_type": "agora", 
                    "full_name": "david/agoraone", 
                    "short_description": "AgoraOne", 
                    "id": 1
                }
            }, 
            {
                "obj": 
                {
                    "mugshot_url": "/static/img/election_new_form_info.png", 
                    "name": "electionone", 
                    "url": "/david/agoraone/election/electionone", 
                    "pretty_name": "electionone", 
                    "agora": 
                    {
                        "mugshot_url": "/static/img/agora_default_logo.png", 
                        "name": "agoraone", 
                        "url": "/david/agoraone", 
                        "pretty_name": "AgoraOne", 
                        "content_type": "agora", 
                        "full_name": "david/agoraone", 
                        "short_description": "AgoraOne", 
                        "id": 1
                    }, 
                    "content_type": "election", 
                    "short_description": "election one", 
                    "id": 3
                }
            }, 
            {
                "obj": 
                {
                    "mugshot_url": "/static/img/election_new_form_info.png", 
                    "name": "electiontwo", 
                    "url": "/david/agoraone/election/electiontwo", 
                    "pretty_name": "electiontwo", 
                    "agora": 
                    {
                        "mugshot_url": "/static/img/agora_default_logo.png", 
                        "name": "agoraone", 
                        "url": "/david/agoraone", 
                        "pretty_name": "AgoraOne", 
                        "content_type": "agora", 
                        "full_name": "david/agoraone", 
                        "short_description": "AgoraOne", 
                        "id": 1
                    }, 
                    "content_type": "election", 
                    "short_description": "election two", 
                    "id": 4
                }
            }
        ]
    }

Resource: Election
==================

This resource represents an election


.. http:get:: /election/

   Lists elections

   :query offset: offset number. default is 0
   :query limit: limit number. default is 20
   :statuscode 200 OK: no error

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/election/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "previous": null, 
            "total_count": 1, 
            "offset": 0, 
            "limit": 20, 
            "next": null
        }, 
        "objects": 
        [
            {
                "creator": "/api/v1/user/0/", 
                "comments_policy": "ANYONE_CAN_COMMENT", 
                "result_tallied_at_date": null, 
                "user_perms": [], 
                "result": null, 
                "questions": 
                [
                    {
                        "a": "ballot/question", 
                        "min": 0, 
                        "max": 1, 
                        "tally_type": "simple", 
                        "question": "question three", 
                        "answers": 
                        [
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "one"
                            }, 
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "two"
                            }, 
                            {
                                "a": "ballot/answer", 
                                "url": "", 
                                "details": "", 
                                "value": "three"
                            }
                        ], 
                        "randomize_answer_order": true
                    }
                ], 
                "mugshot_url": "/static/img/election_new_form_info.png", 
                "id": 5, 
                "voting_extended_until_date": null, 
                "is_approved": true, 
                "last_modified_at_date": "2012-12-06T19:17:14.457000", 
                "direct_votes_count": 0, 
                "user_has_delegated": false, 
                "short_description": "election three", 
                "is_vote_secret": false, 
                "voters_frozen_at_date": null, 
                "hash": null, 
                "description": "election three", 
                "frozen_at_date": null, 
                "eligibility": null, 
                "parent_election": null, 
                "pretty_name": "electionthree", 
                "archived_at_date": null, 
                "uuid": "a7be018c-2111-419b-b9b8-c78fd0bc9912", 
                "delegated_votes_count": 0, 
                "percentage_of_participation": 0.0, 
                "name": "electionthree", 
                "delegated_votes_frozen_at_date": null, 
                "url": "/david/agoratwo/election/electionthree", 
                "voting_ends_at_date": null, 
                "approved_at_date": null, 
                "tiny_hash": null, 
                "created_at_date": "2012-12-06T19:17:14.446000", 
                "agora": 
                {
                    "mugshot_url": "/static/img/agora_default_logo.png", 
                    "name": "agoratwo", 
                    "url": "/david/agoratwo", 
                    "pretty_name": "AgoraTwo", 
                    "content_type": "agora", 
                    "full_name": "david/agoratwo", 
                    "short_description": "AgoraTwo", 
                    "id": 2
                }, 
                "voting_starts_at_date": null, 
                "election_type": "ONCE_CHOICE"
            }
        ]
    }

Retrieve an election
--------------------

.. http:get:: /election/(int:election_id)

   Retrieves an election (`election_id`).

   :param election_id: election's unique id
   :type election_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the election is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/election/3/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "creator": "/api/v1/user/0/", 
        "comments_policy": "ANYONE_CAN_COMMENT", 
        "result_tallied_at_date": null, 
        "user_perms": [], 
        "result": null, 
        "questions": 
        [
            {
                "a": "ballot/question", 
                "min": 0, 
                "max": 1, 
                "tally_type": "simple", 
                "question": "question one", 
                "answers": 
                [
                    {
                        "a": "ballot/answer", 
                        "url": "", 
                        "details": "", 
                        "value": "one"
                    }, 
                    {
                        "a": "ballot/answer", 
                        "url": "", 
                        "details": "", 
                        "value": "two"
                    }, 
                    {
                        "a": "ballot/answer", 
                        "url": "", 
                        "details": "", 
                        "value": "three"
                    }
                ], 
                "randomize_answer_order": true
            }
        ], 
        "mugshot_url": "/static/img/election_new_form_info.png", 
        "id": 3, 
        "voting_extended_until_date": null, 
        "is_approved": true, 
        "last_modified_at_date": "2012-12-06T19:16:14.014000", 
        "direct_votes_count": 0, 
        "user_has_delegated": false, 
        "short_description": "election one", 
        "is_vote_secret": true, 
        "voters_frozen_at_date": null, 
        "hash": null, 
        "description": "election one", 
        "frozen_at_date": null, 
        "eligibility": null, 
        "parent_election": null, 
        "pretty_name": "electionone", 
        "archived_at_date": null, 
        "uuid": "a36b385a-8f20-4730-a92e-0b9968adac18", 
        "delegated_votes_count": 0, 
        "percentage_of_participation": 0.0, 
        "name": "electionone", 
        "delegated_votes_frozen_at_date": null, 
        "url": "/david/agoraone/election/electionone", 
        "voting_ends_at_date": null, 
        "approved_at_date": null, 
        "tiny_hash": null, 
        "created_at_date": "2012-12-06T19:16:14.004000", 
        "agora": 
        {
            "mugshot_url": "/static/img/agora_default_logo.png", 
            "name": "agoraone", 
            "url": "/david/agoraone", 
            "pretty_name": "AgoraOne", 
            "content_type": "agora", 
            "full_name": "david/agoraone", 
            "short_description": "AgoraOne", 
            "id": 1
        }, 
        "voting_starts_at_date": null, 
        "election_type": "ONCE_CHOICE"
    }


Modify election
---------------

.. http:put:: /agora/(int:election_id)/

   Modifies an election (`election_id`). Requires the authenticated user to be election administrator. All the parameters are optional: you only need to suply the parameters you want to change.

   :form question: a text with the main question in the election. 
   :form answers: a list with at least two possible answers to the question.
   :form pretty_name: A title for the election. 
   :form description: A description text for the election. It can follow restructured text format and be as large as needed. 
   :form is_vote_secret: A boolean specifiying if the direct votes must all be secret or not. 
   :form from_date: A string representing the starting date of the election, in format '%Y-%m-%dT%H:%M:%S'.
   :form to_date: A string representing the end date of the election, in format '%Y-%m-%dT%H:%M:%S'.
   :status 202 CREATED: when agora is modified correctly
   :status 403 FORBIDDEN: when the user has no agora administration permissions
   :status 400 BAD REQUEST: when the form parameters are invalid

   .. sourcecode:: http

    PUT /api/v1/agora/5/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

    {
        "from_date": "2020-02-18T20:13:00"
    }

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 202 ACCEPTED
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8


    {
        "archived_at_date": null,
        "biography": "bio",
        "comments_policy": "ANYONE_CAN_COMMENT",
        "created_at_date": "2013-05-17T22:15:42.508978",
        "creator": 
        {
            "date_joined": "2012-11-29T16:08:43.874000",
            "first_name": "",
            "full_name": "",
            "id": 0,
            "is_active": true,
            "last_login": "2013-05-17T22:15:38.096210",
            "last_name": "",
            "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png",
            "short_description": "Is a member of 2 agoras and has emitted  0 direct votes.",
            "url": "/user/david",
            "username": "david"
        },
        "election_type": "ONCE_CHOICE",
        "eligibility": null,
        "extra_data": null,
        "full_name": "david/agoraone",
        "id": 1,
        "image_url": "",
        "is_vote_secret": false,
        "membership_policy": "ANYONE_CAN_JOIN",
        "mugshot_url": "/static/img/agora_default_logo.png",
        "name": "agoraone",
        "pretty_name": "updated name",
        "short_description": "new desc",
        "url": "/david/agoraone"
    }

List all votes
--------------

.. http:get:: /election/(int:election_id)/all_votes

   Lists all votes in the election (`election_id`)

   :param election_id: election's unique id
   :type election_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the election is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/election/5/all_votes/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 1, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "voter": 
                {
                    "username": "user2", 
                    "first_name": "Jose Lopez", 
                    "last_name": "", 
                    "mugshot_url": "http://www.gravatar.com/avatar/ecdecef1814126d1846e600cbef37a24?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FJL.png", 
                    "url": "/user/user2", 
                    "is_active": true, 
                    "last_login": "2013-05-17T22:47:36.725849", 
                    "full_name": "Jose Lopez", 
                    "short_description": "justo. consectetur ultricies ullamcorper nisi, volutpat porta. Sed dui. non Nam Ut volutpat Maecenas orci", 
                    "id": 2, 
                    "date_joined": "2012-11-29T19:37:36.987000"
                }, 
                "hash": "cf2bf1afcda9a53a659ac005b8d6f44b356bc51073db8bf5ccbc9e24c49309f2", 
                "public_data": 
                {
                    "a": "vote", 
                    "election_uuid": "3b1a8ab9-8b7e-4c23-8ae3-40ee8444e5d8", 
                    "election_hash": {
                        "a": "hash/sha256/value", 
                        "value": "1cf3f094e9289371ef32a065ab7fef82976315f1d68ad87bc418d75a1f3a1619"
                    }, 
                    "answers": 
                    [
                        {
                            "a": "plaintext-answer", 
                            "choices": 
                            [
                                "foo"
                            ]
                        }
                    ]
                }, 
                "casted_at_date": "2013-05-17T22:47:36.753031", 
                "is_counted": true, 
                "is_direct": true, 
                "invalidated_at_date": null, 
                "reason": "", 
                "election": "/api/v1/election/6/", 
                "tiny_hash": null, 
                "is_public": true, 
                "id": 5, 
                "action_id": 25
            }
        ]
    }

List cast votes
---------------

.. http:get:: /election/(int:election_id)/cast_votes

   Lists cast votes in the election (`election_id`)

   :param election_id: election's unique id
   :type election_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the election is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/election/5/cast_votes/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 1, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "voter": 
                {
                    "username": "user1", 
                    "first_name": "Juana Molero", 
                    "last_name": "", 
                    "mugshot_url": "http://www.gravatar.com/avatar/cc721459f5b77680bc6a8ba6c9681c46?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FJM.png", 
                    "url": "/user/user1", 
                    "is_active": true, 
                    "last_login": "2013-05-17T22:52:28.258599", 
                    "full_name": "Juana Molero", 
                    "short_description": "ultricies. semper vel et, eu laoreet Quisque odio semper ornare. elementum elementum tristique pretium ornare", 
                    "id": 1, 
                    "date_joined": "2012-11-29T19:37:36.263000"
                }, 
                "hash": "9b79aa77a3f7f131e267dc773b7e1a41a3532088239ee24a5e91452c6b203ad8", 
                "public_data": 
                {
                    "a": "vote", 
                    "election_uuid": "9ec1f881-ffed-4c6a-a837-78203d7bb303", 
                    "election_hash": 
                    {
                        "a": "hash/sha256/value", 
                        "value": "772bef83e59004d872b5c0161440373b615958561574f392af7a1a4a8c2a7ea3"
                    }, 
                    "answers": 
                    [
                        {
                            "a": "plaintext-answer", 
                            "choices": 
                            [
                                "foo"
                            ]
                        }
                    ]
                }, 
                "casted_at_date": "2013-05-17T22:52:25.449726", 
                "is_counted": false, 
                "is_direct": true, 
                "invalidated_at_date": "2013-05-17T22:52:28.284395", 
                "reason": "", 
                "election": "/api/v1/election/6/", 
                "tiny_hash": null, 
                "is_public": true, 
                "id": 4, 
                "action_id": 23
            }
        ]
    }

List delegated votes
--------------------

.. http:get:: /election/(int:election_id)/delegated_votes

   Lists delegated votes in the election (`election_id`)

   :param election_id: election's unique id
   :type election_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the election is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/election/5/delegated_votes/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 1, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "voter": 
                {
                    "username": "david", 
                    "first_name": "", 
                    "last_name": "", 
                    "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
                    "url": "/user/david", 
                    "is_active": true, 
                    "last_login": "2013-05-17T22:55:42.573888", 
                    "full_name": "", 
                    "short_description": "Is a member of 2 agoras and has emitted  0 direct votes.", 
                    "id": 0, 
                    "date_joined": "2012-11-29T16:08:43.874000"
                }, 
                "hash": "5ae8b6f7aecf1de2169697f67b91f0ce5e5856484f9789ce1502958662dc24e6", 
                "public_data": 
                {
                    "a": "delegated-vote", 
                    "election_uuid": "cd9fd683-42e6-4c7e-81da-c9e10fceede2", 
                    "election_hash": 
                    {
                        "a": "hash/sha256/value", 
                        "value": "0018facf373870b5cf6905c682d770cae12cfd3768608a1842d2d45b04b8f384"
                    }, 
                    "answers": 
                    [
                        {
                            "a": "plaintext-delegate", 
                            "choices": 
                            [
                                {
                                    "username": "user1", 
                                    "user_name": "Juana Molero", 
                                    "user_id": 1
                                }
                            ]
                        }
                    ]
                }, 
                "casted_at_date": "2013-05-17T22:55:36.343185", 
                "is_counted": true, 
                "is_direct": false, 
                "invalidated_at_date": null, 
                "reason": "", 
                "election": "/api/v1/election/1/", 
                "tiny_hash": null, 
                "is_public": true, 
                "id": 1, 
                "action_id": null
            }
        ]
    }

List votes from delegates
-------------------------

.. http:get:: /election/(int:election_id)/votes_from_delegates

   Lists votes from delegates in the election (`election_id`)

   :param election_id: election's unique id
   :type election_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the election is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/election/5/votes_from_delegates/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 1, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "voter": 
                {
                    "username": "user2", 
                    "first_name": "Jose Lopez", 
                    "last_name": "", 
                    "mugshot_url": "http://www.gravatar.com/avatar/ecdecef1814126d1846e600cbef37a24?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FJL.png", 
                    "url": "/user/user2", 
                    "is_active": true, 
                    "last_login": "2013-05-17T22:58:27.127776", 
                    "full_name": "Jose Lopez", 
                    "short_description": "justo. consectetur ultricies ullamcorper nisi, volutpat porta. Sed dui. non Nam Ut volutpat Maecenas orci", 
                    "id": 2, 
                    "date_joined": "2012-11-29T19:37:36.987000"
                }, 
                "hash": "86d3731e6124c9de2a3ff03f4f8a157a1109a87cf760164f922c4fc8add0c21c", 
                "public_data": 
                {
                    "a": "vote", 
                    "election_uuid": "a0350de8-0072-4c43-b49e-2ea51ae88c7a", 
                    "election_hash": 
                    {
                        "a": "hash/sha256/value", 
                        "value": "e9a38ec615bfd439e6b4db8cc56d6d31a08dc501303e2524db625f27158f0295"
                    }, 
                    "answers": 
                    [
                        {
                            "a": "plaintext-answer", 
                            "choices": 
                            [
                                "foo"
                            ]
                        }
                    ]
                }, 
                "casted_at_date": "2013-05-17T22:58:27.154732", 
                "is_counted": true, 
                "is_direct": true, 
                "invalidated_at_date": null, 
                "reason": "", 
                "election": "/api/v1/election/6/", 
                "tiny_hash": null, 
                "is_public": true, 
                "id": 5, 
                "action_id": 25
            }
        ]
    }

Direct votes
------------

.. http:get:: /election/(int:election_id)/direct_votes

   Lists direct votes in the election (`election_id`)

   :param election_id: election's unique id
   :type election_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the election is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/election/5/direct_votes/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 1, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "voter": 
                {
                    "username": "user2", 
                    "first_name": "Jose Lopez", 
                    "last_name": "", 
                    "mugshot_url": "http://www.gravatar.com/avatar/ecdecef1814126d1846e600cbef37a24?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FJL.png", 
                    "url": "/user/user2", 
                    "is_active": true, 
                    "last_login": "2013-05-17T23:00:52.341482", 
                    "full_name": "Jose Lopez", 
                    "short_description": "justo. consectetur ultricies ullamcorper nisi, volutpat porta. Sed dui. non Nam Ut volutpat Maecenas orci", 
                    "id": 2, 
                    "date_joined": "2012-11-29T19:37:36.987000"
                }, 
                "hash": "3961449f925e5b798c30ca532f54e1b860de6e59a4601f93004f8a88b4f3ab8f", 
                "public_data": 
                {
                    "a": "vote", 
                    "election_uuid": "a86c4989-2d26-446a-8cef-dde824479b77", 
                    "election_hash": 
                    {
                        "a": "hash/sha256/value", 
                        "value": "7148fb6792603ab5f03ce1623919cf4d76f51ac2f78179faf0b23a9f1f664f2d"
                    }, 
                    "answers": 
                    [
                        {
                            "a": "plaintext-answer", 
                            "choices": 
                            [
                                "foo"
                            ]
                        }
                    ]
                }, 
                "casted_at_date": "2013-05-17T23:00:52.368171", 
                "is_counted": true, 
                "is_direct": true, 
                "invalidated_at_date": null, 
                "reason": "", 
                "election": "/api/v1/election/6/", 
                "tiny_hash": null, 
                "is_public": true, 
                "id": 5, 
                "action_id": 25
            }
        ]
    }


List comments
-------------

.. http:get:: /election/(int:election_id)/comments

   Retrieves comments in election (`election_id`)

   :param election_id: election's unique id
   :type election_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the election is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/election/1/comments/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "total_count": 1, 
            "limit": 20, 
            "offset": 0
        }, 
        "objects": 
        [
            {
                "geolocation": "[0, 0]", 
                "description": "", 
                "timestamp": "2013-05-17T23:06:06.068504", 
                "type_name": "target_election_action_object_comment", 
                "actor": 
                {
                    "username": "david", 
                    "first_name": "", 
                    "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
                    "content_type": "user", 
                    "url": "/user/david", 
                    "full_name": "", 
                    "id": 0
                }, 
                "public": true, 
                "verb": "commented", 
                "vote": {}, 
                "action_object": 
                {
                    "comment": "\\t<p>blah blah blah blah.</p>", 
                    "id": 1, 
                    "content_type": "comment"
                }, 
                "id": 1, 
                "target": 
                {
                    "mugshot_url": "/static/img/election_new_form_info.png", 
                    "name": "electionone", 
                    "url": "/david/agoraone/election/electionone", 
                    "pretty_name": "electionone", 
                    "agora": 
                    {
                        "mugshot_url": "/static/img/agora_default_logo.png", 
                        "name": "agoraone", 
                        "url": "/david/agoraone", 
                        "pretty_name": "AgoraOne", 
                        "content_type": "agora", 
                        "full_name": "david/agoraone", 
                        "short_description": "AgoraOne", 
                        "id": 1
                    }, 
                    "content_type": "election", 
                    "short_description": "election one", 
                    "id": 3
                }
            }
        ]
    }

Add comment
-----------

.. http:post:: /election/(int:election_id)/add_comment

   Adds a new comment in election (`election_id`) with the authenticated user. The user must be authenticated and have ``comment`` permission.

   :param election_id: election's unique id
   :type election_id: int
   :status 200 OK: no error
   :status 403 FORBIDDEN: when the user is not authenticated or has not permission
   :status 404 NOT FOUND: when the election is not found

   **Example request**:

   .. sourcecode:: http

    POST /api/v1/election/1/add_comment/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript
    Authorization: ApiKey daniel:204db7bcfafb2deb7506b89eb3b9b715b09905c8

    {
        "comment": "blah blah blah blah...."
    }

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        ": blah blah blah blah...."
    }


Execute an action
-----------------

.. http:post:: /election/(int:election_id)/action

   Request to execute an action in the election (`election_id`).

   The available actions are:

   **get_permissions**

   Returns a list of the permissions that the authenticated user has over the specified election.

   **approve**

   Approves the election. The authenticated user must have ``approve_election`` permission on the election and the election must have be pending approval.

   **start**

   Starts the election, so that the electorate can start voting on it. The authenticated user must have ``begin_election`` permission on the election.

   **stop**

   Stops the voting period of the election. The authenticated user must have ``end_election`` permission on the election.

   **archive**

   Archives the election. The authenticated user must have ``archive_election`` permission on the election.

   **vote**

   The authenticated emits a direct vote in this election, providing the following fields:
    * **is_vote_secret**: boolean indicating if the vote is public (and thus subject to delegation) or not. Required.
    * **reason**: a reason for the vote. Conditional, used only for public votes.
    * **question0**: string containing the chosen option for the question. Required.
 
   User must have ``emit_direct_vote`` permission to succeed. A single user can vote multiple times in one election, but only the last vote will remain valid.

   **cancel_vote**

   The authenticated user cancels its direct vote in the election. User must have ``emit_direct_vote`` permission and a valid current vote in the election to succeed.

   :param agora_id: agora's unique id
   :type agora_id: int
   :form action: name of the action. Required.
   :status 200 OK: no error
   :statuscode 403 FORBIDDEN: when the user has not the required permissions
   :status 404 NOT FOUND: when the agora is not found

   **Example request**:

   .. sourcecode:: http

    POST /api/v1/election/1/action/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript
    Authorization: ApiKey linus:204db7bcfafb2deb7506b89eb3b9b715b09905c8

    {
        "is_vote_secret": true,
        "question0": "Yay",
        "action": "vote"
    }

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "status": "success"
    }


Resource: CastVote
==================

Represents a vote in an election.

Retrieve a vote
---------------

.. http:get:: /castvote/(int:castvote_id)

   Retrieves a vote (`castvote_id`).

   :param castvote_id: vote's unique id
   :type castvote_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the vote is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/castvote/1/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "voter": {
            "username": "david", 
            "first_name": "", 
            "last_name": "", 
            "mugshot_url": "http://www.gravatar.com/avatar/08d5c7923d841a23030038591c9ae3e0?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2F.png", 
            "url": "/user/david", 
            "is_active": true, 
            "last_login": "2013-05-17T23:54:35.764850", 
            "full_name": "", 
            "short_description": "Is a member of 2 agoras and has emitted  0 direct votes.", 
            "id": 0, 
            "date_joined": "2012-11-29T16:08:43.874000"
        }, 
        "hash": "8bfe96079a5331f516652bbd9528c7fc84f4772b6e99cd45b37ef5a15cd189a2", 
        "public_data": {}, 
        "casted_at_date": "2013-05-17T23:54:30.944729", 
        "is_counted": true, 
        "is_direct": false, 
        "invalidated_at_date": null, 
        "reason": "", 
        "election": "/api/v1/election/1/", 
        "tiny_hash": null, 
        "is_public": false, 
        "id": 1, 
        "action_id": null
    }


Resource: DelegateElectionCount
===============================

This resource stores how many delegated votes an election delegate got in a given election. These resources are created when an election is tallied.

List
----

.. http:get:: /delegateelectioncount/

   List resource items

   :query offset: offset number. default is 0
   :query limit: limit number. default is 20
   :query delegate: filter by `delegate` id. It allows all django filter types.
   :query election: filter by `election` id. It allows all django filter types.
   :statuscode 200 OK: no error

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/delegateelectioncount/?election=1 HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "meta": 
        {
            "previous": null, 
            "total_count": 2, 
            "offset": 0, 
            "limit": 20, 
            "next": null
        }, 
        "objects": 
        [
            {
                "count": 1, 
                "created_at_date": "2013-05-17T23:42:57.514430", 
                "delegate": 
                {
                    "username": "user1", 
                    "first_name": "Juana Molero", 
                    "mugshot_url": "http://www.gravatar.com/avatar/cc721459f5b77680bc6a8ba6c9681c46?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FJM.png", 
                    "content_type": "user", 
                    "url": "/user/user1", 
                    "full_name": "Juana Molero", 
                    "id": 1
                }, 
                "election": "/api/v1/election/6/"
            }, 
            {
                "count": 2, 
                "created_at_date": "2013-05-17T23:42:57.514941", 
                "delegate": 
                {
                    "username": "user2", 
                    "first_name": "Jose Lopez", 
                    "mugshot_url": "http://www.gravatar.com/avatar/ecdecef1814126d1846e600cbef37a24?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FJL.png", 
                    "content_type": "user", 
                    "url": "/user/user2", 
                    "full_name": "Jose Lopez", 
                    "id": 2
                }, 
                "election": "/api/v1/election/6/"
            }
        ]
    }

Retrieve
--------

.. http:get:: /delegateelectioncount/(int:item_id)

   Retrieves an item in the resource (`item_id`).

   :param item_id: delegate election count resource id.
   :type item_id: int
   :status 200 OK: no error
   :status 404 NOT FOUND: when the resource is not found

   **Example request**:

   .. sourcecode:: http

    GET /api/v1/delegateelectioncount/1/ HTTP/1.1
    Host: example.com
    Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

    HTTP/1.1 200 OK
    Vary: Accept, Accept-Language, Cookie
    Content-Type: application/json; charset=utf-8

    {
        "count": 1, 
        "created_at_date": "2013-05-17T23:59:06.392604", 
        "delegate": 
        {
            "username": "user3", 
            "first_name": "David Perez", 
            "mugshot_url": "http://www.gravatar.com/avatar/783711dc4d57fe8542857211b9064d42?s=50&d=https%3A%2F%2Funitials.com%2Fmugshot%2F50%2FDP.png", 
            "content_type": "user", 
            "url": "/user/user3", 
            "full_name": "David Perez", 
            "id": 3
        }, 
        "election": "/api/v1/election/6/"
    }
