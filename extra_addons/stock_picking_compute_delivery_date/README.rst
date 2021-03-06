.. image:: https://img.shields.io/badge/licence-AGPL--3-blue.svg
    :alt: License: AGPL-3

Stock Picking Compute Delivery Date
===================================

This module allows to recompute the delivery dates of outgoing Moves based on
the dates of incoming Moves for the same product. That means that for every
outgoing move we update the expected date based on our best guess based on the
information we have.

The method we use is different for moves that are chained (MTO flow) or moves
that are not (MTS flow).

In all cases, at the end we add the security margin defined for the company.

MTO flow
========
For every outgoing Move the system finds the corresponding incoming Move. That
is identified by the 'move_dest_id' (Destination Move) field. In the usual
case where the incoming Move is generated by a Purchase Order that in turn is
generated by the Scheduler, that field is filled automatically by OpenERP.
For that module we use only that field, so we do not need a dependency on the
Purchase module.

The date of the incoming Move, plus the Security Days defined in the Company,
is then written in the outcoming Move. The existing expected date is not used
and is overwritten.

MTS flow
========
For the Make To Stock flow, the logic is more complex.

First, for each product, the system takes the list of outgoing Moves ordered
by creation date, and the list of incoming Moves ordered by expected date.

It then loops over the outgoing Moves, and for each one, it tries to find at
what time we will have enough stock to make the delivery. That information is
based on the current stock, and on the day when we expect to receive
deliveries of the same product.

If incoming Moves run out, we stop processing the product and leave any
remaining outgoing Moves untouched. We may decide to change that logic later.

User Interface
==============
The process can be run in three ways:

* Select a few lines from the Products tree view, and from "More" click
  "Compute delivery dates for all products".
* Click Warehouse / Products / Compute all delivery dates.
* A Scheduled action is provided, initially disabled.

Changes
=======
With version 1.1, this module was ported to Odoo 8.0. Since there are no more
MTO and MTS products as such, each move is now individually processed with one
flow or the other depending on the presence of moves chained to it, or not.

Possible future improvements
============================
* Behave differently when incoming Moves run out.
* Use the Priority field to decide which outgoing Moves to process first (now
  only the expected date is used).
* Since v8 a change in the expected date of move changes immadiately the 
  expected dates of the chained moves. This could make the MTO part of this
  module unnecessary, if the change is consistent. That could be investigated
  further.
* For the MTS computation, all outgoing and incoming moves are grouped together
  for the computation of the availability plan. In other words, the plan is
  global for any products, and it does not take into account the presence of
  different warehouses.

Bug Tracker
===========

Bugs are tracked on `GitHub Issues <https://github.com/OCA/{project_repo}/issues>`_.
In case of trouble, please check there if your issue has already been reported.
If you spotted it first, help us smashing it by providing a detailed and welcomed feedback
`here <https://github.com/OCA/{project_repo}/issues/new?body=module:%20{module_name}%0Aversion:%20{version}%0A%0A**Steps%20to%20reproduce**%0A-%20...%0A%0A**Current%20behavior**%0A%0A**Expected%20behavior**>`_.


Credits
=======

Contributors
------------

* Leonardo Pistone <leonardo.pistone@camptocamp.com>

Maintainer
----------

.. image:: https://odoo-community.org/logo.png
   :alt: Odoo Community Association
   :target: https://odoo-community.org

This module is maintained by the OCA.

OCA, or the Odoo Community Association, is a nonprofit organization whose
mission is to support the collaborative development of Odoo features and
promote its widespread use.

To contribute to this module, please visit http://odoo-community.org.

