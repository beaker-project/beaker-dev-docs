.. _proposal-allow-installation-user-defined-distro:

Allow installation of a user-defined distro that is not registered in Beaker
============================================================================

:Author: Anwesha Chatterjee
:Status: Not Implemented
:Release: 25

Background, rationale and use cases
-----------------------------------

Currently the only way for a user to specify a distro for provisioning in a
recipe XML is to use the ``distroRequires`` tag. This method requires that
the distro is imported into the Beaker distro library prior to submitting the
job.

This is inconvenient for the following reasons:

* Only admins can import distro records into Beaker's distro library.

* When producing several kernel builds per day, it is time consuming to have
  to register each of them to Beaker's distro library.

* The user may like to specify a distro tree url for the purpose of a temporary
  test and therefore not want to register it to the library.

Therefore for the purpose of such time and resource constraints, as well as simply
for convenience it would be nice to have the flexibility to schedule tests for a
distro that is not imported into Beaker. It would mean having the ability to bypass
the distro library completely, and run tests using user-defined distro metadata.
An example XML structure for defining the distro metadata was mentioned by Bill in
:issue:`1124474`

Proposed design: user-visible changes
-------------------------------------

When submitting a job, the ``<distroRequires/>`` element will no longer be mandatory.
Instead, the recipe may contain either a ``<distro/>`` element specifying a
user-defined distro, or ``<distroRequires/>`` for matching a distro from the distro
library as it does today. If the ``<distro/>`` element is present, it must have the
following required child elements:

``tree``
  Location of the network installation tree, containing a suitable installer and
  corresponding images and packages.
``initrd``
  Location of the initramfs image for booting the installer. This may be a path
  (relative to the base tree URL) or an absolute URL.
``kernel``
  Location of the kernel image for booting the installer. This may be a path
  (relative to the base tree URL) or an absolute URL.
``arch``
  CPU architecture that this distro is compatible with, for example ``x86_64`` or ``ppc64le``.


The ``<distro/>`` element may also have these optional child elements:

``name``
  Name of the distro, for example ``RHEL-7.5-20170918.n.0``. The name is only used for display purposes.
  It will be shown on the recipe page, and is available for searching in the recipes grid.
``osversion``
  OS version expressed as the major and minor versions of this distro.
``variant``
  Variant of the distro, for example ``Server``. This is only used for display purposes.

An example XML ``<distro/>`` element is:

.. code-block:: xml
   :linenos:

   <distro>
       <tree url="http://dummylab.example.com/distros/MyCustomLinux1.0/Server/i386/os/"/>
       <initrd url="pxeboot/initrd"/>
       <kernel url="pxeboot/vmlinuz"/>
       <arch value="i386"/>
       <name value="MyCustomLinux1.0"/>
       <osversion major="RedHatEnterpriseLinux7" minor="4" />
       <variant value="Server"/>
   </distro>


Proposed design: implementation details
---------------------------------------

The metadata provided as child elements in the ``<distro/>`` XML element will be stored
as additional columns in the ``installation`` table. Currently the ``installation``
table is created at the time of distro provisioning.

Since the metadata is provided to Beaker at the time of job submission, the installation
table will be created at the time of job submission instead. Since the data for all the
columns in the table is not available at the time of job submission, the columns in
question can be made nullable, and filled in at provisioning time.

The optional attributes in the ``<distro/>`` are also made to be nullable in the table.

When the user uses the traditional method of specifying distro information using     the
``<distroRequires/>`` XML tag, the ``distro_tree`` column will be populated as usual,
but the columns mentioned in the above table will also be populated with data parsed
from the relevant ``distro_tree`` record in the Beaker distro library.

This means that the ``distro_tree_id`` column will still exist in the ``installation``
and ``recipe`` tables, but it will now be nullable.

The areas in the code that rely on and reference ``distro_tree`` such as
``recipe.distro_tree`` and ``recipe.installation.distro_tree`` will be updated to
instead reference the parsed metadata present in the above columns added to the
``installation`` and ``recipe`` tables.


Deferred features
-----------------

It was proposed that Beaker would accept a URL for the distro tree and beaker would then
automatically retrieve and populate the metadata by parsing .treeinfo. This would be
problematic as it would involve performing a HTTP request on the provided URL, and is
perhaps best handled as a separate feature.
