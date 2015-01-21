.. index::
    single: Security

.. include:: <isonum.txt>

Security
========

SiteSupra does not provide any kind of authentication for user part of CMS; it only provides authentication and user
management layer for CMS part, decoupled in separate ``SupraPackageCmsAuthentication`` (more on standard packages in
:doc:`corresponding section <standard_packages>`). So, the documentation below applies only to CMS part, but you can
always add authentication to your website following this :doc:`cookbook article </cookbook/authentication>`.

SiteSupra security layer is heavily based on `Symfony security component <https://github.com/symfony/Security>`_, so if
things like ``SecurityContext``, ``Token`` or ``UserProvider`` sound new to you - please read Symfony docs first.

Security concepts and configuration
-----------------------------------

Security is, currently, blindly bound to ``cms.prefix`` container parameter and secures all urls beginning with it. Url
mapping happens in ``CmsAuthenticationRequestListener``, and, if no authorized user was found, user is being redirected
to login page.

.. note::

    We are likely to extends security layer to both backend and frontend - stay tuned!

The second listener, ``CmsAuthenticationResponseListener`` ensures that current ``Token`` is stored in the session
(under key defined by ``cms_authentication.session.storage_key`` parameter).

SiteSupra also dispatches ``AuthController::TOKEN_CHANGE_EVENT`` every time a new token is stored in the session.
Voters and ACL's are enabled, but not used yet.

You can view default security configuration in ``Supra\Package\CmsAuthentication\Resources\config\config.yml``. Apart
of paths and services, if defines a shared user source (explained below), sets up user providers (bound to
``CmsAuthentication:User`` entity), both combined into provider chain, and sets ``SupraBlowfishEncoder`` as a default
password encoder.

CLI commands
------------

SiteSupra also provides some basic use management commands (for adding and removing backend user groups), allowing you
to manage users event if the database is empty. Please see :doc:`supra_cli` for more details.

User source and user provider
-----------------------------

By default SiteSupra uses ``Supra\Package\CmsAuthentication\Entity\User`` as base user entity and corresponding repository
(which already implements ``Symfony\Component\Security\Core\User\UserProviderInterface``) as a user source. Again, by
default is it bound to current connection (please read :doc:`database_and_audit` if you want to know more about SiteSupra
database layer).

Shared user provider
--------------------

Sometimes, for development purposes, it is nice to have a shared database with some standard users or share users between
SiteSupra installations in production. This is possible by defining a new database connection in main configuration file
(``supra\config.yml``), under cms_authentication |rarr| users |rarr| shared_connection, as show in example below:

.. code-block:: yaml
    :linenos:

    cms:
        active_theme: default
    framework:
        doctrine:
            credentials:
                hostname: localhost
                username: root
                password: ~
                charset: utf8
                database: supra9
    cms_authentication:
        users:
            shared_connection:
                host: localhost
                user: root
                password: ~
                charset: utf8
                dbname: supra9_shared_users
                driver: mysql
                event_manager: public
            user_providers:
                doctrine:
                    supra.authentication.user_provider.public:
                        em: public
                        entity: CmsAuthentication:User
            provider_chain: [ doctrine.entity_managers.public ]
