Maintaining the beaker-project.org infrastructure
=================================================

This document describes the configuration and upgrade procedures for the host 
running beaker-project.org, and setting up a new project on
git.beaker-project.org and gerrit.beaker-project.org.

If you just want to update the web site at http://beaker-project.org/ see its `README
<http://git.beaker-project.org/cgit/beaker-project.org/tree/README>`__.

Configuration management
------------------------

The contents of ``/etc`` are tracked using `etckeeper 
<http://joeyh.name/code/etckeeper/>`_. After making config changes, run 
``etckeeper commit`` to record them.

Remote access
-------------

Remote access is by SSH key authentication only. Speak to a Beaker developer if 
you need a user account to be created.

Lighttpd
--------

Lighttpd listens on ports 80 and 443 and serves all web traffic for 
\*.beaker-project.org.

The base config is in the standard place (``/etc/lighttpd/lighttpd.conf``). 
Config for each name-based virtual host is included from the corresponding file 
in ``/etc/lighttpd/vhosts.d/``.

Git daemon
----------

The git daemon serves read-only git repos on port 9418.

At present the Fedora package for git-daemon uses xinetd, but we are running it 
as a systemd service instead (defined in 
``/etc/systemd/system/git-daemon.service``).

Gerrit
------

Gerrit listens on port 8080 to serve its web application. This port is not open 
to the world, it is proxied by the gerrit.beaker-project.org vhost in lighttpd. 
Gerrit also listens on port 29418 for its fake SSH service.

Note that although we are running Gerrit's embedded Jetty container, we are 
*not* using Gerrit's control script ``gerrit.sh`` to start and stop the daemon 
as described in Gerrit's documentation. Instead, the Gerrit daemon is managed 
by systemd (service defined in ``/etc/systemd/system/gerrit.service``) so use 
systemctl to stop and start it.

There is no standard way to install or upgrade Java web apps so the following 
layout has been devised. Gerrit WARs are kept in ``/var/lib/gerrit``. Gerrit 
"site directories" are kept underneath that path. There is one site, named 
beaker. So the "site path" (as mentioned in the Gerrit docs) is 
``/var/lib/gerrit/beaker``. The ``etc`` directory for the Gerrit site is then 
symlinked to ``/etc/gerrit/beaker``, so that it is tracked by etckeeper.

To upgrade Gerrit, follow these steps (also check the Gerrit release notes for 
any other release-specific upgrade steps, such as reindexing)::

    cd /var/lib/gerrit
    wget https://gerrit-releases.storage.googleapis.com/gerrit-2.13.3.war
    systemctl stop gerrit
    java -jar gerrit-2.13.3.war init -d beaker
    chown -R tomcat:tomcat beaker
    systemctl start gerrit

The upgrade process first prompts for settings. You can accept all defaults -- 
the defaults are the existing configuration. Then it will perform any necessary 
schema upgrades.

SMTP
----

Postfix accepts mails for beaker-project.org on port 25 (as well as relaying 
from localhost, e.g. Gerrit notifications).

All user accounts (including root) have a mail alias in ``/etc/aliases`` 
pointing to their real address. No mail should ever be delivered to the local 
system.

Setting up a new project
------------------------

First, initialize a shared bare repository on beaker-project.org:

- SSH to beaker-project.org: ``ssh <username>@beaker-project.org``
- Init a bare git repo at :file:`/srv/git/`: ``git init --bare --shared <project_name>``
- Change the directory permissions: ``chown -R <username>:beakerdevs /srv/git/<project_name>``

Next, setup the project on gerrit:

- Login to gerrit and go to
  http://gerrit.beaker-project.org/#/admin/create-project/
- Inherit permissions from the "beaker" project
- Go to http://gerrit.beaker-project.org/#/admin/projects/<project_name>,access and setup
  "DENY" permission for  "Fedorahosted replication authgroup" and "Github replication authgroup"
- Push the new project's code to the master branch: ``git push git+ssh://<username>@gerrit.beaker-project.org:29418/<project-name> master``
