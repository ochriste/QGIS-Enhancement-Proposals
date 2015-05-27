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
   (known by the plugin) Concern rights: publish, insert, update,
   delete. Mostly used in WFS/Transaction, and the publish in all requests.
 * ``authorizedLayerAttributes`` Be able to show some attributes only for a
   subset of user Used in: WMS/GetFeatureInfo, WFS/GetFeature.
 * ``allowToEdit`` Be able to don't allow to edit a particular feature,
   in our case base on the Geometry Used in: WFS/Transaction.

Our need is to do the following kind of restrictions:

 * Geometric restriction that depends on the connected user and the layer.
 * Attributes restriction that depends on the connected user and the layer.

All the knowledge about witch user is connected and on what he can access
is the responsability of the plugin.

The ``layerFilter`` and the ``allowToEdit`` fills nearly the same use case,
but ``layerFilter`` is optimised to create requests and concern the read access,
and ``allowToEdit`` is optimised to used on an existing feature object
and concern the write access.

#. Implementation Details
-------------------------

See: https://github.com/qgis/QGIS/pull/2056.

I create a new kind of server plugin and I don't extend the current server filter
for the following reason::
 * I dont want the  remove things in the xml that will be build by QGIS.
 * I want that the implementation of the plugin isn't too complex.
 * The FEATURE_COUNT will don't correctly work with hooks.


#.# Python Bindings
...................

See summary.

#.# Affected Files
..................

See: https://github.com/qgis/QGIS/pull/2056/files.

#. Test Coverage
----------------

I don't know how we can do unit test on it, for me the possibility is to
run the server on apache, and test the query results.

#. Performance Implications
---------------------------

Should be relay low if there no plugins that implement it.
Also depends on the plugin implementation.

#. Backwards Compatibility
--------------------------

New interface, then no incidence.

#. Documentation
----------------

We can create restriction access python plugin.

The class to implements is ``qgis.server.qgsaccesscontrolplugin``.

The optional method to implement are:

#.# ``layerFilter``
...................

Return a filter for the map that will be used in the following requests::
 * WMS/GetMap
 * WMS/GetFeatureInfo
 * WFS/GetFeature

#.# ``layerPermissions``
........................

Restrict the permissions on the layer. The concern rights are: 
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

Don't allow to edit a specific kink of feature.

Used in the following requests::
 * WFS/Transaction

#. Issue Tracking ID(s)
-----------------------

#2056

#. Voting History
-----------------

(required)
