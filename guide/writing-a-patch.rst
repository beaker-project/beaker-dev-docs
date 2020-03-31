Writing a patch
===============

.. highlight:: console

Create a local working branch
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Start by creating a local git branch where you will commit your work::

    git checkout origin/develop -b myfeature

This will create a local git branch called ``myfeature`` that is associated
with the upstream ``develop`` branch for easy rebasing.

Beaker uses a number of Javascript libraries which are tracked using
git submodules. You must run ``git submodule init`` followed by ``git
submodule update`` to clone them.

For new features and any invasive bug fixes (e.g. those requiring database
changes), the working branch should be based on ``origin/develop`` as shown.

For bug fixes that don't require invasive changes, then the working branch
should be based on the latest release branch. For example, if the latest
release shown on the `release download page
<http://beaker-project.org/releases/>`__ is Beaker 27.4, then local branches
to work on bug fixes should be created with a command like::

    git checkout origin/release-27 -b bz123456_fix_this_bug

Including the bug number in the branch name isn't required (since the branch
name is never published to anyone else), but it's a useful reference point
when working on multiple patches in parallel.


Develop against a production database snapshot
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If possible, load a large, realistic database dump into your local Beaker
database before you start writing any code, rather than starting from an empty
database. This will give you a more realistic picture of how your code will
perform in real world conditions.

Beaker core developers can use a database dump from Red Hat's production Beaker
instance for their development, which will help to reveal potential problems in
your patch when it has to run against tables containing tens or hundreds of
millions of rows.

After you have loaded and migrated a database snapshot, perform the following
steps to safeguard data and avoid interfering with production systems:

#. Set all user passwords to ``NULL``::

     UPDATE tg_user SET password = NULL;

#. Set the power settings stored in the ``power`` table to ``NULL``::

     UPDATE power SET power_address = NULL, power_user = NULL, power_passwd = NULL;

#. In order to log in, set the password for a user. For example::

     UPDATE tg_user SET password = sha("admin") WHERE user_name = "admin";

Testing your patch
~~~~~~~~~~~~~~~~~~

While working on your patch, you can run individual test cases as you add or
update them. For example::

    cd IntegrationTests/
    ./run-tests.sh -sv bkr.inttest.server.selenium.test_recipes:RecipeHTTPTest.test_anonymous_cannot_update_recipe

Once your code is ready, you can run all the tests for a particular page,
command, or area of functionality to check for unintended side effects::

    ./run-tests.sh -v bkr.inttest.server.selenium.test_recipes

You can also run the complete test suite (but bear in mind that it takes over
2 hours to run, and Jenkins will do this for you once your patch is posted to
GitHub)::

    ./run-tests.sh

The ``run-tests.sh`` script is a thin wrapper around
`nosetests <http://readthedocs.org/docs/nose/>`_ which sets up
``PYTHONPATH`` for running from a git checkout.

Once you've verified the patch passes the test suite, commit it to your
local branch::

    git commit

You can also run the Beaker test suite in Beaker (assuming you have
access to a working Beaker instance) using the
``/distribution/beaker/dogfood`` task. If your Beaker instance doesn't
already have a copy of this task, you can build it from Beaker's source
under the ``Tasks`` subdirectory. You can base your job on this `sample
dogfood job XML <../../sample-dogfood-job.xml>`_.

Building RPMs
~~~~~~~~~~~~~

You can use ``Misc/rpmbuild.sh`` to build Beaker RPMs for testing::

    Misc/rpmbuild.sh -bb

or if you have Mock or Koji suitably configured, you can generate an
SRPM and build from that::

    Misc/rpmbuild.sh -bs

You may first need to install all the build dependencies using
``yum-builddep beaker.spec``. The ``Misc/rpmbuild.sh`` script will
build from the HEAD commit in git, so make sure you have committed
your changes to your local branch.

If the patch changes an existing feature or adds a new one,
then ideally the relevant documentation should be updated. Also note that
linking the documented feature to the release notes, and/or using the
'versionchanged/versionadded' directive where appropriate is encouraged.

Submitting your patch
~~~~~~~~~~~~~~~~~~~~~

The Beaker project uses the `GitHub <http://www.github.com>`_.
All patches are reviewed in GitHub before being merged.

You can read official GitHub documentation for `submitting patches
<https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/proposing-changes-to-your-work-with-pull-requests>`__.


Exceptions to the review process
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Core developers are permitted to bypass the review process by setting the
review on their own patches in at least the following circumstances:

- when minor fixes have been made to a previously approved documentation
  patch (documentation patch reviews are mainly aimed at overall structure
  and picking up omissions and technical errors. Fixing a typo or grammar
  error doesn't require restarting the entire review process)
- updating the git submodules for the beaker-project.org documentation (this
  may be pushed directly to git, bypassing GitHub entirely)
- design proposal updates (design proposals should generally be discussed on
  the development mailing list rather than in a GitHub review, although the
  latter can be useful for line-by-line commenting on specific details)
- technical road map updates (the overall technical road map is only updated
  by, or at the direction of, the Beaker Development Lead, rather than using
  the regular change review process)
- any changes to the beaker-administrivia repo (these scripts are just used
  to help with issue management and status tracking, and don't directly
  impact the actual functional code, tests or documentation)

As other exceptional cases are identified, they will also be noted here.
