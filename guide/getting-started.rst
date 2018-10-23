Getting started
===============

.. highlight:: console

Prerequisites
-------------

The Beaker team uses `RHEL
6 <http://www.redhat.com/products/enterprise-linux/server/>`_ for
development, testing, and deployment, therefore you should install RHEL6 or
CentOS 6 in a virtual machine before continuing with the steps described below.

Beaker repository structure
---------------------------

Beaker consists of several different components. The largest and most
critical are a set of web services that manage an inventory of hardware
systems distributed across multiple labs and take care of provisioning
systems appropriately and dispatching jobs to them.

This is the software that is developed in the `main Beaker git
repository <http://git.beaker-project.org/cgit/beaker/>`_ (click link to
browse). The bulk of this developer guide focuses on this component.

However, http://git.beaker-project.org plays host to a few other
components which are also considered part of the wider "Beaker project":

-  `beaker-project.org <http://git.beaker-project.org/cgit/beaker-project.org/>`_:
   The source for the project web site (including this developer's
   guide)
-  `beah <http://git.beaker-project.org/cgit/beah/>`_: The test harness
   used to communicate between running tests and the Beaker
   infrastructure. Other test harnesses (such as autotest or STAF) are
   not yet officially supported. Unlike the Beaker web services (which
   are only officially supported on the platforms described below), the
   test harness must run on all operating systems supported for testing.
-  `rhts <http://git.beaker-project.org/cgit/rhts/>`_: The code in this
   repo isn't part of Beaker as such, it's a collection of utilities
   designed to help with writing and running Beaker test cases.

Getting the source code
-----------------------

Start by cloning `Beaker's git
repository <http://git.beaker-project.org/cgit/beaker/>`_::

    git clone git://git.beaker-project.org/beaker
    cd beaker
    git submodule update --init

This will clone the Beaker sources and update the git submodules to keep track
of all (recent) JS libraries.

For the purposes of development, Beaker should be run on the ``develop``
branch::

    git checkout develop

This branch will become the next Beaker release. If you want to test
against the latest released version, you can use the ``master`` branch.
For older releases, use the relevant git tag.

Cloning the other repos is similar to the above, but they all develop
directly on master (as they don't have a concept of "hot fix" releases
that only include bug fixes - any release may include a combination of
both new features and bug fixes)

Installing dependencies
-----------------------

Beaker depends on some packages in the RHEL Optional repo. If you are using
RHEL, you should enable the Optional repo::


    subscription-manager repos --enable=rhel-6-server-optional-rpms

Beaker also depends on a number of packages which are not included in RHEL. These
are published on the Beaker web site in the server yum repository::

    wget -O /etc/yum.repos.d/beaker-server.repo \
        https://beaker-project.org/yum/beaker-server-RedHatEnterpriseLinux.repo

The simplest way to install all the packages you will need for developing
Beaker is to install the latest pre-built version of Beaker's test suite. Yum
will pull in all the dependencies (your working directory should be the project
root of your local clone of the main beaker project)::

    yum install beaker-integration-tests
    yum-builddep beaker.spec

Since we will be running Beaker directly from a source checkout, we can now
remove the pre-built packages. This is just to avoid any confusion between the 
two different copies of Beaker. The dependencies will remain installed.

::

    yum remove beaker-common \
        beaker-client \
        beaker-lab-controller \
        beaker-server \
        beaker-integration-tests

Creating and populating a database
----------------------------------

Beaker currently only supports MySQL with InnoDB as the database backend
(it does use SQL Alchemy internally though, so porting it to an
alternate backend shouldn't be too difficult). On RHEL and Fedora
systems, MySQL can easily be installed with::

    yum install mysql mysql-server

For Beaker development, the following settings should be added to
``/etc/my.cnf`` in the ``[mysqld]`` section before starting the MySQL
daemon::

    default-storage-engine=INNODB
    max_allowed_packet=50M
    character-set-server=utf8

Once these settings are in place, enable and start the database daemon::

    chkconfig mysqld on
    service mysqld start

Before running the development server for the first time, you must
create and populate its database::

    cd Server/
    mysql -uroot <<"EOF"
    CREATE DATABASE beaker;
    GRANT ALL ON beaker.* TO 'beaker'@'localhost' IDENTIFIED BY 'beaker';
    EOF
    PYTHONPATH=../Common:. python bkr/server/tools/init.py \
        --user=admin \
        --password=adminpassword \
        --email=me@example.com

By default this uses the ``beaker`` database on localhost. This can be
changed by editing ``dev.cfg`` and updating the above configuration
commands appropriately.

Running the Beaker web application
----------------------------------

You can then start a development server using the ``run-server.sh``
script::

    cd Server/
    ./run-server.sh

If you want to set up a complete Beaker testing environment (including a
lab controller) with the ability to provision systems and run jobs,
refer to `Beaker in a box <../../docs/in-a-box/>`_, or the more detailed 
`installation instructions <../../docs/admin-guide/installation.html>`_.

Running Lab Controller processes in a development environment is
currently not well tested.

Running Beaker's test suite
---------------------------

Beaker has a large and thorough suite of integration tests, including many
`Selenium/WebDriver <http://code.google.com/p/selenium/>`_ browser tests.
Before submitting a patch, it should be tested by running the test suite either
locally, or in Beaker, or both.

In order to run the test suite locally, you must create two additional
test databases in your local MySQL instance::

    mysql -uroot <<"EOF"
    CREATE DATABASE beaker_test;
    GRANT ALL ON beaker_test.* TO 'beaker'@'localhost' IDENTIFIED BY 'beaker';
    EOF

    mysql -uroot <<"EOF"
    CREATE DATABASE beaker_migration_test;
    GRANT ALL ON beaker_migration_test.* TO 'beaker'@'localhost' IDENTIFIED BY 'beaker';
    EOF

Once the test databases are created, you can then run the entire test suite
using ``run-tests.sh``::

    cd IntegrationTests/
    ./run-tests.sh

The ``run-tests.sh`` script is a thin wrapper around
`nosetests <http://readthedocs.org/docs/nose/>`_ which sets up ``PYTHONPATH``
for running from a git checkout.

Next steps
----------

Now that you have a working Beaker development environment, refer to the next 
section for tips about writing your patch and posting it for review.

The subsequent sections also provide important tips and guidelines about 
different aspects of developing for Beaker.
