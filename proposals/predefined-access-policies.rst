.. _proposal-predefined-access-policies:

Predefined Access Policies for Systems
======================================

:Author: Amit Saha, Dan Callaghan, Nick Coghlan
:Status: Implemented
:Release: `20 <http://beaker-project.org/docs/whats-new/release-20.html>`__

Abstract
--------

:ref:`proposal-access-policies` implemented in Beaker 0.15
presents some usability challenges for shared system management: 

* By default, each new system is created with a custom access policy
  that merely grants all users the "View" permission. Only the system
  owner (which defaults to the user that created the system entry) can
  edit the custom access policy or do anything else with the system.

* This is inconvenient for shared system management, as it means each
  new system needs to have its policy configured appropriately for the group
  that manages it. Changes to policy rules must also be applied to all systems
  covered by that policy.

* While these activities can be scripted through the remote web API,
  there are still plenty of opportunities to get things wrong.

This design proposal addresses that problem by changing the procedure
for configuring a newly added group managed system to be:

* add the new system to the appropriate ``system pool``
* configure the system to use the pool policy rather than its own custom policy

The new system will then use the preconfigured pool policy for access
control, and automatically respect any future changes to that policy. If
access needs to be temporarily restricted to meet specific access criteria,
the system can be switched back to using its custom policy for a time,
before being returned to using the appropriate pool policy.

Dependenices
------------
None

Proposal
--------

An initial implementation of :ref:`proposal-system-pools` will be
completed as part of this work to allow pool owners to define a pool access
policy. A "pool" is a named collection of systems and the creating
user is set as the pool "owner". The owner of the pool can be changed
to a "owning group". Users in the owning group are allowed to add and remove
systems from the pool. If the "owning group" of a system pool is
deleted, the deleting user is made the "owning user" of the system pool.

System owners and admins will then have the ability to either
define a custom access policy specific to the system or use an
existing policy which was defined for a system pool. The system *must*
be added to a pool first to be able to use its policy. Using a policy
defined for any arbitrary system pool will not be possible.

Note that the system pools feature implemented as part of this work
will only have a subset of the features described in the
:ref:`proposal-system-pools` design proposal. It will essentially be a
renaming of the existing "system groups" feature and aims to remove
the ambiguity between system groups and user groups.

Proposed User Interface
-----------------------

Defining ad hoc system pools
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* As a Beaker user, I want to define a new pool of systems.

Through the web UI:

   Select "Systems -> Pools" from the menu, then click "Create" and enter the
   pool name.

Through the ``bkr`` cli::

   bkr pool-create <poolname>

A new pool is created containing no systems.

Modifying system pool details
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* As a pool owner, I want to change my pool's details such as the
  pool name, description or owner.

Through the web UI:

   Select "Systems -> Pools" from the menu, then click on the
   pool name which you want to modify. This will open the pool's page
   where you can click on "Edit" and change the specific detail.
   Click on "Save changes" once done.

Through the ``bkr`` cli::

   bkr pool-modify <poolname> --name <name>
   bkr pool-modify <poolname> --description <description>
   bkr pool-modify <poolname> --owner <user>
   bkr pool-modify <poolname> --owning-group <group>

The changes should take effect.

Controlling system pool membership
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* As a pool owner, I want to add a system to my pool.

Through the web UI:

   On the system page, click the "Pools" tab. Enter the name of the pool and 
   click "Add".

This can also be done from the pool's page by going to the "Systems"
tab, typing in the system FQDN and clicking on "Add".

Through the ``bkr`` cli::

   bkr pool-add --pool=<poolname> --system=<fqdn>

* As a pool owner, I want to remove a system from my pool.

Through the web UI:

   On the system page, click the "Pools" tab. Click "Remove" next to your pool.

This can also be done from the pool's page by going to the "Systems"
tab and clicking on "Remove" against the system FQDN.

Through the ``bkr`` cli::

   bkr pool-remove --pool=<poolname> --system=<fqdn>

Viewing/Modifying Pool Policy
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* As a System Pool creator, Beaker system owner or administrator, I
  want to see/edit the access policy defined for a pool.

Through the web UI:

   Go to the "Systems->Pools" page to view the list of system pools
   defined and then click on the system pool name. This will take you
   to the system pool's page where you can view and modify existing
   policy rules. Pool owners, administrators and users with the
   ``edit_policy`` permission can change the pool policy.

Through the ``bkr`` cli:

Grant "reserve" permission to "qeteam" user group::

   bkr policy-grant --pool=<poolname> \
        --permission=reserve --group=qeteam

Revoke the above permission::

   bkr policy-revoke --pool=<poolname> \
        --permission=reserve --group=qeteam

Selecting a predefined access policy for systems
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* As a Beaker system owner, privileged user or administrator, I want to set a
  predefined access policy for my system(s):

Through the web UI:

   Go to the "Access Policy" tab on a system's page and then click on
   "Use policy from pool:" and select the system pool whose access
   policy is to be used for the system and click on "Save Changes".

Through the ``bkr`` cli::

   bkr system-modify test1.example.com --pool-policy <poolname>

The system will now use the access policy defined by the pool. Note
that this will not remove the custom access policy for the system, and
the system can be switched back to using the custom access policy.

When a system is removed from a pool or the system pool is deleted,
any system using its access policy is updated to use its custom access
policy instead.

Switching to a system's custom access policy
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* As a Beaker system owner, privileged user or administrator, I want to switch to my
  system's custom access policy:

Through the web UI:

   Go to the "Access Policy" tab on a system's page and then click on
   "Use Custom Access Policy".

Through the ``bkr`` cli::

   bkr system-modify test1.example.com --use-custom-policy

The system will no longer use the access policy defined by a
pool and will use the system's custom access policy.

Deleting a system pool
~~~~~~~~~~~~~~~~~~~~~~

* As a system pool owner or administrator, I want to delete a system
  pool:

Through the web UI:

   Select "Systems -> Pools" from the menu, then click on the
   pool name which you want to delete. This will open the pool's page
   where you can click on "Delete" to delete the pool.

Through the ``bkr`` cli::

   bkr pool-delete <poolname>

Deferred features
-----------------

The following additional features are under consideration, but have been
deliberately omitted in order to reduce the complexity of the initial
iteration of the design:

* Adding other pools as members of a pool. The initial iteration
  does not allow pools to be members of other pools, which introduces
  potential concerns about scalability in large organisations.

  Adding this feature may also make it possible to effectively delegate
  the ability to add systems to other pools.

  See the deferred subgroups feature in :ref:`proposal-enhanced-user-groups`
  for a possible implementation strategy that could also be used for
  system subpools.

* Cloning system or pool access control policies. The initial iteration does
  not include the ability to copy an existing policy when creating a new
  policy for a system or pool.

