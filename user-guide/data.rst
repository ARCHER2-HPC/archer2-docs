Data Transfer Guide
===================

This section covers the different ways that you can transfer data 
on to and off Cirrus. In particular, we cover:

* Using the Cirrus Data Services Node (DSN)
* SSH-based methods (scp, sftp, rsync)
* Using the UK Research Data Facility

In all cases of data transfer, users should use the Cirrus DSN in preference to 
the Cirrus login nodes as the DSN has higher bandwidth external network connections
(10 Gb/s rather than 1 Gb/s on the login nodes).

Before you start
----------------

Read Harry Mangalam's guide on `How to transfer large amounts of data via network <http://moo.nac.uci.edu/~hjm/HOWTO_move_data.html>`_.  This tells you *all* you want to know about transferring data.

Accessing the Cirrus Data Services Node (DSN)
---------------------------------------------

You can access the Cirrus DSN using your Cirrus username at the address ``dsn.cirrus.ac.uk``:

::

   ssh username@dsn.cirrus.ac.uk

(remember to replace ``username`` with your Cirrus username).

The Cirrus DSN has high bandwidth connections (10 Gb/s) to the external network and so has the 
potential to give higher performance for data transfers.

Data Transfer via SSH
---------------------

The easiest way of transferring data to/from Cirrus is to use one of
the standard programs based on the SSH protocol such as ``scp``,
``sftp`` or ``rsync``. These all use the same underlying mechanism (ssh)
as you normally use to log-in to Cirrus. So, once the the command has
been executed via the command line, you will be prompted for your
password for the specified account on the **remote machine**.

To avoid having to type in your password multiple times you can set up a
*ssh-key* as documented in the User Guide at :doc:`connecting`

SSH Transfer Performance Considerations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ssh protocol encrypts all traffic it sends. This means that
file-transfer using ssh consumes a relatively large amount of CPU time
at both ends of the transfer. The Cirrus DSN has
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

 
For example, to transfer files to Cirrus:

::

    scp [options] source user@dsn.cirrus.ac.uk:[destination]

(Remember to replace ``user`` with your Cirrus username in the example
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

    scp [options] -c arcfour source user@dsn.cirrus.ac.uk:[destination]

(Remember to replace ``user`` with your Cirrus username in the example
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

To transfer files to Cirrus using ``rsync`` the command should have the form:

::

    rsync [options] -e ssh source user@dsn.cirrus.ac.uk:[destination]

(Remember to replace ``user`` with your Cirrus username in the example
above.)

In the above example, the ``[destination]`` is optional, as when left
out rsync will simply copy the source into the users home directory.
Also the ``source`` should be the absolute path of the file/directory
being copied or the command should be executed in the directory
containing the source file/directory.

Additional flags can be specified for the underlying ``ssh`` command by
using a quoted string as the argument of the ``-e`` flag. e.g.

::

    rsync [options] -e "ssh -c arcfour" source user@login.cirrus.ac.uk:[destination]

(Remember to replace ``user`` with your Cirrus username in the example
above.)

Using the RDF from Cirrus
-------------------------

The Cirrus DSN provides access to the RDF file system via direct mounts on a virtual
machine (VM). To access the VM with the RDF file system mounts you should log into
the DSN as normal and then use SSH to connect to the ``dsn-rdf`` VM:

::

   ssh user@dsn-rdf

(Remember to replace ``user`` with your Cirrus username in the example
above.)

Once you are on the RDF access VM, you will be able to find the RDF file systems 
mounted as:

* ``/epsrc``
* ``/nerc``
* ``/general``


The specific file system for your project's data will depend on which was allocated when the
project was setup.

.. note:: Not all projects on Cirrus have space allocated on the RDF. If you are unsure if you have space or not, please contact the `Cirrus Helpdesk <mailto:support@cirrus.ac.uk>`_

Moving data between the RDF and the Cirrus file system
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The simplest (and most efficient) way to do this is to use the ``cp`` command on the RDF access VM. For example, once you are logged onto the RDF access VM you could copy data from the RDF to the Cirrus file system with:

::

   cp /general/t01/t01/user/some_data.tar.gz /lustre/home/t01/user/

.. warning:: You should never use ``mv`` to move data between RDF file systems and Cirrus file systems (or between any two different file systems) as there is the potential to lose data. You should always copy the data, verify that the copy is not corrupted and then delete the original version.

Transferring data to/from the RDF
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you wish to transfer data to/from the RDF then you should use the RDF Data Transfer Nodes (DTNs) rather than the Cirrus DSN node. Documentation on how to transfer data to/from the RDF can be found on the RDF website:

* `RDF Data Transfer Guide <http://rdf.ac.uk/documentation/data-management/transfers.php>`__


