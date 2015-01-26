.. index::
    single: Controllers

Controllers
===========

What are Controllers?
---------------------

SiteSupra, following `Symfony <http://symfony.com>`_, is a request-response framework. It boots up in a request context,
applies business logic to the data provided, creates a response object, and returns it to the client. Requests are, in
general, mapped through the :doc:`routing <routing>` engine to Controllers, which are PHP classes implementing the logic
behind SiteSupra.

Each :doc:`Package <standard_packages>` has it's own set of controllers and routing rules. There is no limit on number
of controllers and actions your web application may have. You may build and group your logic in the way you like.

Controller, Naming, and Routes
------------------------------

Controllers are package - specific classes, extending ``Supra\Core\Controller\Controller``. They must reside in
``Controller`` namespace (like ``Supra\Package\Framework\Controller``). Each controller method should accept ``Request``
and return a ``Response`` (more on these classes in Symfony `HttpFoundation documentation <https://github.com/symfony/HttpFoundation>`_).

SiteSupra expect ``Controller`` suffix in each Controller name (like ``FooBarController`` or ``UserController``) and
``Action`` suffix in each action method (like ``deleteAction`` or ``listAction``).

:doc:`Routes <routing>` use short syntax (``Package:Controller:action``). For example, ``Framework:Routing:export`` resolves
to ``SupraPackageFramework`` (namespace ``Supra\Package\Framework\Controller``), class ``RoutingController``, and method
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

Of course, all types of Symfony responses are supported (like ``JsonResponse``, or ``RedirectResponse``). For example,
 a redirect to another URL could be called in the following way:

.. code-block:: php
    :linenos:

    <?php

    public function doStuffWithRedirectAction()
    {
        //doing stuff here

        return new RedirectResponse('http://example.com/');
    }

Base Controller Class
---------------------

SiteSupra provides base class for your controllers, which is ``Supra\Core\Controller\Controller``. First of all, it is
:doc:`ContainerAware <di_container>`, so you can always access DI Container via ``$this->container`` (container instance is set by :doc:`Http kernel <http_kernel>` when controller is instantiated).
It's provided with ``$package`` and ``$application`` properties, which are set to current package class name (like
``Supra\Package\Framework\SupraPackageFramework``), and frontend application name (like ``cms_authentication``).

.. note: there can be bug when $package is not set, needs some study. Tch.

Other handy methods presents in ``Controller`` class are listed below:

* ``renderResponse`` renders :doc:`twig <templating>` template and returns a ``Response`` object;
* ``render`` renders :doc:`twig <templating>` template and returns result as a string;
* ``setApplication`` overrides current application for ApplicationManager (see :doc:`concepts` for more details);
* ``getUser`` returns current user or returns null if there's no security context, or if the security context does not contain valid token, or if the token does not contain valid user. Fore more information, see :doc:`security`;
* ``getPackage`` returns current package name (without namespace prefix, like ``Framework``);
* ``checkActionPermission`` is a security-oriented stub that is not yet ported from legacy SiteSupra code to Symfony ACL.

Exceptions
----------

Controllers do not provide any custom exception handling. Instead, any exception is caught by :doc:`Http kernel <http_kernel>`.
Depending on current :doc:`debug settings <development_and_production>` either trace is written or a special controller
is being called (invoking ``exception500Action``).
A special case is ``Symfony\Component\Routing\Exception\ResourceNotFoundException``, which is forwarded to ``exception404Action`` of exception controller thus allowing you to show pretty 404 page in production mode.

