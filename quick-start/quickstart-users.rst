Quickstart for users
====================

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

This guide aims to quickly enable new users to get up and running on 
ARCHER2. It covers the process of getting an ARCHER2 account, logging in
and running your first job.

Request an account on ARCHER2
-----------------------------

.. warning::

  You need to use both a password and a passphrase-protected SSH key pair to log into 
  ARCHER2. You get the password from SAFE, but, you will also need to setup your own
  SSH key pair and add the public part to your account via SAFE before you will be able
  to log in. We cover the authentication steps below.

Obtain an account on the SAFE website
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The first step is to sign up for an account on the ARCHER2 SAFE website. The SAFE account
is used to manage all of your login accounts, allowing you to report on your usage and quotas.
To do this:

1. Go to the `SAFE New User Signup Form <https://safe.epcc.ed.ac.uk/signup.jsp>`__
2. Fill in your personal details.  You can come back later and change them if you wish
3. Click *Submit*

You are now registered. Your SAFE password will be emailed to the email address you provided.
You can then login  with that email address and password. (You can change your initial
SAFE password whenever you want by selecting the `Change SAFE password` option from the
`Your details` menu.)

Generating and adding an SSH key pair
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

How you generate your SSH key pair depends on which operating system you use and which 
SSH client you use to connect to ARCHER2. We will not cover the details on generating an
SSH key pair here, but detailed information on generating an SSH key pair is available
in the `ARCHER2 User and Best Practice Guide <https://docs.archer2.ac.uk/user-guide/connecting.html>`__.

Once you have generated your SSH key pair, you should add the public part to your
login account using SAFE:

1. `Log into SAFE <https://safe.epcc.ed.ac.uk>`__
2. Use the menu *Your details* and select *Update personal details*
3. Either copy and paste the public part of your SSH key into the SSH Public key box or use the
   button to select the public key file on your computer.
4. Click *Update* to associate the public SSH key part with your SAFE account

Request an ARCHER2 login account
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Once you have a SAFE account and an SSH key you will need to request a user account on ARCHER2 itself.
To do this you will require a *Project Code*; you usually obtain this from the Principle
Investigator (PI) or project manager for the project you will be working on. Once you have
the Project Code:

1. `Log into SAFE <https://safe.epcc.ed.ac.uk>`__
2. Use the *Login accounts - Request new account* menu item
3. Select the correct project from the drop down list
4. Select the *ARCHER2* machine in the list of available machines
5. Click *Next*
6. Enter a username for the account
7. Click *Request* 

The PI or project manager of the project will be asked to approve your request. After your
request has been approved the account will be created and when this has been done you will
receive an email. You can then come back to SAFE and pick up the initial single-use password
for your new account.

.. note::

  ARCHER2 account passwords are also sometimes referred to as LDAP passwords by the system.


Generating and adding an SSH key pair
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

How you generate your SSH key pair depends on which operating system you use and which 
SSH client you use to connect to ARCHER2. We will not cover the details on generating an
SSH key pair here, but detailed information on this topic is available in the `ARCHER2 User
and Best Practice Guide <https://docs.archer2.ac.uk/user-guide/connecting.html>`__.

After generating your SSH key pair, add the public part to your login account using SAFE:

1. `Log into SAFE <https://safe.epcc.ed.ac.uk>`__
2. Use the menu *Login accounts* and select the ARCHER2 account to be associated with the SSH key
3. On the subsequent Login account details page, click the *Add Credential* button
4. Select *SSH public key* as the Credential Type and click *Next*
5. Either copy and paste the public part of your SSH key into the SSH Public key box or use the
   button to select the public key file on your computer
6. Click *Add* to associate the public SSH key part with your account

Once you have done this, your SSH key will be added to your ARCHER2 account.

Remember, you will need to use both an SSH key and password to log into ARCHER2 so you will also
need to collect your initial password before you can log into ARCHER2 for the first time.
We cover this next.

Collecting your ARCHER2 password
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You should now collect your ARCHER2 password:

1. `Log into SAFE <https://safe.epcc.ed.ac.uk>`__
2. Use the *Login accounts* menu to select your new login account
3. Use the *View Login Account Password* button to view your single-use ARCHER2 password

This password is generated randomly by the software. It's best to copy-and-paste it across
when you log in to ARCHER2. After you login, you will immediately be prompted to begin the process
of changing your password. You should now enter the initial password again, and then you will be
prompted for your new, easy-to-remember password. Your new password should conform to the
`ARCHER2 Password Policy <https://www.archer2.ac.uk/about/policies/passwords_usernames.html>`__.

.. note::

  The *View Login Account Password* option within SAFE will continue to display your old initial
  password. Your SAFE account has no knowledge of your new machine account password.

Login to ARCHER2
----------------

To log into ARCHER2 you should use the ``login.archer2.ac.uk`` address:

:: 

   ssh [userID]@login.archer2.ac.uk

You will first be prompted for the passphrase associated with your
SSH key pair. Once you have entered your passphrase successfully, you
will then be prompted for your machine account password. You need to
enter both credentials correctly to be able to access ARCHER2.

.. note::

  If your SSH key pair is not stored in the default location (usually
  ``~/.ssh/id_rsa``) on your local system, you may need to specify the
  path to the private part of the key wih the ``-i`` option to ``ssh``.
  For example, if your key is in a file called ``keys/id_rsa_archer2``
  you would use the command
  ``ssh -i keys/id_rsa_archer2 username@login.archer2.ac.uk``
  to log in.

.. note::

  When you first log into ARCHER2, you will be prompted to change your
  initial password. This is a three step process:
  
  1. When prompted to enter your *ldap password*: re-enter the password you retrieved from SAFE
  2. When prompted to enter your new password: type in a new password
  3. When prompted to re-enter the new password: re-enter the new password
  
  Your password has now been changed.

.. seealso::

  More information on connecting to ARCHER2 is available in :doc:`../user-guide/connecting`.

File systems and manipulating data
----------------------------------

ARCHER2 has a number of different file systems and understanding the difference between them is crucial
to being able to use the system. In particular, transferring and moving data often requires a bit of
thought in advance to ensure that the data is secure and in a useful form.

ARCHER2 file systems are:

* **/home**: backed up for disaster recovery purposes only, data recovery for accidental deletion is not
  supported. NFS is available on login and service nodes.
* **/work**: not backed-up. Lustre is available on login, service and compute nodes.

.. TODO: Need to add the solid state storage to this

Top tips for managing data on ARCHER2:

* Do not generate huge numbers of files (>1000) in a single directory.
* Poor performance relating to file transfer is often due to the number of files involved in the
  transfer - minimise the number of files that you have to transfer by using archiving tools to improve
  performance.
* Archive directories or large numbers of files before moving them between file systems (e.g. by using
  commands like ``tar`` or ``zip``).
* When using ``tar`` or ``rsync`` between file systems mounted on ARCHER2 avoid the use of compression
  options as these can slow performance (time saved by transferring smaller compressed files is usually less
  than the overhead added by having to compress files on the fly).
* Think about automating the merging and transfer of multiple files output by software on ARCHER2 to
  other resources. The Data Management Guide linked below provides examples of how to automatically
  verify the integrity of an archive.

.. seealso::

  Information on best practice in managing you data is available here, 
  :doc:`../user-guide/data`.

Accessing software
------------------

Software on ARCHER2 is principally accessed through environment modules. These
load and unload the desired compilers, tools and libraries through the
``module`` command and its subcommands. Some modules will be loaded by default
on login, providing a default working environment; many more will be available
for use but initially unloaded, allowing you to set up the environment to suit
your needs.

At any stage you can check which modules have been loaded by running

::

  module list

Running the following command will display all environment modules available on
ARCHER2, whether loaded or unloaded

::

  module avail

The search field for this command may be narrowed by providing the first few
characters of the module name being queried. For example, all available versions
and variants of VASP may be found by running

::

  module avail vasp

You will see that different versions are available for many modules. For
example, ``vasp/5/5.4.4`` and ``vasp/6/6.1.0`` are two available versions of
VASP. Furthermore, a default version may be specified; this is used if no
version is provided by the user.

.. note::

  VASP is licensed software, as are other software packages on ARCHER2. You must
  have a valid licence to use licensed software on ARCHER2. Often you will need to
  request access through the SAFE. More on this below.

The ``module load`` and ``module add`` commands perform the same action, loading
a module for use. Following the above,

::

  module load vasp/5

would load the default version of VASP 5, while

::

  module load vasp/5/5.4.4

would specifically load version ``5.4.4``. A loaded module may be unloaded through
the identical ``module unload``, ``module remove`` or ``module delete``
commands, e.g.

::

  module unload vasp

