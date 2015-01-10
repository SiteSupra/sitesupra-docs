.. index::
    single: Routing

Routing
=======

SiteSupra routing is heavily based on Symfony routing component and uses very similar syntax. However, there are some
minor differences. First of all, you have to load all your routing files manually in your package's ``inject()`` method:

.. code-block:: php
    :linenos:

