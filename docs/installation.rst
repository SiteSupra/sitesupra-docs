.. index::
   single: Installation

Installing and configuring SiteSupra
====================================

As SiteSupra is based on Symfony components, and manages dependencies with composer, the installation process is pretty
  straightforward.

Software requirements
---------------------

.. index::
    single: Installation; Requirements

SiteSupra requires PHP 5.4 or higher. SiteSupra can run under Apache and ngnix.


.. note::

    SiteSupra is not tested on Windows platform yet.

Cloning SiteSupra
-----------------

SiteSupra source code is hosted at `github <https://github.com/SiteSupra/sitesupra>`_.

.. code-block:: bash

    $ git clone https://github.com/SiteSupra/sitesupra.git sitesupra


Configuring SiteSupra
~~~~~~~~~~~~~~~~~~~~~

When you've finished cloning SiteSupra, you have to do the following:

1.  run ``composer update`` to update dependencies
2.  set up web server permissions for ``storage`` folder (you can stick to plain old chmod 777 or use ACL approach as does `Symfony <http://symfony.com/doc/current/book/installation.html#checking-symfony-application-configuration-and-setup>`_
3.  copy ``supra/config.yml.example`` to ``supra/config.yml`` and set your database credentials
4.  create database by running ``supra/cli.php doctrine:schema:update --force``
5.  load initial fixtures by running ``supra/cli.php supra:bootstrap``
6.  publish assets with ``supra/cli.php assets:publish``

All done! Now just point your web server of choice to ``web`` directory in SiteSupra project root.

Configuring Apache
~~~~~~~~~~~~~~~~~~

.. index:: Installation; Apache

Apache's ``DocumentRoot`` directive should point into ``web`` directory in SiteSupra project root. Symlinking should be
enabled (add ``Options +FollowSymlinks`` for your virtual host), ``mod_rewrite`` is also required.

Rewrite rules are pretty simple (don't worry, they are already provided in ``.htaccess`` file):

.. code-block:: bash

    RewriteEngine On

    RewriteCond %{DOCUMENT_ROOT}%{REQUEST_FILENAME} -f
    
    RewriteRule ^ - [L,NS]
    RewriteRule ^.*$ /index.php$0 [L,NS]


Configuring nginx
~~~~~~~~~~~~~~~~~

.. index:: Installation; nginx

Wow. We haven't yet written installation documentation for nginx, this is a bit embarrassing. Maybe you can help us by
`contributing <https://github.com/SiteSupra>`_ to this documentation on github?


