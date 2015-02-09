.. _proposal-predefined-access-policies:

Predefined Access Policies for Systems
======================================

:Author: Amit Saha, Dan Callaghan
:Status: In progress
:Target Release: 20

Abstract
--------

:ref:`proposal-access-policies` introduced custom access policies for
systems. It allowed adding access policy rules granting various
permissions to individual users or groups of users. However, it did
not allow systems to be setup with a predefined policy. This meant
that when a new system is added, acceess policy rules *always* had to
be manually defined. This proposal aims to allow systems to either
define a custom access policy or to use a predefined system pool
policy thus allowing a mechamism to apply the same access policy
consistently across many systems.

Dependenices
------------
None

Proposal
--------

An initial implementation of :ref:`proposal-system-pools` will be
completed as part of this work to allow pool owners to define a pool access
policy. A "pool" is a named collection of systems. Each pool has an "owning
group". Users in the owning group are allowed to add and remove
systems from the pool.

System owners and admins will then have the ability to either
define a custom access policy specific to the system or use an
existing policy which was defined for a system pool.

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

   Select "Systems -> Pools" from the menu, then click "Create". Enter the
   pool name, and select a group to own the pool.

Through the ``bkr`` cli::

   bkr pool-create --owner-group=<groupname> <poolname>

A new pool is created containing no systems.


Controlling system pool membership
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* As a pool owner, I want to add a system to my pool.

Through the web UI:

   On the system page, click the "Pools" tab. Enter the name of the pool and 
   click "Add".

Through the ``bkr`` cli::

    bkr pool-add --pool=<poolname> --system=<fqdn>

* As a pool owner, I want to remove a system from my pool.

Through the web UI:

   On the system page, click the "Pools" tab. Click "Remove" next to your pool.

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
  policy rules. Only pool owners and administrators can change the
  pool policy.

Through the ``bkr`` cli:

Create an access policy for a pool with a single rule::

    bkr pool-policy-create --pool=<poolname> \
        --permission=reserve --group=qeteam

Remove the pool policy::

    bkr pool-policy-remove --pool=<poolname>

Selecting a predefined access policy for systems
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* As a Beaker system owner or administrator, I want to set a
  predefined access policy for my system(s):

Through the web UI:

   Go to the "Access Policy" tab on a system's page and then click on
   "Use Access Policy from System Pool" and then type in the system
   pool name whose access policy is to be used for the system and
   click on "Save Changes".

Through the ``bkr`` cli::

   bkr policy-grant --system=test1.example.com --pool-policy <poolname>

The system will now use the access policy defined by the pool. Note
that this will not a custom access policy that may already exist for
the system. It will just not be applicable.

Revoking a predefined access policy for systems
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* As a Beaker system owner or administrator, I want to revoke a
  predefined access policy for my system(s):

Through the web UI:

   Go to the "Access Policy" tab on a system's page and then click on
   "Revoke Access Policy from System Pool".

Through the ``bkr`` cli::

   bkr policy-revoke --system=test1.example.com --pool-policy

The system will no longer use the access policy defined by a
pool. If a custom access policy for the system was earlier defined,
this will now take effect.


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

* Pool deletion. The initial iteration does not allow pools to be deleted,
  or even hidden. This feature may actually be needed to make various other
  parts of the UI usable, in which case it will be designed and implemented
  for the target release (and the design proposal updated accordingly).

* Cloning system or pool access control policies. The initial iteration does
  not include the ability to copy an existing policy when creating a new
  policy for a system or pool.

