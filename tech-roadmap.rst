.. _technical-roadmap:

Technical roadmap
=================

Beaker is a big project, with a lot of moving parts. It's been around for
quite a while, and is used in several different ways by different groups
of people.

This technical roadmap is designed to highlight areas of currently active
development, as well as more speculative changes that may happen at some
point in the future. :ref:`design-proposals` describe the major
changes and some of them are also referenced here.

If any of these projects sound particularly interesting, folks are welcome to
:doc:`get involved <guide/index>`.

Active development
------------------

The ideas in this section are currently under active development. Patches for
at least some of these are likely to be found on the `Beaker Gerrit server
<http://gerrit.beaker-project.org>`_, and in the absence of unexpected
complications, they should show up in a Beaker release within the next few
months. Searching `Bugzilla
<https://bugzilla.redhat.com/buglist.cgi?product=Beaker&bug_status=__open__>`_
for Beaker bugs with target milestones set will often provide more detail on
the specific proposals.

Web UI modernisation
~~~~~~~~~~~~~~~~~~~~

The current main web UI is based on the TurboGears 1 stack (although it
uses SQLAlchemy rather than SQLObject for the database access layer). This
makes some aspects of development more awkward than they might be with a
more recent web framework.

Starting with the Beaker 28 release, the main web server is in the
process of being migrated to Flask, by allowing endpoints to be
implemented Flask handlers. We are also aiming to replace the front end
components with cleaner alternatives based on React/Patternfly.

As part of those activities, we are planning to build web UI as separate
service.

Reference harness modernisation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

At present all Beaker recipes are run with the same harness, Beah. This
harness includes a number of features solely for backwards compatibility
with a legacy test execution system that is now long obsolete.

The :doc:`proposals/reference-harness` design proposal covers the creation
of a new reference harness that not only eliminates those no longer needed
backwards compatibility features, but is also able to operate independently
of Beaker and adds new capabilities (such as executing tests directly from
source control) that would be difficult to implement as part of the current
harness.

Issues with running the existing Beah harness inside a container has raised
the importance of making the new reference harness more readily available
to Beaker users.

Planned development
-------------------

The ideas in this section are firmly on the to-do list, but it is not yet
clear when they will be ready for inclusion.

Task oriented guides for users and administrators
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Beaker offers a lot of power and flexibility, but that can often come at
the price of making the right way to do certain things non-obvious. Beaker's
documentation is likely to benefit from additional sections that take a
"task-based" approach to documentation and answer questions like "How do I
limit my recipe to systems with a graphics adapter?" or "How do I require
that my recipe run directly on bare metal and not in a VM?".

This will include a general "troubleshooting guide" to help users and
administrators collaborate effectively in tracking down the more obscure
failures that can occur with the kind of integration testing Beaker
supports.

Systematic self-tests for provisioning and beah
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

As a tool for better validating new Beaker releases, as well as making it
easier to check for the correct operation of new Beaker installations, a
set of self-test Beaker tasks will be made readily available. These tasks
should come with helper scripts scripts for installing them into a
Beaker installation and the appropriate job definitions to execute them
across all configured architectures and distro trees.


Exploration
-----------

The ideas in this section are projects that one or more of the current
developers are at least tinkering with, but they may be at wildly
divergent stages of maturity.

Explicit system pool selection
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Beaker currently schedules jobs on any system the user has access to,
preferring the users own systems over group systems, over the generally
accessible system pool.

This approach isn't always desirable, since some systems have special
features that should only be used when explicitly requested, or a user may
wish to target a specific job at a particular set of machines.

Allowing systems to be grouped into pools (independent of the access policies
used to grant or deny access to the systems) will allow users to express
more abstract preferences about machines that aren't directly related to
the system itself.

This idea is covered by the :ref:`proposal-system-pools` design proposal.

More flexible job prioritisation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Armed with the new user group and access policy models, and the new event
driven approach to scheduling, it becomes possible to offer system owners
much greater control over which recipes are selected to run on their
systems.

This idea is covered by the :doc:`proposals/effective-job-priorities` design
proposal.

Job based recipe access limitations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Running recipes can currently inadvertently interfere with systems running
recipes for unrelated jobs. While it is intentional that recipes can control
systems other than the one they are running on, there should really be a
mechanism that limits this access to only those systems running other
recipes within the same recipe set.

Unifying ``hostRequires`` filtering and web UI search functionality
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Beaker's job processing and the web UI both allow a user to identify a
subset of interest within the full set of available systems. The user
interface for these is necessarily different, as once is based on the XML
file defining a job, while the other is defined through an interactive web
form.

However, rather than being thin wrappers around a shared internal filter
creation API, the dynamic filter creation implementations in these
components are almost completely separate. This means that capabilities
are sometimes added to the ``hostRequires`` processing and not to the web
UI, or vice-versa.

It seems like it should be possible to substantially reduce the level of
duplication between these two components, and thus make it easier to add
new filtering and sorting criteria in the future.

Speculative ideas
-----------------

The ideas in this section aren't really in development at all. Instead,
they reflect capabilities we think we'd *like* Beaker to have, or other
improvements we'd like to make, and may even have some initial design
sketches behind them. While there are no current concrete plans to do
anything about any of the ideas in this section, we're certainly open to
discussing them and reviewing any proposed patches related to them.

Most of these are at least non-trivial projects, and it's an open question
if some of them are feasible at all. Some of them may prove to be bad ideas,
regardless of feasibility.


