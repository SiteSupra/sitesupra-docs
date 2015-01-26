.. index::
    single: Templating

Templating
==========

SiteSupra uses `Twig <http://twig.sensiolabs.org/>`_ template engine. Nothing unusual, just few things worth to mention:

* There's one Twig extension, called ``CmsExtension``, that provides functions for preparing and rendering CMS JS/CSS assets, and another one, called ``PageExtension``, that manages CMS-specific functions and tags (more on that later);
* Views reside in package's ``Resource\view`` folders, just like in Symfony;
* You can reference template inside a package and use shorthand syntax like ``{% extends 'SamplePackage:layouts/base.html.twig' %}``.

To reference and render a template you can always access Twig environment by calling ``$container->getTemplating()``
or call ``renderResponse`` from your controller (package defaults to current package here):

.. code-block:: php
    :linenos:

    <?php

    public function indexAction()
    {
        return $this->renderResponse('index.html.twig');
    }

You can register a custom extension during package injection;

.. code-block:: php
    :linenos:

    <?php

    public function inject(ContainerInterface $container)
    {
        $container->getTemplating()->addExtension(new PageExtension());
    }

PageExtension
-------------

To explain ``PageExtension``, we need to discuss two things: ``PageExecutionContext`` and ``BlockExecutionContext`` (make
sure you've read :doc:`concepts` first).

Both of these objects are simple container classes for objects defining current block or page being executed. Both of them
contain a ``Request`` object (plain ``Request`` for Blocks and ``PageRequest`` for Pages) and a ``Controller`` object
(``BlockController`` and ``PageController`` accordingly).

.. note::

    As other SiteSupra internal features, this is likely to change in the future.

This extension defines one filter, called ``decorate`` that works with internal ``HtmlTag`` instances, and a few functions
listed below:

* ``collection()``, and ``list()`` resolve property to a collection, example would be writing ``{% for item in collection(property('image', 'image')) %}``;
* ``set()`` resolves property to a set;
* ``property()`` fetches single property from a Block or Page;
* ``isPropertyEmpty()`` checks if property value is empty;
* ``placeHolder()`` defines a placeholder (see :doc:`blocks_and_editables` and :doc:`concepts` for more information).

Every function in ``PageExtension`` is based on a custom ``node_class``.
This facilitates the process of dynamic creation of block properties when template is parsed.
``BlockPropertyNodeVisitor`` creates block properties on-the-fly.
