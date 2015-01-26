.. index::
   single: Installation

Installing and Configuring SiteSupra
====================================

SiteSupra is based on Symfony components and manages dependencies with composer. The installation process is pretty
straightforward. However, you may consider checking :doc:`requirements` first.


Cloning SiteSupra
-----------------

SiteSupra source code is hosted at `github <https://github.com/SiteSupra/sitesupra>`_.
Clone or checkout SiteSupra into your work folder.

.. code-block:: bash

    $ git clone https://github.com/SiteSupra/sitesupra.git sitesupra


Configuring SiteSupra
---------------------

To configure SiteSupra on your computer follow the next steps :

1.  Run ``composer update`` to update dependencies;
2.  Set up web server permissions for ``storage`` folder (you can stick to plain old chmod 777 or use ACL approach as `Symfony <http://symfony.com/doc/current/book/installation.html#checking-symfony-application-configuration-and-setup>`_ does);
3.  Copy ``supra/config.yml.example`` to ``supra/config.yml`` and provide database credentials;
4.  Create database by running ``supra/cli.php doctrine:schema:update --force``;
5.  Load initial fixtures by running ``supra/cli.php supra:bootstrap``;
6.  Publish assets with ``supra/cli.php assets:publish``.

All done! Now just point your web server of choice to ``web`` directory in SiteSupra project's root.

Configuring Apache
------------------

.. index:: Installation; Apache

Apache's ``DocumentRoot`` directive should point into ``web`` directory in SiteSupra project's root.
Allow to follow symlinks and configure rewrite rules as listed below.

.. code-block:: bash

    Options +FollowSymlinks

    RewriteEngine On

    RewriteCond %{DOCUMENT_ROOT}%{REQUEST_FILENAME} -f
    
    RewriteRule ^ - [L,NS]
    RewriteRule ^.*$ /index.php$0 [L,NS]


Rewrite rules for ``.htaccess`` are provided in ``.htaccess`` file that comes along with SiteSupra source code.

Configuring nginx
-----------------

.. index:: Installation; nginx

Wow. We haven't yet written installation documentation for nginx, this is a bit embarrassing. Maybe you can help us by
`contributing <https://github.com/SiteSupra>`_ to this documentation on github?


