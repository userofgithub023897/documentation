=======================
Upgrade to Nextcloud 27
=======================

.. note:: Critical changes were collected `on GitHub <https://github.com/nextcloud/server/issues/37039>`_.
    See the original ticket for links to the pull requests and tickets.

General
-------

info.xml
^^^^^^^^

Make sure your ``appinfo/info.xml`` allows for Nextcloud 27.

.. code-block:: xml

    <dependencies>
        <nextcloud min-version="23" max-version="27" />
    </dependencies>

Front-end changes
-----------------

Removed APIs
^^^^^^^^^^^^

* tbd

Back-end changes
----------------

Removal of PSR-0 class loader
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Nextcloud 27 no longer loads classes in the deprecated :ref:`PSR-0 naming standard<psr0>`. :ref:`Update the structure to PSR-4<app-psr4-autoloader>` or :ref:`ship a custom autoloader<app-custom-classloader>`.

Added APIs
^^^^^^^^^^

* ``\OCP\AppFramework\Http\EmptyContentSecurityPolicy::allowEvalWasm(bool)``: sets ``wasm-unsafe-eval`` in ``script-src`` of the Content Security Policy `to allow compilation and execution of WebAssembly on the page <https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/script-src#unsafe_webassembly_execution>`_

  * ``wasm-unsafe-eval`` is `supported by most browsers <https://caniuse.com/mdn-http_headers_content-security-policy_script-src_wasm-unsafe-eval>`_
  * WebAssembly compilation and execution in worker threads is not affected by this directive (browsers allow compilation and execution of WebAssembly in worker threads by default)

* tbd

Changed APIs
^^^^^^^^^^^^

* tbd

Deprecated APIs
^^^^^^^^^^^^^^^

* tbd

Removed APIs
^^^^^^^^^^^^

* tbd
