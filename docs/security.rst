.. index::
    single: Security

.. include:: <isonum.txt>

Security
========

.. TODO not clear

SiteSupra does not provide any kind of authentication for user part of CMS; it only provides authentication and user
management layer for CMS part, decoupled in separate ``SupraPackageCmsAuthentication`` (more on standard packages in
:doc:`corresponding section <standard_packages>`). So, the documentation below applies only to CMS part, but you can
always add authentication to your website following this :doc:`cookbook article </cookbook/authentication>`.

SiteSupra security layer is based on `Symfony security component <https://github.com/symfony/Security>`_.

Security Concepts and Configuration
-----------------------------------

Security is blindly bound to ``cms.prefix`` container parameter and secures all URLs beginning that.
URL mapping happens in ``CmsAuthenticationRequestListener``.
When visitor is not authorized yet, then the visitor is being redirected to CMS login page.

.. note::

    We are likely to extend security layer to both backend and frontend - stay tuned!

The second listener, ``CmsAuthenticationResponseListener``, ensures that current ``Token`` is stored in user session
under the key defined by ``cms_authentication.session.storage_key`` parameter.

SiteSupra dispatches ``AuthController::TOKEN_CHANGE_EVENT`` every time a new token is stored in the session.
Voters and ACL's are enabled, but not used yet.

Default security configuration is stored in ``Supra\Package\CmsAuthentication\Resources\config\config.yml``.
Apart from paths and services, it defines a shared user source (explained below), sets up user providers (bound to
``CmsAuthentication:User`` entity), both combined into provider chain, and sets ``SupraBlowfishEncoder`` as a default
password encoder.

CLI Commands
------------

.. TODO not clear what supra provides

SiteSupra provides some basic user management commands (for adding and removing backend user groups) allowing you
to manage users event if the database is empty. refer to :doc:`supra_cli` for more details.

User Source and User Provider
-----------------------------

By default SiteSupra uses ``Supra\Package\CmsAuthentication\Entity\User`` as base user entity and corresponding repository
(which already implements ``Symfony\Component\Security\Core\User\UserProviderInterface``) as a user source. Again, by
default it is bound to current connection (please refer to :doc:`database_and_audit` if you want to learn more on SiteSupra
database layer).

Shared User Provider
--------------------

While developing web project it is good to have a shared user database with some default user accounts in there or share users between
SiteSupra installations in production. This is possible by defining a new database connection in main configuration file
(``supra\config.yml``) under cms_authentication |rarr| users |rarr| shared_connection as shown in example below:

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