The above unloads whichever version of VASP is currently in the environment.
Rather than issuing separate unload and load commands, versions of a module may
be swapped as follows:

::

  module swap vasp vasp/5/5.4.4

Other helpful commands are:

* ``module help <modulename>`` which provides a short description of the module
* ``module show <modulename>`` which displays the contents of the modulefile

Points to be aware of include:

* Some modules will conflict with others. A simple example would be the conflict
  arising when trying to load a different version of an already loaded module.
  When a conflict occurs, the loading process will fail and an error message
  will be displayed. Examination of the message and the module output (via
  ``module show``) should reveal the cause of the conflict and how to resolve
  it.
* The order in which modules are loaded *can* matter. Consider two modules
  which set the same variable to a different value. The final value
  would be that set by the module which loaded last. If you suspect that two
  modules may be interfering with one another, you can examine their contents
  with ``module show``.

Requesting access to licensed software
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Some of the software installed on ARCHER2 requires a user to have a valid licence agreed with the 
software owners/developers to be able to use it (for example, VASP). Although you will be able to
load this software on ARCHER2, you will be barred from actually using it until your licence has been
verified.

You request access to licensed software through the EPCC SAFE (the web administration tool you used
to apply for your account and retrieve your initial password) by being added to the appropriate
*Package Group*. To request access to licensed software:

1. Log in to `SAFE <https://safe.epcc.ed.ac.uk>`__
2. Go to the Menu *Login accounts* and select the login account which requires access to the software
3. Click *New Package Group Request*
4. Select the software from the list of available packages and click *Select Package Group*
5. Fill in as much information as possible about your license; at the very least provide the information
   requested at the top of the screen such as the licence holder's name and contact details. If you are
   covered by the license because the licence holder is your supervisor, for example, please state this.
6. Click *Submit*

Your request will then be processed by the ARCHER2 Service Desk who will confirm your license with the
software owners/developers before enabling your access to the software on ARCHER2. This can take several
days (depending on how quickly the software owners/developers take to respond) but you will be advised
once this has been done.

Create a job submission script
------------------------------

To run a program on the ARCHER2 compute nodes you need to write a job submission script that tells the
system how many compute nodes you want to reserve and for how long. You also need to use the ``srun``
command to launch your parallel executable.

.. seealso::

  For a more details on the Slurm scheduler on ARCHER2 and writing job submission scripts see the
  :doc:`../user-guide/scheduler` section of the User and Best Practice Guide.

.. warning::

   Parallel jobs on ARCHER2 should be run from the /work file system as /home is not available on the
   compute nodes - you will see a ``chdir`` or *file not found* error if you try to run a job from the /home file system.

Create a job submission script called ``submit.slurm`` in your space on the work file system using your
favourite text editor. For example, using ``vim``:

::

  auser@uan01:~> cd /work/t01/t01/auser
  auser@uan01:/work/t01/t01/auser> vim submit.slurm

.. note::
  
  You will need to use your project code and username to get to the correct directory.
  i.e. replace the `t01` above with your project code and replace the username `auser` with your ARCHER2 username.

Paste the following text into your job submission script, replacing ``ENTER_YOUR_BUDGET_CODE_HERE`` with
your budget code e.g. ``e99-ham``, ``ENTER_PARTITION_HERE`` with the partition you wish to run on (e.g 
``standard``), and ``ENTER_QOS_HERE`` with the quality of service you want (e.g. ``standard``).

::

  #!/bin/bash --login

  #SBATCH --job-name=test_job
  #SBATCH --nodes=1
  #SBATCH --tasks-per-node=128
  #SBATCH --cpus-per-task=1
  #SBATCH --time=0:5:0
  #SBATCH --account=ENTER_YOUR_BUDGET_CODE_HERE
  #SBATCH --partition=ENTER_PARTITION_HERE
  #SBATCH --qos=ENTER_QOS_HERE

  # Load the xthi module to get access to the xthi program
  module load xthi

  # srun launches the parallel program based on the SBATCH options
  srun --cpu-bind=cores xthi

Submit your job to the queue
----------------------------

You submit your job to the queues using the ``sbatch`` command:

::

  auser@uan01:/work/t01/t01/auser> sbatch submit.slurm
  Submitted batch job 23996
  
  The value returned is your *Job ID*.

Monitoring your job
-------------------

You use the ``squeue`` command to examine jobs in the queue. Use:

::

  auser@uan01:/work/t01/t01/auser> squeue -u $USER

