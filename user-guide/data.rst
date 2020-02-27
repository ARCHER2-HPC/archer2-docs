Data management and transfer
============================

This section covers best practice and tools for data management on ARCHER2.

Useful resources and links
--------------------------

  - Harry Mangalam's guide on `How to transfer large amounts of data via network <https://hjmangalam.wordpress.com/2009/09/14/how-to-transfer-large-amounts-of-data-via-network/>`_. This provides lots of useful advice on transferring data though we now recommend using Globus Online, rather than GridFTP directly.

Data management
---------------

We strongly recommend that you give some thought to how you use the various data-storage
facilities that are part of the ARCHER2 service. This will not only allow you to use the
machine more effectively but also to ensure that your valuable data is protected.

File systems
~~~~~~~~~~~~

The ARCHER2 service, like many HPC systems has a complex structure. There are a number of
different data storage types available to users:

  - Home file systems
  - Work file systems
  - Solid state storage
  - RDF storage

Each type of storage has different characteristics and policies, and is suitable for
different types of use.

There are also many different types of node available to users:

  - Login nodes
  - Compute nodes
  - Data analysis and pre-/post-processing nodes
  - Data transfer nodes

Each type of node sees a different combination of the storage types.

Home file systems
^^^^^^^^^^^^^^^^^

There are four independent home file-systems. Every project has an allocation on one
of the four. You do not need to know which one your project uses as your projects space
can always be accessed via the path ``/home/project-code``. Each home file-system is
approximately 60TB in size and is implemented using standard Network Attached Storage
(NAS) technology. This means that these disks are not particularly high performance
but are well suited to standard operations like compilation and file editing. These
file systems are visible from the ARCHER2 login nodes and the pre-/post-processing nodes.

The home file systems **are fully backed up**. Full backups are taken weekly with
incremental backups added every day in between. Backups are kept for disaster recovery
purposes only. If you have accidentally lost data from a backed-up file-system and have
no other way of recovering the data then contact us as quickly as possible but we may
be unable to assist.

These file-systems are a good location to keep source-code, copies of scripts and
compiled binaries. Small amounts of important data can also be copied here for safe
keeping though the file systems are not fast enough to manipulate large datasets
effectively.

.. warning::

  files with filenames that contain non-ascii characters and/or non-printable characters
  cannot be backed up using our automated process and so will be omitted from all backups.

Work file systems
^^^^^^^^^^^^^^^^^

There are three independent work file-systems:

  - /fs2 1.5PB
  - /fs3 1.5PB
  - /fs4 1.8PB

Every project has an allocation on one of the three. You do not need to know which one
your project uses as your projects space can always be accessed via the path
``/work/project-code``.

These are high-performance, Lustre parallel file systems. They are designed to support
data in large files. The performance for data stored in large numbers of small files is
probably not going to be as good.

These are the only file systems that are available on the compute nodes so all data read
or written by jobs running on the compute nodes has to be hosted here.

.. warning::

  There are no backups of any data on the work file systems. You should not rely on these
  file systems for long term storage.
  
Ideally, these file systems should only contain data that is:

  - actively in use;
  - recently generated and in the process of being saved elsewhere; or
  - being made ready for up-coming work.

In practice it may be convenient to keep copies of datasets on the work file systems that
you know will be needed at a later date. However, make sure that important data is always
backed up elsewhere and that your work would not be significantly impacted if the data on
the work file systems was lost.

Large data sets can be copied to the RDF storage or transferred off the ARCHER2 service
entirely.

If you have data on the work file systems that you are not going to need in the future
please delete it.

Solid state storage
^^^^^^^^^^^^^^^^^^^

.. TODO add description of solid state storage

RDF storage
^^^^^^^^^^^

There are three archive file-systems.

/epsrc
/nerc
/general
Most projects will have an allocation on one of these file-systems. If you have a requirement to use these file-systems and your project does not have an allocation (or the allocation is insufficient) please contact the helpdesk.

The file-systems are provided by the RDF but are directly mounted by the ARCHER login nodes and serial-batch/post-processing nodes as well as the RDF data-mover nodes and analysis cluster.

The archive file-systems are parallel file-systems built using GPFS. They are intended as a safe location for large data-sets so backups to an off-site tape library are performed daily. Backups of deleted files are retained for 180 days. N.B. files with filenames that contain non-ascii characters and/or non-printable characters cannot be backed up using our automated process and so will be omitted from all backups.

As with any parallel file-system large data files are handled more efficiently than large numbers of small data files. If your data consists of a large number of related files you should consider packing them into larger archive files for long term storage. This will also make it easier to manage your data as the collection can be treated as a single object.

Understanding rdf file systems quotas
It should be noted that on the RDF the group allocations are implemented using GPFS "file-sets" this means that the quota and usage is what you expect it to be (the amount of data held within the directory tree). Files outside of these directory trees don't cound towards the group totals.

However the user quotas are still standard unix file-quotas and the usage values is the sum of all of the files owned by the user wherever they are within the /nerc, /epsrc or /general file-system. The user quotas will only sum to the group value if all of the users concerned only have access to a single file-set.

To be clear, every file counts towards both a user quota and a group quota (or a file-set in the case of the RDF file-systems) but one is a limit on the space taken by files owned by a specific user, the other is a limit on the space taken by files belonging to a group (or directory tree).

The group/file-set quota used by a project is constrained such that it can never total more than the overall disk space allcated to the project. However, there is no such limit on the user quotas. Most projects do not choose to set user quotas at all and leave all user quotas to be without limit.

