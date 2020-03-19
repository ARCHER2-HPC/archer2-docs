I/O and file systems
====================

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

Using the ARCHER2 file systems
------------------------------
Different file systems are configured for different purposes and performance. ARCHER2 has three file systems available to users:

Home
^^^^
This file system is not mounted on the compute nodes, and backed up for disaster recovery purposes only. Data recovery for accidental deletion is not supported. Home directories provide a convenient means for a user to have access to files such as source files, input files or configuration files. The home directory for each user is located at:

::

   /home/[project code]/[group code]/[username]

where

``[project code]`` is the code for your project (e.g., x01);

``[group code]`` is the code for your project group, if your project has groups, (e.g. x01-a) or the same as the project code, if not;

``[username]`` is your login name.

Each project is allocated a portion of the total storage available, and the project PI will be able to sub-divide this quota among the groups and users within the project. As is standard practice on UNIX and Linux systems, the environment variable ``$HOME`` is automatically set to point to your home directory.

It should be noted that the home file system is not designed, and does not have the capacity, to act as a long term archive for large sets of results. 

Work
^^^^
High-performance Lustre file system mounted on the compute nodes. All parallel calculations must be run from directories on the ``/work`` file system and all files required by the calculation (apart from the executable) must reside on ``/work``. Each project will be assigned space on a particular Lustre partition with the assignments chosen to balance the load across the available infrastructure.

The work directory for each user is located at:

::

   /work/[project code]/[group code]/[username]


where

``[project code]`` is the code for your project (e.g., x01);

``[group code]`` is the code for your project group, if your project has groups, (e.g. x01-a) or the same as the project code, if not;

``[username]`` is your login name.

It is important to note that there is no separate backup of data on any of the work file systems, which means that in the event of a major hardware failure, or if a user accidently deletes essential data, it will not be possible to recover the lost files.

Links from the ``/home`` file system to directories or files on ``/work`` are strongly discouraged. If links are used, executables and data files on ``/work`` to be used by applications on the compute nodes (i.e. those executed via the ``aprun`` command) should be referenced directly on ``/work``.


BurstBuffer
^^^^^^^^^^^
The 1.1 PiB all-flashed ARCHER2 Burst Buffer is based on the Cray DataWarp technology to significantly increase the I/O performance for all file sizes and all access patterns. Accessible only from compute nodes, the Burst Buffer provides per-job (or short-term) storage for I/O intensive codes.


Disk quotas
-----------

Sharing data with other ARCHER2 users
-------------------------------------

How you share data with other ARCHER users depends on whether they belong to the same project as you or not. Each project has two levels of shared directories that can be used for sharing data.

Sharing data with users in your project
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Each project has a directory called:

::
   
   /work/[project code]/[project code]/shared

   
that has read/write permissions for all project members. You can place any data you wish to share with other project members in this directory.

For example, if your project code is x01 the shared project directory would be located at:

::

   /work/x01/x01/shared
   
Sharing data with all users
^^^^^^^^^^^^^^^^^^^^^^^^^^^
Each project also has a higher level directory called:

::

   /work/[project code]/shared
   
that is writable by all project members and readable by any user on the system. You can place any data you wish to share with other ARCHER2 users who are not members of your project in this directory.

For example, if your project code is x01 the sharing directory would be located at:

::

   /work/x01/shared
   

Achieving efficient I/O
-----------------------
This section provides information on getting the best performance out of the parallel ``/work`` file systems on ARCHER2 when writing data, particularly using parallel I/O patterns.

Lustre
^^^^^^
The ARCHER2 ``/work`` file systems use Lustre as a parallel file system technology. The Lustre file system provides POSIX semantics (changes on one node are immediately visible on other nodes) and can support very high data rates for appropriate I/O patterns.

Striping
^^^^^^^^
One of the main factors leading to the high performance of Lustre file systems is the ability to stripe data across multiple Object Storage Targets (OSTs) in a round-robin fashion. Files are striped when the data is split up in chunks that will then be stored on different OSTs across the Lustre system. Striping might improve the I/O performance because it increases the available bandwith since multiple processes can read and write the same files simultaneously. However striping can also increase the overhead. Choosing the right striping configuration is key to obtain high performance results.

Users have control of a number of striping settings on Lustre file systems. Although these parameters can be set on a per-file basis they are usually set on directory where your output files will be written so that all output files inherit the settings.

::

   lfs setstripe
   

::

   lfs getstripe
Command to query the stripe settings for a directory (or file).

Default configuration
""""""""""""""""""""""
::

   lfs getstripe res_dir
   res_dir
   stripe_count:   1 stripe_size:    1048576 stripe_offset:  -1 
   res_dir/single
   stripe_count:   1 stripe_size:    1048576 stripe_offset:  -1 
   res_dir/double
   stripe_count:   1 stripe_size:    1048576 stripe_offset:  -1
   
Setting Custom Striping Configurations
""""""""""""""""""""""""""""""""""""""
Users can set stripe settings for a directory (or file) using the ``lfs setstripe`` command. The options for ``lfs setstripe`` are:

* ``[--stripe-count|-c]`` to set the stripe count; 0 means use the system default (usually 1) and -1 means stripe over all available OSTs.
* ``[--stripe-size|-s]`` to set the stripe size; 0 means use the system default (usually 1 MB) otherwise use k, m or g for KB, MB or GB respectively
* ``[--stripe-index|-i]`` to set the OST  index (starting at 0) on which to start striping for this file.  An index of -1  allows the  MDS  to choose the starting index and it is strongly recommended, as this allows space and load balancing to  be  done  by the  MDS  as  needed. 

For example, to set a stripe size of 4 MiB for the existing directory ``res_dir``, along with maximum striping count you would use:

::

   lfs setstripe -s 4m -c -1 res_dir/

   
ARCHER2 recommended Striping Settings
"""""""""""""""""""""""""""""""""""""









