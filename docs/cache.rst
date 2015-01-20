.. index::
    single: Cache
    single: Internals; Cache

Cache
=====

SiteSupra does not provide any public caching interfaces. However, it has an internal cache system that you can use.

Cache class
-----------

Cache class (``Supra\Core\Cache\Cache``, accessible by ``cache.cache`` service key or ``$container->getCache()`` method),
exposes the following methods:

* ``fetch($prefix, $key, $default, $timestamp, $ttl, $respectDebug)`` fetches and probably stores value in the cache (if current value was not found). The parameters are explained below:

  * ``$prefix`` and ``$key`` parameters are quite self-explanatory
  * ``$default`` value can be a scalar or a callable (checked by `is_callable <http://php.net/is_callable>`_), which is being called only on cache miss
  * ``$timestamp`` is a last modification timestamp, allowing you to refresh the cache - very handy for storing and combining assets
  * ``$ttl`` is a time to live
  * ``$respectDebug`` - if you set this to ``true``, cache is always refreshed in :doc:`development environment <development_and_production>`

* ``store($prefix, $key, $value, $timestamp, $ttl)`` - directly proxies data to storage driver. If ``$value`` is callable, it is invoked
* ``delete($prefix, $key)`` deletes value from driver
* ``clear($prefix)`` deletes all values on particular prefix

We've tried to make the cache as simple as possible, so the common usage pattern with callback looks like the following:

.. code-block:: php
    :linenos:

    <?php

    $result = $container->getCache()->fetch('internal', 'value1', function() use ($container) {
        //this code will be called only on cache miss
        $value = $container['some.service']->doSomeHeavyOperation();

        return $value;
    }, filemtime('file.txt'), 3600);

Doctrine Wrapper
----------------

Some libraries can use `Doctrine Cache <http://doctrine-orm.readthedocs.org/en/latest/reference/caching.html>`_ as a cache
layer. SiteSupra provides wrapper over it's native cache, ``DoctrineCacheWrapper``, which you can use in the following way:

.. code-block:: php
    :linenos:

    <?php

    use Supra\Core\Cache\DoctrineCacheWrapper;

    $wrapper = new DoctrineCacheWrapper();
    $wrapper->setContainer($container);

    $wrapper->setPrefix('supra_native_prefix');
    $wrapper->setSuffix('doctrine_cache_suffix');

    //and now you can use $wrapper anywhere where instance of CacheProvider is required. Supra will respect ``prefix``
    //that you have set, and Doctrine will use ``suffix``

Cache drivers and current implementation
----------------------------------------

Currently there is only one cache driver implemented in Supra - ``File``, which writes into ``storage/cache`` folder,
creating separate subfolders for each ``prefix`` you define. Also, there are some cache-specific :doc:`CLI commands <supra_cli>`,
you should check them also. More cache drivers to come soon, but now you can write your own just by implementing
``Supra\Core\Cache\Driver\DriverInterface`` interface.
