.. index::
    single: Controllers

Controllers
===========

What are controllers?
---------------------

SiteSupra, following `Symfony <http://symfony.com>`_, is a request-response framework. It boots up in a request context,
applies business logic to the data provided, creates a response object and returns it to the client. Requests are, in
general, mapped through the :doc:`routing <routing>` engine to Controllers, which are PHP classes implementing the logic
behind SiteSupra.

Each :doc:`Package <standard_packages>` has it's own set of controllers and routing rules. There is no limit on number
of controllers and actions, you may group your logic in the way you like.

Controller, naming, and routes
------------------------------

Controllers are package - specific classes, extending ``Supra\Core\Controller\Controller``. They must reside in
``Controller`` namespace (like ``Supra\Package\Framework\Controller``), each controller method should accept ``Request``
and return a ``Response`` (more on these classes in Symfony `HttpFoundation documentation <https://github.com/symfony/HttpFoundation>`_).

SiteSupra expect ``Controller`` suffix on each Controller name (like ``FooBarController`` or ``UserController``), and
``Action`` suffix on each action method (like ``deleteAction`` or ``listAction``).

:doc:`Routes <routing>` use short syntax (``Package:Controller:action``); for example, ``Framework:Routing:export`` resolves
to ``SupraPackageFramework`` (namespace ``Supra\Package\Framework\Controller``), class ``RoutingController`` and method
``exportAction`` (thus, ``Supra\Package\Framework\Controller\RoutingController::exportAction``).

Each action is expected to return a ``Symfony\Component\HttpFoundation\Response`` object. Returning scalar value or not
returning any value at all (which is equivalent to ``return NULL``) will cause ``HttpKernel`` to throw an exception.

.. code-block:: php
    :linenos:

    <?php

    public function doStuffAction()
    {
        //doing stuff here

        return new Response('<html><head><title>Hello!</title</head><body>Hello!</body></html>');
    }

Of course, all types of Symfony responses are supported (like ``JsonResponse``, or ``RedirectResponse``), so, for example,
redirects look like so:

.. code-block:: php
    :linenos:

    <?php

    public function doStuffWithRedirectAction()
    {
        //doing stuff here

        return new RedirectResponse('http://example.com/');
    }

Base Controller class
---------------------

SiteSupra provides base class for your controllers, ``Supra\Core\Controller\Controller``. First of all, it is
:doc:`ContainerAware <di_container>`, so you can always access DI Container via ``$this->container`` (if you are
curios - container instance is set by :doc:`Http kernel <http_kernel>` when controller is instantiated). It's also provided
with ``$package`` and ``$application`` properties, which are set to current package class name (like
``Supra\Package\Framework\SupraPackageFramework``) and frontend application name (like ``cms_authentication``) accordingly.

.. note: there can be bug when $package is not set, needs some study. Tch.

Other handy methods presents in ``Controller`` class are listed below:

* ``renderResponse`` renders :doc:`twig <templating>` template and returns a ``Response`` object
* ``render`` renders :doc:`twig <templating>` template and returns result as string
* ``setApplication`` overrides current application for ApplicationManager (see :doc:`concepts` for more details)
* ``getUser`` returns current user or returns null if there's no security context, or if the security context does not contain valid token, or if the token does not contain valid user. Fore more information, see :doc:`security`.
* ``getPackage`` returns current package name (without namespace prefix, like ``Framework``)
* ``checkActionPermission`` is a security-oriented stub that is not yet ported from old SiteSupra code to Symfony ACL

Exceptions
----------

Controllers do not provide any custom exception handling; instead, any exception is caught by :doc:`Http kernel <http_kernel>`,
and, depending of current :doc:`debug settings <development_and_production>`, either trace is written or a special controller
is being called (invoking ``exception500Action``). A special case is a ``Symfony\Component\Routing\Exception\ResourceNotFoundException``
that, in production mode, is forwarded to ``exception404Action`` of exception controller thus allowing you to show pretty
404 page.

