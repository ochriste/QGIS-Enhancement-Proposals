.. _qep#[.#]:

==============================================================================
QGIS Enhancement #: Add QGIS server access control interface for python plugin
==============================================================================

:Date: 2015/05/27
:Author: Stéphane Brunner
:Contact: stephane dot brunner at camptocamp dot com
:Last Edited: 2015/05/27
:Status:  Draft | Adopted (YYYY/MM/DD) | Completed (YYYY/MM/DD) |
          Superseded by :ref:`QEP #[.#] <qep#[.#]>` (YYYY/MM/DD) |
          Abandoned (YYYY/MM/DD) | Withdrawn (YYYY/MM/DD)
:Version: QGIS X.X

.. note::

    See :ref:`QEP 1 <qep1>` for description of QEP process.

#. Summary
----------

Add the possibility to create python plugin for access control
to all the services of QGIS server,

The plugin will implements the following interface:


 * ``layerFilter`` Return an additional filter, used in WMS/GetMap,
   WMS/GetFeatureInfo, WFS/GetFeature to filter the features.
 * ``layerPermissions`` Change the rights on the layer per user
   (known by the plugin). The concerned rights are: publish, insert, update,
   delete. Mostly used in WFS/Transaction, and the publish in all requests.
 * ``authorizedLayerAttributes`` Be able to show some attributes only for a
   subset of user Used in: WMS/GetFeatureInfo, WFS/GetFeature.
 * ``allowToEdit`` Be able to disallow the edition of a particular feature,
   in our case base on the Geometry Used in: WFS/Transaction.

Our need is to be able to do the following kind of restrictions::

 * Geometric restriction that depends on the connected user and the layer.
 * Attributes restriction that depends on the connected user and the layer.

All the knowledge about which user is connected and on what he is allowed to
access is the responsability of the plugin.

``layerFilter`` and ``allowToEdit`` fill nearly the same use case,
but ``layerFilter`` is optimised to create requests and concerns the read access,
and ``allowToEdit`` is optimised to be used on an existing feature object
and concerns the write access.

#. Implementation Details
-------------------------

See: https://github.com/qgis/QGIS/pull/2056.

I create a new kind of server plugin and I don't extend the current server filter
for the following reason::
 * I dont want to remove things in the xml that will be build by QGIS.
 * I dont want the implementation of the plugin to be too complex.
 * The FEATURE_COUNT won't(???) correctly work with hooks.


#.# Python Bindings
...................

See summary.

#.# Affected Files
..................

See: https://github.com/qgis/QGIS/pull/2056/files.

#. Test Coverage
----------------

I don't know how we can do unit test for this. I think one possibility is to
run the server on apache, and test the query results.

#. Performance Implications
---------------------------

Should be really low if there no plugin that implements it.
Also depends on the plugin implementation.

#. Backwards Compatibility
--------------------------

New interface, then no incidence.

#. Documentation
----------------

We can create restriction access python plugin.

The class to implements is ``qgis.server.qgsaccesscontrolplugin``.

The optional methods to implement are:

#.# ``layerFilter``
...................

Return a filter for the map that will be used in the following requests::
 * WMS/GetMap
 * WMS/GetFeatureInfo
 * WFS/GetFeature

#.# ``layerPermissions``
........................

Restrict the permissions on the layer. The concerned rights are: 
publish, insert, update and delete.

The publish permission is used in all the request that concern a layer
including the ``GetCapabilities``.

The others permissions are be used in the WFS/Transaction.

#.# ``authorizedLayerAttributes``
.................................

Restrict the published feature attributes.

Used in the following requests::
 * WMS/GetFeatureInfo
 * WFS/GetFeature

#.# ``allowToEdit``

Disallow the edition of a specific kind of feature.

Used in the following requests::
 * WFS/Transaction

#. Issue Tracking ID(s)
-----------------------

#2056

#. Voting History
-----------------

(required)