We would recommend that projects EITHER use a single group and user quotas or use group quotas only to avoid confusion.

Data Transfer via SSH
---------------------

The easiest way of transferring data to/from ARCHER2 is to use one of
the standard programs based on the SSH protocol such as ``scp``,
``sftp`` or ``rsync``. These all use the same underlying mechanism (SSH)
as you normally use to log-in to ARCHER2. So, once the the command has
been executed via the command line, you will be prompted for your
password for the specified account on the **remote machine**.

To avoid having to type in your password multiple times you can set up a
*SSH key pair* and use an *SSH agent* as documented in the User Guide at
:doc:`connecting`.

SSH Transfer Performance Considerations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ssh protocol encrypts all traffic it sends. This means that
file-transfer using ssh consumes a relatively large amount of CPU time
at both ends of the transfer. The ARCHER2 DSN has
fairly fast processors that can sustain about 100 MB/s transfer.
The encryption algorithm used is negotiated between the ssh-client and the ssh-server. There are command
line flags that allow you to specify a preference for which encryption
algorithm should be used. You may be able to improve transfer speeds by
reqeusting a different algorithm than the default. The *arcfour*
algorithm is usually quite fast if both hosts support it.

A single ssh based transfer will usually not be able to saturate the
available network bandwidth or the available disk bandwidth so you may
see an overall improvement by running several data transfer operations
in parallel. To reduce metadata interactions it is a good idea to
overlap transfers of files from different directories.

In addition, you should consider the following when transferring data:

* Only transfer those files that are required. Consider which data you
  really need to keep.
* Combine lots of small files into a single *tar* archive, to reduce the
  overheads associated in initiating many separate data transfers (over
  SSH each file counts as an individual transfer).
* Compress data before sending it, e.g. using gzip.

scp command
~~~~~~~~~~~

The ``scp`` command creates a copy of a file, or if given the ``-r``
flag, a directory, on a remote machine.

 
For example, to transfer files to ARCHER2:

::

    scp [options] source user@dsn.archer2.ac.uk:[destination]

(Remember to replace ``user`` with your ARCHER2 username in the example
above.)

In the above example, the ``[destination]`` is optional, as when left
out scp will simply copy the source into the user's home directory. Also
the ``source`` should be the absolute path of the file/directory being
copied or the command should be executed in the directory containing the
source file/directory.

If you want to request a different encryption algorithm add the ``-c
[algorithm-name]`` flag to the ``scp`` options. For example, to use the
(usually faster) *arcfour* encryption algorithm you would use:

::

    scp [options] -c arcfour source user@dsn.archer2.ac.uk:[destination]

(Remember to replace ``user`` with your ARCHER2 username in the example
above.)

rsync command
~~~~~~~~~~~~~

The ``rsync`` command can also transfer data between hosts using a
``ssh`` connection. It creates a copy of a file or, if given the ``-r``
flag, a directory at the given destination, similar to scp above.

Given the ``-a`` option rsync can also make exact copies (including
permissions), this is referred to as *mirroring*. In this case the
``rsync`` command is executed with ssh to create the copy on a remote
machine.

To transfer files to ARCHER2 using ``rsync`` the command should have the form:

::

    rsync [options] -e ssh source user@dsn.archer2.ac.uk:[destination]

(Remember to replace ``user`` with your ARCHER2 username in the example
above.)

In the above example, the ``[destination]`` is optional, as when left
out rsync will simply copy the source into the users home directory.
Also the ``source`` should be the absolute path of the file/directory
being copied or the command should be executed in the directory
containing the source file/directory.

Additional flags can be specified for the underlying ``ssh`` command by
using a quoted string as the argument of the ``-e`` flag. e.g.

::

    rsync [options] -e "ssh -c arcfour" source user@login.archer2.ac.uk:[destination]

(Remember to replace ``user`` with your ARCHER2 username in the example
above.)

Using the RDF from ARCHER2
-------------------------

The ARCHER2 DSN provides access to the RDF file system via direct mounts on a virtual
machine (VM). To access the VM with the RDF file system mounts you should log into
the DSN as normal and then use SSH to connect to the ``dsn-rdf`` VM:

::

   ssh user@dsn-rdf

(Remember to replace ``user`` with your ARCHER2 username in the example
above.)

Once you are on the RDF access VM, you will be able to find the RDF file systems 
mounted as:

* ``/epsrc``
* ``/nerc``
* ``/general``


The specific file system for your project's data will depend on which was allocated when the
project was setup.

.. note:: Not all projects on ARCHER2 have space allocated on the RDF. If you are unsure if you have space or not, please contact the `ARCHER2 Helpdesk <mailto:support@archer2.ac.uk>`_

Moving data between the RDF and the ARCHER2 file system
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The simplest (and most efficient) way to do this is to use the ``cp`` command on the RDF access VM. For example, once you are logged onto the RDF access VM you could copy data from the RDF to the ARCHER2 file system with:

::

   cp /general/t01/t01/user/some_data.tar.gz /lustre/home/t01/user/

.. warning:: You should never use ``mv`` to move data between RDF file systems and ARCHER2 file systems (or between any two different file systems) as there is the potential to lose data. You should always copy the data, verify that the copy is not corrupted and then delete the original version.

Transferring data to/from the RDF
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you wish to transfer data to/from the RDF then you should use the RDF Data Transfer Nodes (DTNs) rather than the ARCHER2 DSN node. Documentation on how to transfer data to/from the RDF can be found on the RDF website:

* `RDF Data Transfer Guide <http://rdf.ac.uk/documentation/data-management/transfers.php>`__


