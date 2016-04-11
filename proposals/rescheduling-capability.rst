.. _proposal-rescheduling-capability:

Re-scheduling Capability for Recipe Sets
========================================

:Author: Róman Joost
:Status: Proposed

Abstract
--------

Beaker should provide a better user experience for jobs with aborted recipe sets
due to broken hardware. This will be achieved by automatically re-scheduling
aborted recipe sets against a different system.

Dependencies
------------

None.

Background
----------

Aborted jobs mean a longer turnaround to useful information for users, because
the job has to be manually re-scheduled. This is even intensified by long
running jobs, long setup time or jobs with lots of tasks. Broken hardware can be
the reason as to why jobs are failing due to aborted recipe sets.

Proposal
--------

When a recipe set finishes, if any recipe in the set is aborted Beaker will
clone the recipe set and append it to the job. As part of the cloning process,
any recipes in the set which were Aborted will have their host requirements
modified to exclude the system where the recipe aborted previously. If there are
no other candidate systems for a recipe, the recipe set will not be
re-scheduled. An error message will indicate this case clearly to the user.

To avoid exhausting the machine pool, a hard limit on the number of times it
will be re-scheduled will be set by Beaker. It should be noted, that any aborted
recipe sets will remain in the job.

How to request recipe set re-scheduling
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A new, optional attribute ``max_retries`` on the ``recipeSet`` element can be
set to request the maximum number of re-schedules performed by Beaker.

Example:

.. code-block:: xml

  <recipeSet max_retries="3">
  ...
  </recipeSet>

will re-schedule the recipe set 3 times at maximum.

How to identify re-scheduled recipe sets
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Technically, cloned recipe sets will use a database field which stores a
reference to the original recipe set. A UI element will be used to make them
distinguishable from regular scheduled recipe sets.

Deferred features
-----------------

* The re-scheduling logic could be expanded to only re-schedule failed tasks,
  skipping tasks which already passed. However this would assume that all tasks
  in a recipe are completely independent and that there are no setup or teardown
  tasks which must always be included. In future Beaker may have a way of
  indicating such tasks (:issue:`1202739`) which would make conditional
  re-scheduling logic feasible.
