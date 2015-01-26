.. index::
   single: Software Requirements

Software Requirements
====================================

.. index::
    single: Installation; Requirements

Web Server
----------
SiteSupra can run under any web server that supports PHP. We prefer Apache or nginx.
Although, SiteSupra is not fully tested on Windows platform yet.
However, from technical perspective we see no reasons why it may not run well on a WAMP-powered server.


Additional Modules
~~~~~~~~~~~~~~~~~~

As many other CMS and frameworks SiteSupra rewrites URLs to hide reference to index.php file and make URLs human readable.
To enable URL rewriting Apache requires ``mod_rewrite`` and nginx requires ``ngx_http_rewrite_module``.

PHP
~~~
PHP version 5.4 or higher is required.

Database
--------
Communications with the database layer is handled by Doctrine ORM.
However, only MySQL database server is supported at the moment. Install MySQL version 5.1 or later.

..
  Cache
  -----
  SiteSupra caches most requested information to speed up access and rendering of web application content.
  We recommend to use latest Memcahced server for caching purposes.