.. index::
    single: CLI
    single: Tools; CLI

Command line interface
======================

General concepts
----------------

Basically, SiteSupra uses `Symfony console component <http://symfony.com/doc/current/components/console/introduction.html>`_ for console operations. `Boris <https://github.com/d11wtq/boris>`_ is used for REPL shell.

The main console executable is ``supra/cli.php``. Running it with no parameters (like ``php supra/cli.php`` will give
you a list of all available commands.

CLI by default loads with ``cli`` environment and debugging enabled.

CLI events
~~~~~~~~~~

You can use events in your console application; you can use ``Symfony\Component\Console\Event\ConsoleEvent`` or
``Supra\Core\Event\ConsoleEvent`` (providing methods ``getData/setData``) for such cases. Currently, there's only one
CLI-only event in SiteSupra,  ``Supra\Package\Framework\Event\FrameworkConsoleEvent::ASSETS_PUBLISH``, provided by
``SupraPackageFramework``, that is fired running ``supra/cli.php assets:publish``. This event can be used to copy some
non-standard assets to your webroot (to simplify things, you can implement ``Supra\Core\Event\ConsoleEventListenerInterface``
for your listener and use ``webRoot`` and ``webRootPublic`` keys of ``$assetsPublishEvent->getData()`` to determine copy
locations).

Core commands
-------------

Package commands
----------------

Writing your own Command
------------------------
