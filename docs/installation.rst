.. index::
   single: Installation

Installing and Configuring SiteSupra Demo Site
==============================================

SiteSupra is based on Symfony components and manages dependencies with composer. The installation process is pretty
straightforward. However, you may consider checking :doc:`requirements` first.


Cloning SiteSupra Demo Site
---------------------------

SiteSupra source code is hosted at `github <https://github.com/SiteSupra/sitesupra>`_.
Clone or checkout SiteSupra into your work folder.

.. code-block:: bash

    $ git clone https://github.com/sitesupra/sitesupra-demo.git sitesupra

Configuring SiteSupra
---------------------

To configure SiteSupra on your computer follow the next steps :

1.  Run ``composer update`` to update dependencies;
2.  Create an empty database;
3.  Copy ``supra/config.yml.example`` to ``supra/config.yml`` and configure database connection;
4.  Create tables by running ``php supra/cli.php doctrine:schema:create``
5.  Setup  assets ``php supra/cli.php assets:publish``;
6.  Load fixtures ``php supra/cli.php sample:fixtures:load storage/fixtures``;


Installing and Configuring SiteSupra Core
=========================================

If you would like to contribute to SiteSupra project consider to checkout SiteSupra core. 
Please note SiteSupra core doesn't contain any web site or blocks. 
You may need to add and configure them by yourself.

Cloning SiteSupra Core
----------------------

SiteSupra source code is hosted at `github <https://github.com/SiteSupra/sitesupra>`_.
Clone or checkout SiteSupra into your work folder.

.. code-block:: bash

    $ git clone https://github.com/sitesupra/sitesupra.git sitesupra


Configuring SiteSupra
---------------------

To configure SiteSupra on your computer follow the next steps :

1.  Run ``composer update`` to update dependencies;
2.  Set up web server permissions for ``storage`` folder (you can stick to plain old chmod 777 or use ACL approach as `Symfony <http://symfony.com/doc/current/book/installation.html#checking-symfony-application-configuration-and-setup>`_ does);
3.  Create an empty database;
4.  Copy ``supra/config.yml.example`` to ``supra/config.yml`` and provide database credentials;
5.  Create tables by running ``php supra/cli.php doctrine:schema:create``;
6.  Load initial fixtures by running ``supra/cli.php supra:bootstrap``;
7.  Publish assets with ``supra/cli.php assets:publish``.

All done! Now just point your web server of choice to ``web`` directory in SiteSupra project's root.

Configuring Web Server
======================

Apache 
------

.. index:: Installation; Apache


Point ``DocumentRoot`` to the ``web`` directory in SiteSupra project's root.
Allow to follow symlinks and configure rewrite rules as listed below.

.. code-block:: bash

    Options +FollowSymlinks

    RewriteEngine On

    RewriteCond %{DOCUMENT_ROOT}%{REQUEST_FILENAME} -f
    
    RewriteRule ^ - [L,NS]
    RewriteRule ^.*$ /index.php$0 [L,NS]


Rewrite rules for ``.htaccess`` are provided in ``.htaccess`` file that comes along with SiteSupra source code.

nginx
-----

.. index:: Installation; nginx

Point ``root`` to the ``web`` directory in SiteSupra project's root.
Configure rewrite rules as shown below:

.. code-block:: bash

    location / {
        try_files $uri $uri/ /index.php;
    }


