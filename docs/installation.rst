.. index::
   single: Installation

Installing and configuring SiteSupra
====================================

A few words on the process...

Software requirements
---------------------

.. index::
    single: Installation; Requirements

SiteSupra requires PHP 5.4 or higher. SiteSupra can run under Apache and ngnix.


.. note::

    SiteSupra is not tested on Windows platform yet.

Cloning SiteSupra
-----------------

SiteSupra source code is hosted at github.

.. code-block:: bash

    $ git clone https://github.com/SiteSupra/sitesupra.git sitesupra


Configuring SiteSupra
~~~~~~~~~~~~~~~~~~~~~

Open your command console and execute the following three commands:

.. tip::

    Run ....


Configuring Apache
~~~~~~~~~~~~~~~~~~

.. index:: Installation; Apache

Rewrite rules for Apache vhost configuration file:

.. code-block:: bash

    RewriteEngine on

    RewriteCond %{DOCUMENT_ROOT}%{REQUEST_FILENAME}.less -f
    RewriteRule ^(.*\.css)$ /cms/lib/supra/combo/combo.php?$1 [L,NS]

    RewriteCond %{DOCUMENT_ROOT}%{REQUEST_FILENAME} -f
    RewriteRule ^ - [L,NS]

    RewriteRule ^(.*)$ /index.php$1 [L,NS]



Configuring nginx
~~~~~~~~~~~~~~~~~

.. index:: Installation; nginx

.. code-block:: bash


