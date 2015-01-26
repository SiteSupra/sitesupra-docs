.. index::
    single: CLI
    single: Tools; CLI

Command Line Interface
======================

General Concepts
----------------

SiteSupra uses `Symfony console component <http://symfony.com/doc/current/components/console/introduction.html>`_ for console operations. `Boris <https://github.com/d11wtq/boris>`_ is used for REPL shell.

The main console executable is ``supra/cli.php``. Running it without parameters (like ``php supra/cli.php``) will give
you a list of all available commands.

By default CLI loads with ``cli`` environment and debugging enabled.

CLI Events
~~~~~~~~~~

You can use events in your console application; you can use ``Symfony\Component\Console\Event\ConsoleEvent`` or
``Supra\Core\Event\ConsoleEvent`` (providing methods ``getData/setData``) for such cases. Currently, there's only one
CLI-only event in SiteSupra,  ``Supra\Package\Framework\Event\FrameworkConsoleEvent::ASSETS_PUBLISH``, provided by
``SupraPackageFramework``, that is fired running ``supra/cli.php assets:publish``. This event can be used to copy some
non-standard assets to your webroot (to simplify things, you can implement ``Supra\Core\Event\ConsoleEventListenerInterface``
for your listener and use ``webRoot`` and ``webRootPublic`` keys of ``$assetsPublishEvent->getData()`` to determine copy
locations).

Core Commands
-------------

SiteSupra does not provide any built-in commands itself; on a plain installation you have ``help`` and ``list`` commands,
that are standard for Symfony Console. Only two argumens (``--env``, or ``-e``, for environment, defaulting to ``cli``,
and ``--debug``, defaulting to true), are present.

Package Commands
----------------

SupraPackageCms
~~~~~~~~~~~~~~~

This package does not provide any CLI commands.

SupraPackageCmsAuthentication
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This package provides the following commands:

* groups:add
* groups:list
* groups:remove
* groups:update
* users:add
* users:list
* users:remove
* users:update

these are self-explanatory and documented inline (try, for example, ``supra/cli.php help groups:update`` - you'll like
it!).

SupraPackageDebugBar
~~~~~~~~~~~~~~~~~~~~

This package does not provide any CLI commands.

SupraPackageFramework
~~~~~~~~~~~~~~~~~~~~~

assets:publish
++++++++++++++

Each package can contain assets (in ``Resources\public`` directory). *Publishing* assets means that this directory will
will be symlinked (sorry, no hard copy option yet) into ``web/public/PACKAGE_NAME``. Supra cleans up package name, so
assets from ``SupraPackageCms`` will we symlinked into ``web/public/cms``, enabling you to access them from the frontend.

cache:clear
+++++++++++

Clears SiteSupra cache. Cleans up all cache entries, if no argument is provided, or a particular segment. For more
information SiteSupra cache see :doc:`cache`.

cache:list
++++++++++

Shows info about SiteSupra cache and segments:

.. code-block:: text

    +----------------+-------+-------+
    | Directory      | Size  | Files |
    +----------------+-------+-------+
    | assets_404     | 0.13M | 14    |
    | cms_assets     | 1.80M | 2     |
    | combo          | 1.06M | 27    |
    | config         | 0.04M | 8     |
    | doctrine       | 0.04M | 5     |
    | frontend_cache | 0.02M | 1     |
    | twig           | 0.02M | 7     |
    +----------------+-------+-------+

container:dump
++++++++++++++

Dumps information about :doc:`container <di_container>` parameters and services:

.. code-block:: text

    Container parameters:
    +-----------------------------------------+-------------------------------------------------+
    | Parameter                               | Value                                           |
    +-----------------------------------------+-------------------------------------------------+
    | directories.supra_root                  | /home/developer/www/composer-test/supra         |
    | directories.project_root                | /home/developer/www/composer-test               |
    | directories.storage                     | /home/developer/www/composer-test/storage       |
    | directories.cache                       | /home/developer/www/composer-test/storage/cache |
    | directories.web                         | /home/developer/www/composer-test/web           |
    | directories.public                      | /home/developer/www/composer-test/web/public    |
    | environment                             | cli                                             |
    | debug                                   | TRUE                                            |
    | cms.media_library_known_file_extensions | array                                           |
    | results truncated...                    |                                                 |
    +-----------------------------------------+-------------------------------------------------+
    Container services:
    +----------------------------------------------------------------------+
    | ID                                                                   |
    +----------------------------------------------------------------------+
    | application                                                          |
    | config.universal_loader                                              |
    | routing.router                                                       |
    | kernel.kernel                                                        |
    | exception.controller                                                 |
    | http.request                                                         |
    | cache.driver                                                         |
    | cache.cache                                                          |
    | results truncated...                                                 |
    +----------------------------------------------------------------------+

container:packages:list
+++++++++++++++++++++++

Lists enabled Packages (showing both package name and class).

Doctrine-specific commands
++++++++++++++++++++++++++

The following commands are directly mapped the their `Doctrine counterparts <http://doctrine-orm.readthedocs.org/en/latest/reference/tools.html>`_:

* doctrine:cache-clear:metadata
* doctrine:cache-clear:query
* doctrine:cache-clear:result
* doctrine:convert-encodings
* doctrine:generate:proxies
* doctrine:schema:create
* doctrine:schema:drop
* doctrine:schema:update

Please refer to `Doctrine documentation <http://doctrine-orm.readthedocs.org/en/latest/reference/tools.html>`_ should you need help on that.

framework:routing:list
++++++++++++++++++++++

Displays all registered routes, patterns, resulting controller, and whether the route is exported to fronted:

.. code-block:: text

    Defined routes:
    +--------------------------------------+-------------------------------------------------------+------------------------------------+----------+
    | Name                                 | Pattern                                               | Controller                         | Frontend |
    +--------------------------------------+-------------------------------------------------------+------------------------------------+----------+
    | framework_combo                      | /_framework_internal/combo/{paths}                    | Framework:Combo:combo              | No       |
    | framework_routes                     | /_framework_internal/routes                           | Framework:Routing:export           | No       |
    | cms_dashboard                        | /backend                                              | Cms:Dashboard:index                | Yes      |
    | cms_dashboard_applications_list      | /backend/applications-list                            | Cms:Dashboard:applicationsList     | Yes      |
    | results truncated...                 |                                                       |                                    |          |
    +--------------------------------------+-------------------------------------------------------+------------------------------------+----------+


supra:bootstrap
+++++++++++++++

Creates default user (username admin, password admin) and loads some initial templates so you can access
backend and create new pages.

supra:shell
+++++++++++

Launches REPL shell, with pre-set ``$container`` and ``$application`` variables. You can play around with some SiteSupra
code without having debug controllers:

.. code-block:: text

    [1] supra> $container->getRouter()->generate('cms_dashboard');
    // '/backend'
    [2] supra>

supra:nested_set:check
++++++++++++++++++++++

.. warning::

    Warning! There is a risk of losing your data. Please don't forget to backup your database prior to running the command.

SiteSupra uses custom NestedSet implementation. It's quite stable and almost bulletproof, although may need in repair from time to time.


Writing your own Command
------------------------

See :doc:`../cookbook/command` for a complete reference.
