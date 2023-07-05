=======================
Upgrade to Nextcloud 28
=======================

General
-------

info.xml
^^^^^^^^

Make sure your ``appinfo/info.xml`` allows for Nextcloud 28.

.. code-block:: xml

    <dependencies>
        <nextcloud min-version="25" max-version="28" />
    </dependencies>

Front-end changes
-----------------

Added APIs
^^^^^^^^^^

* tbd

Changed APIs
^^^^^^^^^^^^

* tbd

Deprecated APIs
^^^^^^^^^^^^^^^

* tbd

Removed APIs
^^^^^^^^^^^^

* ``OC.loadScript`` and ``OC.loadStyle``: Use ``OCP.Loader`` instead.
* ``OC.appSettings``: There is no replacement.

Back-end changes
----------------

Development dependency hell
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Due to the popularity of CLI tools for development of Nextcloud apps, the likelihood of package conflicts has increased. It's highly recommended to see :ref:`app-composer-bin-tools` and migrate to composer bin directories.

Updated core libraries
^^^^^^^^^^^^^^^^^^^^^^

If apps use only official public APIs of Nextcloud, the update of core libraries should have little to no effect on apps. However, there are some edge cases where an app still has a code dependency to a library shipped with Nextcloud, e.g. when those 3rdparty classes or functions are used, and therefore app developers are recommended to check their code for any incompatibility. Moreover it's recommended to check compatibility with sophisticated tools, as documented at the  :ref:`static analysis<app-static-analysis>` section.

``doctrine/dbal``
*****************

The Doctrine Database Abstraction Layer powers Nextcloud's database connection and query builder. In Nextcloud 28, this dependency was updated from 3.3.8 to 3.6.y.

Optimistically speaking, the database connection and the query builder should mostly work like in Nextcloud 27 or older.
Some (minor) breaking changes were inevitable. Here's the summary

* When a query builder instance is using positional parameters ``->setValue('name', '?')`` ``setParameter(0, $name)``, all parameters need to be set again when executing the query multiple times, e.g. in a loop. It is recommended to switch to named parameters using ``createParameter()`` instead.

The details of this change can also be seen in the `pull request on GitHub <https://github.com/nextcloud/server/pull/38556>`__ and in the upstream `dbal 3.6.x upgrade document <https://github.com/doctrine/dbal/blob/3.6.x/UPGRADE.md>`__.

Added APIs
^^^^^^^^^^

* ``\OCP\Mail\IMessage::setSubject`` to set an email subject. See :ref:`email` for an example.
* ``\OCP\Mail\IMessage::setHtmlBody`` and ``\OCP\Mail\IMessage::setPlainBody`` to set an email body See :ref:`email` for an example.
* ``\OCP\IEventSourceFactory`` to create a ``OCP\IEventSource`` instance.
* ``\OCP\Preview\BeforePreviewFetchedEvent::getCrop``
* ``\OCP\Preview\BeforePreviewFetchedEvent::getHeight``
* ``\OCP\Preview\BeforePreviewFetchedEvent::getMode``
* ``\OCP\Preview\BeforePreviewFetchedEvent::getWidth``

Changed APIs
^^^^^^^^^^^^

* ``\OCP\Preview\BeforePreviewFetchedEvent`` now accepts: ``width, height, crop and mode`` as optional constructor arguments.
* Interface ``\OCP\Files\Folder`` got a new method: ``searchBySystemTag(string $tagName, string $userId, int $limit = 0, int $offset = 0)``.
* ``OCP\SystemTag\ISystemTagManager::getTagsByIds()`` now optionally accepts `IUser` as second parameter, to only retrieve system tags visible to that user.

Deprecated APIs
^^^^^^^^^^^^^^^

* ``\OCP\Preview\BeforePreviewFetchedEvent`` passing ``null`` for ``width, height, crop or mode`` is deprecated. Starting with Nextcloud 31 they are mandatory.

Removed APIs
^^^^^^^^^^^^

* ``\OC_App::getAppVersion``: inject ``\OCP\App\IAppManager`` and call ``\OCP\App\IAppManager::getAppVersion``.
* ``\OC_App::getAppInfo``: inject ``\OCP\App\IAppManager`` and call ``\OCP\App\IAppManager::getAppInfo``.
* ``\OC_App::getNavigation``: inject ``\OCP\App\IAppManager`` and call ``\OCP\App\IAppManager::getAll``.
* ``\OC_App::getSettingsNavigation``: inject ``\OCP\App\IAppManager`` and call ``\OCP\App\IAppManager::getAll('settings')``.
* ``\OC_App::isEnabled``: inject ``\OCP\App\IAppManager`` and call ``\OCP\App\IAppManager::isEnabledForUser``.
* ``\OC_Defaults::getLogoClaim``: there is no replacement.
* ``\OCP\Util::linkToPublic``: there is no replacement.
* ``\OC_Defaults::getLogoClaim``: There is no replacement.
* ``\OC::$server->createEventSource()`` has been removed, use ``\OC::$server->get(\OCP\IEventSourceFactory::class)->create()`` instead.

The factory ``\OCP\IEventSourceFactory`` works only from Nextcloud 28.
For older versions use ``\OC::$server->createEventSource()``.

If you want to support Nextcloud 27 and Nextcloud 28:

.. code-block:: php

	// @TODO: Remove method_exists when min-version="28"
	if (method_exists(\OC::$server, 'createEventSource')) {
		$eventSource = \OC::$server->createEventSource();
	} else {
		$eventSource = \OCP\Server::get(IEventSourceFactory::class)->create();
	}
