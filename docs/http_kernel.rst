.. index::
    single: HTTP kernel
    single: Internals; HTTP kernel

HTTP kernel and bootstrap process
=================================

.. note::

    SiteSupra does not use `HttpKernel <https://github.com/symfony/HttpKernel>`_ (we had eight versions of SiteSupra before moving to Symfony, and some of Symfony concepts didn't suit our needs). Our implementation is still a bit incomplete, especially ``RequestStack`` and forwarding, so expect rafactoring soon.

SiteSupra uses plain ``HttpFoundation`` component (see `documentation of HttpFoundation <https://github.com/symfony/HttpFoundation>`_
and `HTTP requests in symfony <http://symfony.com/doc/current/book/http_fundamentals.html#requests-and-responses-in-symfony>`_
for deeper introduction).

SiteSupra mimics Symfony behaviour as close as possible - a ``Request`` object is created, every controller returns
``Response`` (more on the controllers :doc:`here <controllers>`). Basically, request processing happens in the following
order:

* web server hist entry point, ``webroot/index.php``
* SiteSupra builds :doc:`container <di_container>`, ``buildContainer()`` is called
* SiteSupra boots, ``boot()`` is called; two events are fired, ``Supra::EVENT_BOOT_START`` and ``Supra::EVENT_BOOT_END``, and ``boot()`` method is called for every registered Package, allowing early initialization
* request handling starts by calling ``handleRequest($request)``. This method loads ``Supra\Core\Kernel\HttpKernel`` and calls ``handle()``. Request handling by HttpKernel traverses through stages below:

  1. ``KernelEvent::REQUEST`` is thrown. If some of event listeners sets response for ``RequestResponseEvent``, request processing stops and the request is returned
  2. kernel tries to resolve controller and action by checking ``_controller`` and ``_action`` parameters of request ``AttributeBag``; if found, controller is instantiated, action is called and ``HttpKernel`` expects ``Controller`` to return a ``Response``. This is used when forwarding requests or instantiating requests without route.
  3. if controller is not resolved yet kernel loads routing and tries to find current route. ``AttributeBag`` is overwritten by one created from route configuration and controller is actually executed. ``KernelEvent::CONTROLLER_START`` and ``KernelEvent::CONTROLLER_END`` events are fired, and any listener can override response during  ``KernelEvent::CONTROLLER_END`` event. If any exception (generic or ``ResourceNotFoundException``) is thrown request processing moves to exceptions processing (see stage 5 below).
  4. kernel fires final ``KernelEvent::RESPONSE`` event and returns resulting ``Response`` object. If the object is not an instance of ``Response``, an exception is thrown.
  5. if any exception is caught during request processing, then the exception is processed in the following way:

    1. kernel fires ``KernelEvent::EXCEPTION`` event (again, if any listener provides ``Response`` inside this exception event, then the response is returned)
    2. if exception is instance of ``ResourceNotFoundException``, a special event is fired - ``KernelEvent::ERROR404``, which allows, for example, on-the-fly compilation of assets. Finally, if no response is available after the event is processed, the exception is re-thrown (in debug mode) or ``exception404Action`` of default exception controller (stored in container under ``exception.controller`` key) is called
    3. finally, if there's still and exception and no response, an exception is re-thrown (in debug mode) or ``exception500Action`` of default exception controller (stored in container under ``exception.controller`` key) is called

* resulting response is sent to the browser (by calling ``send()`` method)), if any unhandled exception occurs, it is caught by `Debug <http://symfony.com/doc/current/components/debug/introduction.html>`_ component
* SiteSupra shuts down, firing ``Supra::EVENT_SHUTDOWN_START`` and  ``Supra::EVENT_SHUTDOWN_END`` events, and calls ``shutdown()`` method of all registered packages, thus allowing some late cleanup

As you can see, this process is pretty simple is transparent. Last thing to note must be a ``SupraJsonResponse`` class
that is used throughout CMS backend and is used to pass messages, warnings and errors to frontend in a common way - see
the source if you're curious about how it is done.