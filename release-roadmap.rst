.. _release-roadmap:

Release roadmap
===============

Beaker is developed iteratively, aiming to publish major new feature releases
every 6-12 weeks, with smaller, lower impact, maintenance releases published
between major versions.

This release roadmap aims to provide an overview of current plans for
upcoming releases, as well as a record of past releases, and the most
significant enhancements in those releases.

Refer to :ref:`dev-lifecycle` for more information on the Beaker development
process.


Upcoming releases
-----------------

Beaker 19.2
^^^^^^^^^^^

Planned release: January 2015

The Beaker 19.2 minor release (and associated updates to other Beaker
components) will be focused on improving Beaker's support for test execution
on Project Atomic container host systems, as well as Beaker's support for
bare metal testing on ppc64le and aarch64 systems.


Beaker 20
^^^^^^^^^

Planned release: late January 2015

The currently planned focus for Beaker 20 is to implement the previously
deferred predefined access policy feature from the
:ref:`proposal-access-policies` design proposal.


Beaker 21
^^^^^^^^^

Planned release: March 2015

The currently planned focus for Beaker 21 is to improve Beaker's result
reporting capabilities, including the ability to directly export results
in JUnit-compatible XML format, as well as to enhance the capabilities and
usability of the job results page in the main Beaker web interface.


Past releases
-------------

Beaker 19 (25th November, 2014)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The focus of Beaker 19 was to implement an improved system
details page in the Beaker web UI, as the Beaker 0.15 release not only
highlighted many of the shortcomings of the existing interface, but also
provided greatly improved tools for dealing with them.

This release also saw a change in Beaker's version numbering scheme to drop
the leading zero. Beaker has been in production use for several years at a
number of organisations, so continuing to use a version number scheme that
indicated pre-production status didn't really make sense.

See the `Beaker 19 Release Notes
<../../docs-release-19/whats-new/#beaker-19>`__ for details.


Beaker 0.18 (4th September, 2014)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Beaker 0.18 included improved usage reminder emails as described in
the :ref:`proposal-beaker-usage-report-emails` design propsal and
introduced better support for custom distros (as described in the
:ref:`custom-distros` design proposal).

See the `Beaker 0.18 Release Notes
<../../docs-release-0.18/whats-new/#beaker-0-18>`__ for details.


Beaker 0.17 (11th June, 2014)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Beaker 0.17 included two new scheduler features:

* A test harness independent system reservation mechanism via the
  ``<reservesys/>`` Job XML element. This makes it possible to debug
  issues during test execution which may have caused the external
  watchdog to expire, a kernel panic or an installation failure.

* Force schedule a job on a system irrespective of its status. This
  makes it possible to run diagnostic tests on broken or manual
  systems before adding them back to the pool of available systems.

In addition, it included experimental integration with OpenStack for
dynamically creating VMs (additional background available in the
:ref:`proposal-dynamic-virtualization` design proposal).

See the `Beaker 0.17 Release Notes
<../../docs-release-0.17/whats-new/#beaker-0-17>`__ for details.


Beaker 0.16 (14th March, 2014)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The focus of Beaker 0.16 was the :ref:`proposal-external-tasks` design
proposal, allowing tasks to be managed as references to external git
repositories, rather than forcing reliance on Beaker's centralised library of
task RPMs.

In addition to the significant benefits this offers in task management
itself (such as more exact reproducibility of previous test runs, easier
testing of experimental versions of tasks and more flexibility in test
structure), this proposal also has the benefit of avoiding the need to
frequently regenerate yum repo metadata for a central task library that may
end up containing thousands of tasks.

See the `Beaker 0.16 Release Notes
<../../docs-release-0.16/whats-new/#beaker-0-16>`__ for details.


Beaker 0.15.1 (22nd October, 2013)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The focus of Beaker 0.15 was the per-system access policy portion of
:ref:`proposal-access-policies`.

Just as the enhanced user group model allowed groups to assume shared
management of jobs, the new access policy model allows groups to
assume shared management of systems.

See the `Beaker 0.15 Release Notes
<../../docs-release-0.15/whats-new/#beaker-0-15>`__ for details.

Note that the initial release of Beaker 0.15 including a number of critical
defects in the revised permissions model and the upgraded web interface that
rendered it effectively undeployable. The release date given above is for the
0.15.1 maintenance release that addressed these critical issues.

Due to the extended maintenance lifecycle for Beaker 0.14, Beaker 0.15 also
had an extended maintenance life cycle (through to February 2014).


Beaker 0.14 (2nd August, 2013)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The focus of Beaker 0.14 development was the completion of
:ref:`proposal-enhanced-user-groups`, by allowing users to nominate
"submission delegates" that can submit jobs on their behalf.

See the `Beaker 0.14 Release Notes
<../../docs-release-0.14/whats-new/#beaker-0-14>`__ for details.

Due to the issues with the initial Beaker 0.15 update, Beaker 0.14
received an extended maintenance life cycle (through to December 2013).


Beaker 0.13 (7th June, 2013)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The focus of Beaker 0.13 was :ref:`proposal-enhanced-user-groups`

The elements of the proposal implemented in this release included three key
elements:

* Administrators may delegate membership of specific groups to an
  LDAP server (to avoid maintaining membership data in two locations)
* Users may create and manage their own custom groups (to avoid overloading
  the administrators of large installations)
* Jobs may be submitted on behalf of a group, granting all members of that
  group full access to the job (to avoid the creation of shared accounts
  for collective management of jobs)

See the `Beaker 0.13 Release Notes <../../docs/whats-new/#beaker-0-13>`__ for
details.


Beaker 0.12 (5th April, 2013)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Beaker 0.12 made it easier for users to switch between production and
development Beaker instances. It has three key elements:

* A new script was added to the Beaker server tools, which allows a
  system administrator to update the task library from the task
  library of another Beaker instance
* The Beaker client gained a new ``--hub`` parameter which makes it easy
  to run a command against a Beaker instance other than the one in
  the system or user configuration file.
* The Beaker client configuration architecture was adjusted to make it
  easy to provide a system wide site specific configuration file, rather
  than requiring each user to define their own configuration

See the `Beaker 0.12 Release Notes <../../docs/whats-new/#beaker-0-12>`__ for
details.
