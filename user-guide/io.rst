I/O and file systems
====================

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

Using the ARCHER2 file systems
------------------------------
There are three filesystems available to ARCHER2 users:

Home
^^^^^^^^^^^^^^^
Not mounted on the compute nodes filesystem and backed up for disaster recovery purposes only. Data recovery for accidental deletion is not supported. Home directories provide a convenient means for a user to have access to files such as source files, input files or configuration files. The home directory for each user is located at:

::

   /home/[project code]/[group code]/[username]

where

``[project code]`` is the code for your project (e.g., x01);

``[group code]`` is the code for your project group, if your project has groups, (e.g. x01-a) or the same as the project code, if not;

``[username]`` is your login name.

Each project is allocated a portion of the total storage available, and the project PI will be able to sub-divide this quota among the groups and users within the project. As is standard practice on UNIX and Linux systems, the environment variable ``$HOME`` is automatically set to point to your home directory.

The ``/home`` filesystem is backed up for disaster recovery purposes only. Data recovery for accidental deletion is not supported.

It should be noted that the ``/home`` filesystem is not designed, and does not have the capacity, to act as a long term archive for large sets of results. 

Work
^^^^^^^^^
High-performance, Lustre filesystem mounted on the compute nodes. All parallel calculations must be run from directories on the ``/work`` filesystem and all files required by the calculation (apart from the executable) must reside on ``/work``. Each project will be assigned space on a particular Lustre partition with the assignments chosen to balance the load across the available infrastructure.

The work directory for each user is located at:

::

   /work/[project code]/[group code]/[username]


where

``[project code]`` is the code for your project (e.g., x01);

``[group code]`` is the code for your project group, if your project has groups, (e.g. x01-a) or the same as the project code, if not;

``[username]`` is your login name.

It is important to note that there is no separate backup of data on any of the work filesystems, which means that in the event of a major hardware failure, or if a user accidently deletes essential data, it will not be possible to recover the lost files.

Links from the ``/home`` filesystem to directories or files on ``/work`` are strongly discouraged. If links are used, executables and data files on ``/work`` to be used by applications on the compute nodes (i.e. those executed via the ``aprun`` command) should be referenced directly on ``/work``.


BurstBuffer
^^^^^^^^^^^^^^^
The 1.1 PiB all-flashed ARCHER2 Burst Buffer is based on the Cray DataWarp technology to significantly increase the I/O performance for all file sizes and all access patterns. Accessible only from compute nodes, the Burst Buffer provides per-job (or short-term) storage for I/O intensive codes.


-----------

Achieving efficient I/O
-----------------------
