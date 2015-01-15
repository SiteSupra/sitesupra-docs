.. index::
    single: Templating

Templating
==========

SiteSupra uses `Twig <http://twig.sensiolabs.org/>`_ for templating. Nothing unusual, just some things are worth mentioning:

* there's one Twig extension, ``CmsExtension``, that provides functions used to prepare CMS JS/CSS assets, and another one, called ``PageExtension``, that manages CMS-specific functions and tags (more on that later)
* views reside in package's ``Resource\view`` folders, just like with Symfony
* you can reference template inside a package, you can use shorthand syntax like ``{% extends 'SamplePackage:layouts/base.html.twig' %}``

If you need to reference and render template, you can always access Twig environment by calling ``$container->getTemplating()``,
or call ``renderResponse`` from your controller (package defaults to current package here):

.. code-block:: php
    :linenos:

    <?php

    public function indexAction()
    {
        return $this->renderResponse('index.html.twig');
    }

If you need to register a custom extension, you can do it during package injection;

.. code-block:: php
    :linenos:

    <?php

    public function inject(ContainerInterface $container)
    {
        $container->getTemplating()->addExtension(new PageExtension());
    }

PageExtension
-------------
