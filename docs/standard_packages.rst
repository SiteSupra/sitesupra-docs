.. index::
    single: Internals; Standard packages

Standard packages
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
user authentication. If you need to authenticate frontend users, you have to implement this yourself.

DebugBar
--------

Integrates `PHP DebugBar <http://phpdebugbar.com/>`_ into SiteSupra, allowing you to monitor all requests, request
timelines, SQL queries, events and many more. This package is active only if SiteSupra is in debug mode.

Framework
---------

This package combines and integrates everything together. It sets up Doctrine, EntityAudit, Twig, and all other
components. It also registers most of the commands that you can access from :doc:`supra_cli`