Automated classification of intermittent and spurious test failures
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The OpenStack CI infrastructure includes a tool called "Elastic Recheck".
Essentially what they do is take the automated logs from particular
OpenStack CI runs, feed them into an ElasticSearch instance, and then run
various classifiers over those logs. Elastic Recheck then posts back to
the failed change proposal, indicating the likely cause of the failure (and
potentially triggering a second check attempt).

While such a tool wouldn't need to be part of Beaker itself, it may still be
a useful feature to explore, and there may be a place for publishing suitable
classifiers in a related project.

A Beaker installation could potentially make use of such a tool in two ways.
Firstly, Beaker includes the concept of "result acknowledgements", where
users can "NAK" a result to indicate that it wasn't a valid test run (for
example, there was an error in the test else, or something failed in the
lab environment). An Bayesian classifier could be used to scan the logs of
NAKed results, looking for patterns that are likely to indicate these kinds
of "failures", which don't actually reflect a fault in the software being
tested.

Secondly, for genuine test failures, a Bayesian classifier could be used to
identify log data that is likely to correspond with a failed test, and
suggest that as a probable cause when a test fails, rather than requiring
users to trawl through the logs themselves. This is one of the key approaches
the OpenStack CI team used to build their Elastic Recheck tool - many of
the common failures were identified by automated scanning of previous failed
test runs rather than by identifying the causes of the failure directly.


Provisioning other hypervisors
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Beaker provides rich "guest recipe" functionality for testing installation
and other operations within a KVM based virtual machine. Testing against
non-KVM hypervisors is possible, but more awkward, as the guest VMs must be
precreated and registered with Beaker as full systems with appropriate
custom power scripts that handle the process of starting and stopping the
underlying virtual machines. This is an unfortunate limitation.

Asynchronous message queues
~~~~~~~~~~~~~~~~~~~~~~~~~~~

The provisioning service on the lab controllers currently receives
commands by polling a command queue stored on the main server. Similarly,
the main task scheduler polls the database to determine when new
and queued recipes can be assigned to systems.

It may be worth adopting `fedmsg <http://www.fedmsg.com>`__, or something
similar, to help get rid of these polling calls.

Alternate database backend
~~~~~~~~~~~~~~~~~~~~~~~~~~

The only currently supported database backend for the main server is MySQL
(or an equivalent, like MariaDB). There are all sorts of reasons why this
isn't good, but migrating to PostgreSQL isn't straightforward. The two main
issues to be addressed are the handling of queries where MySQL and
PostgreSQL have drastically difference performance characteristics
(and there's no solution that performs well in both), and the
challenge of actually doing a data migration for any existing
Beaker installations.

The status of ``beah``
----------------------

In many respects, ``beah``, the native Beaker test harness, duplicates aspects
of other test frameworks like `autotest <http://autotest.github.io/>`__,
`avocado <https://avocado-framework.github.io/>`__ and
`STAF <http://staf.sourceforge.net/>`__.

Being so heavily dependent on kickstart files and the RPM based task library,
``beah`` is also quite inflexible in terms of platform support.

The following kinds of changes will be considered for ``beah``:

* documentation improvements
* compatibility updates for supported test systems
* any changes needed for image based provisioning with OpenStack
* any changes needed for lab infrastructure compatibility
* reliability fixes
* equivalent capabilities for additions made to the stable harness API

Outside these areas, we consider it a poor use of resources to further
duplicate the effort going into development of other automated test
harnesses, and hence any major feature proposals for ``beah`` will likely be
rejected - we would prefer for any such efforts to be directed towards the
system changes needed to better support alternative harnesss.

To support existing Beaker users, the ``beah`` test harness will be
maintained indefinitely, and the kinds of changes noted above will continue
to be permitted. The only way ``beah`` itself would ever be phased out is if
a simpler and more robust alternative became available and was capable of
correctly executing all of the existing Beaker tests that the core Beaker
developers have access to. The :doc:`proposals/reference-harness` design
proposal is expected to lead to the eventual creation of just such a harness.

Previously implemented ideas
----------------------------

The following ideas were included in earlier versions of this roadmap, but
are now implemented in Beaker:

- `Running tests in a Container <https://beaker-project.org/docs/user-guide/contained-test-harness.html>`_
- `Job results in JUnit XML format <https://beaker-project.org/docs/whats-new/release-22.html#job-results-in-junit-xml-format>`__
- `System page redesign <https://beaker-project.org/docs/whats-new/release-19.html#improved-system-page>`__
- `Experimental support for Open Stack based dynamic virtualization <http://beaker-project.org/docs/whats-new/release-0.17.html>`__
- `Installation specific theming of the Web UI <https://beaker-project.org/docs/whats-new/release-0.17.html#theming-the-web-interface>`__
- `IPv6 support in the default test harness <http://beah.readthedocs.org/en/latest/admin.html#using-beah-for-ipv6-testing>`__
- `Delegating job submission <../docs/whats-new/release-0.14.html#submission-delegates>`__
- `Separate system architecture guide <../docs/whats-new/release-0.14.html#architecture-guide>`__
- `Jenkins plugin to launch Beaker jobs <https://lists.fedorahosted.org/pipermail/beaker-devel/2013-July/000657.html>`__
- `Self-service user groups <../docs/whats-new/release-0.13.html#more-flexible-user-groups>`__
- `Group ownership of jobs <../docs/whats-new/release-0.13.html#group-jobs>`__
- `autotest support for stable harness API <https://github.com/autotest/autotest/pull/629>`__
- `Stable harness API <../docs/whats-new/release-0.12.html#provisional-support-for-alternative-harnesses>`_
- `Working with multiple Beaker instances
  <../docs/whats-new/release-0.12.html#other-enhancements>`_



