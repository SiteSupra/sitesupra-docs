.. index::
    single: Routing

.. include:: <isonum.txt>

Routing
=======

Loading routes
--------------

SiteSupra routing is heavily based on Symfony routing component and uses very similar syntax. However, there are some
minor differences. First of all, you have to load all your routing files manually in your package's ``inject()`` method:

.. code-block:: php
    :linenos:

    <?php

    $container->getRouter()->loadConfiguration(
        $container->getApplication()->locateConfigFile($this, 'routes.yml')
    );

``locateConfigFile`` searches ``routes.yml`` in your package's ``Resources\config`` directory.

Common example
--------------

Let's see some examples of routing definitions. The most standard simple would be ``SupraPackageFramework`` main routing
file;

.. code-block:: yaml
    :linenos:

    configuration:
        prefix:             ~
    routes:
        framework_combo:
            pattern:        /_framework_internal/combo/{paths}
            controller:     Framework:Combo:combo
            requirements:
                paths:      .+
            defaults:
                paths:      ~
        framework_routes:
            pattern:        /_framework_internal/routes
            controller:     Framework:Routing:export

``configuration`` section, seen on ``line 1``, defines global ``prefix`` and ``defaults`` (default parameter values)
keys. ``prefix`` must be set, even with default ``~`` value.

``routes`` section, starting from ``line 3``, defines actual routes. Each route may contain the following fields:

* ``pattern``, defining actual URI that will trigger the route
* ``controller``, specifying the controller (in the example above, ``Framework:Combo:combo`` resolves into ``SupraPackageFramework`` |rarr| ``ComboController`` |rarr| ``comboAction`` (just like Symfony does!)
* ``filters``, defining Symfony route filters
* ``requirements``, where you can specify per-parameter regex requirements
* ``defaults``, providing default parameter values
* ``options``, currently supporting only ``frontend`` key

where only ``pattern`` and ``controller`` are required.

.. tip::
    As SiteSupra's routing is based on `Symfony Routing component <http://symfony.com/doc/current/components/routing/introduction.html>`_, everything written in Symfony documentation applies also to SiteSupra - we did not reinvent the wheel here.

Container parameters and javascript
-----------------------------------

Let's see a bit more complicated example from ``SupraPackageCms``:

.. code-block:: yaml
    :linenos:

    configuration:
        prefix: ~
    routes:
        cms_dashboard:
            pattern:            %cms.prefix%
            controller:         Cms:Dashboard:index
            options:
                frontend:       true

First of all, you can use container parameters (in ``%container.parameter.name%`` form) in your route ``pattern``;
secondly, you can provide ``frontend: true`` option and use in Javascript like this:

.. code-block:: javascript
    :linenos:
    
    Supra.Url.generate('cms_dashboard');