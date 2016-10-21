Development tips
================

This is an assortment of various tips and tricks which the development team 
uses to improve or speed up their work.

Use the nose-progressive plugin
-------------------------------

The `nose-progressive <https://github.com/erikrose/nose-progressive>`_ plugin 
shows failures while the tests are still running, rather than printing them all 
at the end like stock nose. This is handy in a long-running test suite like 
Beaker's.

The ``python-nose-progressive`` package is in Fedora and EPEL.

Trade data safety for speed in a development VM
-----------------------------------------------

The Beaker test suite's performance is generally dominated by MySQL writes and 
Firefox profile creation.

If you are developing in a VM and can tolerate data loss in case the VM 
terminates unexpectedly, set ``data=writeback,barrier=0`` mount options for the 
filesystem storing :file:`/var/lib/mysql`. Also ensure that the VM's disks are 
backed by LVM volumes, not image files.

Also consider mounting a tmpfs on ``/tmp`` (although Firefox profile creation 
seems to be largely CPU-bound so this does not have much impact on overall 
speed).