To list all the jobs **you** have in the queue. ``squeue`` on its own lists all jobs
in the queue from all users.

Checking the output from the job
--------------------------------

The job submission script above should write the output to a file called ``slurm-<jobID>.out``
(i.e. if the Job ID was 23996, the file would be ``slurm-23996.out``), you can check the contents
of this file with the ``cat`` command. If the job was successful you should see output that looks
something like:

:: 

  auser@eslogin01:/work/t01/t01/auser> cat slurm-23996.out
  Hello from rank 20, thread 0, on nid00001. (core affinity = 20)
  Hello from rank 27, thread 0, on nid00001. (core affinity = 27)
  Hello from rank 23, thread 0, on nid00001. (core affinity = 23)
  Hello from rank 34, thread 0, on nid00001. (core affinity = 34)
  Hello from rank 18, thread 0, on nid00001. (core affinity = 18)
  Hello from rank 33, thread 0, on nid00001. (core affinity = 33)
  Hello from rank 19, thread 0, on nid00001. (core affinity = 19)
  Hello from rank 22, thread 0, on nid00001. (core affinity = 22)
  Hello from rank 6, thread 0, on nid00001. (core affinity = 6)
  Hello from rank 26, thread 0, on nid00001. (core affinity = 26)
  Hello from rank 31, thread 0, on nid00001. (core affinity = 31)
  Hello from rank 21, thread 0, on nid00001. (core affinity = 21)
  Hello from rank 35, thread 0, on nid00001. (core affinity = 35)
  Hello from rank 32, thread 0, on nid00001. (core affinity = 32)
  Hello from rank 28, thread 0, on nid00001. (core affinity = 28)
  Hello from rank 25, thread 0, on nid00001. (core affinity = 25)
  Hello from rank 24, thread 0, on nid00001. (core affinity = 24)
  Hello from rank 30, thread 0, on nid00001. (core affinity = 30)
  Hello from rank 29, thread 0, on nid00001. (core affinity = 29)
  Hello from rank 10, thread 0, on nid00001. (core affinity = 10)
  Hello from rank 2, thread 0, on nid00001. (core affinity = 2)
  Hello from rank 11, thread 0, on nid00001. (core affinity = 11)
  Hello from rank 0, thread 0, on nid00001. (core affinity = 0)
  Hello from rank 1, thread 0, on nid00001. (core affinity = 1)
  Hello from rank 7, thread 0, on nid00001. (core affinity = 7)
  Hello from rank 4, thread 0, on nid00001. (core affinity = 4)
  Hello from rank 3, thread 0, on nid00001. (core affinity = 3)
  Hello from rank 5, thread 0, on nid00001. (core affinity = 5)
  Hello from rank 8, thread 0, on nid00001. (core affinity = 8)
  Hello from rank 9, thread 0, on nid00001. (core affinity = 9)
  Hello from rank 12, thread 0, on nid00001. (core affinity = 12)
  Hello from rank 13, thread 0, on nid00001. (core affinity = 13)
  Hello from rank 14, thread 0, on nid00001. (core affinity = 14)
  Hello from rank 15, thread 0, on nid00001. (core affinity = 15)
  Hello from rank 16, thread 0, on nid00001. (core affinity = 16)
  Hello from rank 17, thread 0, on nid00001. (core affinity = 17)
  ... output trimmed ...

If something has gone wrong, you will find any error messages in the file instead of the
expected output.

Acknowledging ARCHER2
---------------------

.. TODO: Update with DOI for ARCHER2, once we have it

You should use the following phrase to acknowledge ARCHER2 for all research outputs that were
generated using the ARCHER2 service:

::

  This work used the ARCHER2 UK National Supercomputing Service (https://www.archer2.ac.uk).

You should also tag outputs with the keyword ARCHER2 whenever possible.

Useful Links
------------

If you plan to compile your own programs on ARCHER2, you may also want to look at
:doc:`quickstart-developers`.

Other documentation you may find useful:

* :doc:`ARCHER2 User and Best Practice Guide <../user-guide/overview>` - Covers all aspects of use of the ARCHER2 service. This includes fundamentals (required by all users to use the system effectively), best practice for getting the most out of ARCHER2, and more advanced technical topics.

* `Cray Programming Environment User Guide <https://pubs.cray.com/content/S-2529/17.05/xctm-series-programming-environment-user-guide-1705-s-2529/introduction>`__

