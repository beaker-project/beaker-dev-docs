.. _proposal-system-pools:

Scheduler Integration for System Pools
======================================

:Author: Dan Callaghan, Nick Coghlan
:Status: Deferred
:Target Release: TBD


Abstract
--------

This proposal builds on the system pools defined in
:ref:`proposal-predefined-access-policies` by allowing job submitters to
express preferences and requirements for system pools in their jobs.


Proposal deferral
-----------------

Further work on this proposal is currently deferred, pending research into
the Apache Mesos scheduling meta-framework, and its potential suitability
as a replacement for the current bespoke Beaker scheduler.


Proposal
--------

As defined in :ref:`proposal-predefined-access-policies` a "pool" in this
proposal is a named collection of systems. Each pool has an "owning group".
Users in the owning group are allowed to add and remove systems from the pool.

The proposal described in this document allows job submitters to influence the
system selection through the job XML based on a system's pool membership.


Proposed user interface
-----------------------

Restrict recipe execution to a specific system pool
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* I want to submit a job and limit it to systems in a particular pool.

Through the job XML:

   Use  ``<pool op="=" value="somepool" />`` in the
   ``<hostRequires/>`` of your job XML.

Through the ``bkr`` cli:

   Pass ``--hostrequire=pool=somepool`` to a workflow command.

This filter will select only systems which are in the given pool.


Prefer particular system pools for recipe execution
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* I want to submit a job and express an ordered preference regarding
  the pools where I would like the job to run.

Through the job XML:

   Use ``<autopick/>`` in the ``<recipe/>`` section of the job XML, with a
   sequence of ``<pool/>`` elements::

       <autopick>
           <pool>somepool</pool>
           <pool>anotherpool</pool>
       </autopick>

   There is an implied "other systems" at the end, which covers all other
   systems which the user has access to (use ``<hostRequires/>`` to limit
   a job to specific system pools).

   When ``random="true"`` is set on the autopick element, the pool order
   in the XML is still used, but the specific system used is selected
   randomly from within each pool (or the implied "other systems" after
   the list of specific pools is exhausted). To select randomly from
   multiple pools without expressing a preference between them, use
   an empty ``<autopick  random="true" />`` element and several
   ``<pool/>`` selection elements grouped under a ``<or/>`` element
   in the ``<hostRequires/>`` section of the job XML.


Deferred features
-----------------

The following additional features are under consideration, but have been
deliberately omitted in order to reduce the complexity of the initial
iteration of the design:

* Allowing users to specify a default pool preference to be used when there
  is no ``autopick`` section in the submitted recipe XML.
