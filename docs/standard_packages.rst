.. index::
    single: Internals; Standard packages

Standard Packages
=================

Core
----

Not much of a package but SiteSupra itself. It contains core classes and definitions of SiteSupra framework.


Cms
---

SiteSupra CMS by itself. This package contains controllers, routing, entities and frontend assets that are used
throughout CMS backend.

CmsAuthentication
-----------------

SiteSupra authentication layer is separated in CmsAuthentication bundle. It sets up SecurityContext and handles backend
user authentication. It is not suitable for front-end user authentication.

DebugBar
--------

Integrates `PHP DebugBar <http://phpdebugbar.com/>`_ into SiteSupra allowing you to monitor requests, their
 time lines, SQL queries, events, and much more. This package is active only when SiteSupra runs in debug mode.

Framework
---------

This package combines and integrates everything together. It sets up Doctrine, EntityAudit, Twig, and all other
components required to run SiteSupra. It also registers most of the commands that you can access from :doc:`supra_cli`.