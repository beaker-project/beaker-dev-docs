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

Beaker 24
^^^^^^^^^

Beaker 24 will provide a production ready implementation for provisioning
virtual machines in OpenStack. This provides a basis for supporting image based
provisioning with OpenStack Glance.

Past releases
-------------

Beaker 23 (7th July, 2016)
^^^^^^^^^^^^^^^^^^^^^^^^^^

The focus on Beaker 23 was to improve Beaker's result reporting capabilities, as
described in :ref:`proposal-job-page-improvements`. It ships with a new recipe
state reflecting the provisioning of a machine and generating GRUB2 menus for
x86 EFI systems.

Beaker 22 (14th January, 2016)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Last maintenance release: **22.3 (1st April, 2016)**

Beaker 22 added support for extra job XML elements and inverted groups.

This release also provides the ability to directly export results in a
JUnit-compatible XML format, rather than continuing to require the use of
external XSLT templates to perform the translation.

Beaker 21 (26th August, 2015)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Last maintenance release: **21.2 (17th November, 2015)**

Beaker 21 was shipped with one-click hardware scanning support for all Beaker
systems. From this release onwards, Beaker uses ``lshw`` for improved hardware
scanning abilities. This results in better system capability reporting,
resulting in a richer set of attributes to query on a larger set of hardware
architectures.

Beaker 20 (20th April, 2015)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Last maintenance release: **20.2 (14th July, 2015)**

The focus for Beaker 20 was to implement the
:ref:`proposal-predefined-access-policies` design proposal. This proposal
includes an initial implementation of "system pools" (replacing the current
system groups feature), and allows systems to be configured to use the access
policy associated with one of their pools, rather than the default system
specific custom policy.

This release will also enhance Beaker's bare metal provisioning support by
making it possible to select which bootloader to use based not only on the
CPU architecture of the server being provisioned, but also the specific
operating system being installed.



Beaker 19 (25th November, 2014)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Last maintenance release: **19.2 (15th January, 2015)**

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

Last maintenance release: **0.18.4 (24th October, 2014)**

Beaker 0.18 included improved usage reminder emails as described in
the :ref:`proposal-beaker-usage-report-emails` design propsal and
introduced better support for custom distros (as described in the
:ref:`custom-distros` design proposal).

See the `Beaker 0.18 Release Notes
<../../docs-release-0.18/whats-new/#beaker-0-18>`__ for details.


Beaker 0.17 (11th June, 2014)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Last maintenance release: **0.17.3 (14th August, 2014)**

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

Last maintenance release: **0.16.2 (17th April, 2014)**

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

Last maintenance release: **0.15.5 (25th February, 2014)**

The focus of Beaker 0.15 was the :ref:`proposal-access-policies` design
proposal.

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
had an extended maintenance life cycle.


Beaker 0.14 (2nd August, 2013)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Last maintenance release: **0.14.4 (29th November, 2013)**

The focus of Beaker 0.14 development was the completion of
:ref:`proposal-enhanced-user-groups`, by allowing users to nominate
"submission delegates" that can submit jobs on their behalf.

See the `Beaker 0.14 Release Notes
<../../docs-release-0.14/whats-new/#beaker-0-14>`__ for details.

Due to the issues with the initial Beaker 0.15 update, Beaker 0.14
received an extended maintenance life cycle.


Beaker 0.13 (7th June, 2013)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Last maintenance release: **0.13.2 (3rd July, 2013)**

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

Last maintenance release: **0.12.1 (23rd April, 2013)**

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


Earlier public releases
^^^^^^^^^^^^^^^^^^^^^^^

For details of all releases back to 0.9.0 (1st June, 2012), refer to
the `release download page <https://beaker-project.org/releases/>`__.

For dates of all releases back to 0.3 (14th May, 2009), refer to the release
tags in the `Beaker git repo
<https://git.beaker-project.org/cgit/beaker/refs/tags>`__.
