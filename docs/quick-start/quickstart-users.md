# Quickstart for users

This guide aims to quickly enable new users to get up and running on
ARCHER2. It covers the process of getting an ARCHER2 account, logging in
and running your first job.

!!! important
    This guide covers both the ARCHER2 4-cabinet system and the
    ARCHER2 full system. Please ensure you follow the instructions
    for the correct system.

## Request an account on ARCHER2

!!! important
    You need to use both a password and a passphrase-protected SSH key pair
    to log into ARCHER2. You get the password from SAFE, but, you will also
    need to setup your own SSH key pair and add the public part to your
    account via SAFE before you will be able to log in. We cover the
    authentication steps below.

### Obtain an account on the SAFE website

The first step is to sign up for an account on the ARCHER2 SAFE website.
The SAFE account is used to manage all of your login accounts, allowing
you to report on your usage and quotas. To do this:

1.  Go to the [SAFE New User Signup
    Form](https://safe.epcc.ed.ac.uk/signup.jsp)
2.  Fill in your personal details. You can come back later and change
    them if you wish
3.  Click *Submit*

You are now registered. Your SAFE password will be emailed to the email
address you provided. You can then login with that email address and
password. (You can change your initial SAFE password whenever you want
by selecting the *Change SAFE password*
option from the *Your details* menu.)

### Request an ARCHER2 login account

Once you have a SAFE account and an SSH key you will need to request a
user account on ARCHER2 itself. To do this you will require a *Project
Code*; you usually obtain this from the Principle Investigator (PI) or
project manager for the project you will be working on. Once you have
the Project Code:

=== "Full system"
    1.  [Log into SAFE](https://safe.epcc.ed.ac.uk)
    2.  Use the *Login accounts - Request new account* menu item
    3.  Select the correct project from the drop down list
    4.  Select the *archer2* machine in the list of available machines
    5.  Click *Next*
    6.  Enter a username for the account and (optionally) an SSH public
        key
        1.  If you do not specify an SSH key at this stage, your default
            key will be used (if you have one). For users who had an ARCHER
            account, the default key will be your ARCHER SSH key.
        2.  You can always add an SSH key (or additional SSH keys) using
            the process described below.
    7.  Click *Request*

The PI or project manager of the project will be asked to approve your
request. After your request has been approved the account will be
created and when this has been done you will receive an email. You can
then come back to SAFE and pick up the initial single-use password for
your new account.

!!! note
    ARCHER2 account passwords are also sometimes referred to as LDAP
    passwords by the system.

### Generating and adding an SSH key pair

How you generate your SSH key pair depends on which operating system you
use and which SSH client you use to connect to ARCHER2. We will not
cover the details on generating an SSH key pair here, but detailed
information on this topic is available in the
[ARCHER2 User and Best Practice Guide](https://docs.archer2.ac.uk/user-guide/connecting).

After generating your SSH key pair, add the public part to your login
account using SAFE:

1.  [Log into SAFE](https://safe.epcc.ed.ac.uk)
2.  Use the menu *Login accounts* and select the ARCHER2 account to be
    associated with the SSH key
3.  On the subsequent Login account details page, click the *Add
    Credential* button
4.  Select *SSH public key* as the Credential Type and click *Next*
5.  Either copy and paste the public part of your SSH key into the SSH
    Public key box or use the button to select the public key file on
    your computer
6.  Click *Add* to associate the public SSH key part with your account

Once you have done this, your SSH key will be added to your ARCHER2
account.

Remember, you will need to use both an SSH key and password to log into
ARCHER2 so you will also need to collect your initial password before
you can log into ARCHER2 for the first time. We cover this next.

!!! note
    If you want to connect to ARCHER2 from more than one machine, e.g. from your home laptop as well as your work laptop, you should generate an ssh key on each machine, and add each of the public keys into SAFE.  

### Collecting your ARCHER2 password

You should now collect your ARCHER2 password:

1.  [Log into SAFE](https://safe.epcc.ed.ac.uk)
2.  Use the *Login accounts* menu to select your new login account
3.  Use the *View Login Account Password* button to view your single-use
    ARCHER2 password

This password is generated randomly by the software. It's best to
copy-and-paste it across when you log in to ARCHER2. After you login,
you will immediately be prompted to begin the process of changing your
password. You should now enter the initial password again, and then you
will be prompted for your new, easy-to-remember password. Your new
password should conform to the [ARCHER2 Password
Policy](https://www.archer2.ac.uk/about/policies/passwords_usernames.html).

!!! note
    The *View Login Account Password* option within SAFE will continue to
    display your old initial password. Your SAFE account has no knowledge of
    your new machine account password.

## Login to ARCHER2

To log into ARCHER2 you should use the address:

=== "Full system"
    ssh [userID]@login.archer2.ac.uk

The order in which you are asked for credentials depends on the system you
are accessing:

=== "Full system"
    You will first be prompted for the passphrase associated with your SSH key pair. Once you have entered this passphrase successfully, you will then be prompted for your machine account password. You need to enter both credentials correctly to be able to access ARCHER2.
    !!! tip
        If you previously logged into the 4-cabinet system with your account you may see an error 
        from SSH that looks like

        ```
        @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
        @       WARNING: POSSIBLE DNS SPOOFING DETECTED!          @
        @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
        The ECDSA host key for login.archer2.ac.uk has changed,
        and the key for the corresponding IP address 193.62.216.43
        has a different value. This could either mean that
        DNS SPOOFING is happening or the IP address for the host
        and its host key have changed at the same time.
        Offending key for IP in /Users/auser/.ssh/known_hosts:11
        @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
        @    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
        @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
        IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
        Someone could be eavesdropping on you right now (man-in-the-middle attack)!
        It is also possible that a host key has just been changed.
        The fingerprint for the ECDSA key sent by the remote host is
        SHA256:UGS+LA8I46LqnD58WiWNlaUFY3uD1WFr+V8RCG09fUg.
        Please contact your system administrator.
        ```

        If you see this, you should delete the offending host key from your `~/.ssh/known_hosts`
        file (in the example above the offending line is line #11)

!!! tip
    If your SSH key pair is not stored in the default location (usually
    `~/.ssh/id_rsa`) on your local system, you may need to specify the path
    to the private part of the key wih the `-i` option to `ssh`. For
    example, if your key is in a file called `keys/id_rsa_archer2` you would
    use the command `ssh -i keys/id_rsa_archer2
    username@login.archer2.ac.uk` to log in.

!!! tip
    When you first log into ARCHER2, you will be prompted to change your
    initial password. This is a three step process:

    1.  When prompted to enter your *ldap password*: re-enter the password
        you retrieved from SAFE
    2.  When prompted to enter your new password: type in a new password
    3.  When prompted to re-enter the new password: re-enter the new
        password

    Your password has now been changed.

!!! hint
    More information on connecting to ARCHER2 is available in
    the [Connecting to ARCHER2](https://docs.archer2.ac.uk/user-guide/connecting/) section of
    the User Guide.

## File systems and manipulating data

ARCHER2 has a number of different file systems and understanding the
difference between them is crucial to being able to use the system. In
particular, transferring and moving data often requires a bit of thought
in advance to ensure that the data is secure and in a useful form.

ARCHER2 file systems are:

  - **home** file systems: backed up. Available on login and data analysis nodes.
  - **work** file systems: not backed-up. Available on login, data analysis and
    compute nodes.

All users have a directory on one of the home file systems and on
one of the work file systems. The directories are located at:

- `/home/[project ID]/[project ID]/[user ID]` (this is also set as your home directory)
- `/work/[project ID]/[project ID]/[user ID]`

Top tips for managing data on ARCHER2:

  - Do not generate huge numbers of files (\>1000) in a single
    directory.
  - Poor performance relating to file transfer is often due to the
    number of files involved in the transfer - minimise the number of
    files that you have to transfer by using archiving tools to improve
    performance.
  - Archive directories or large numbers of files before moving them
    between file systems (e.g. by using commands like `tar` or `zip`).
  - When using `tar` or `rsync` between file systems mounted on ARCHER2
    avoid the use of compression options as these can slow performance
    (time saved by transferring smaller compressed files is usually less
    than the overhead added by having to compress files on the fly).
  - Think about automating the merging and transfer of multiple files
    output by software on ARCHER2 to other resources. The Data
    Management Guide linked below provides examples of how to
    automatically verify the integrity of an archive.

!!! hint
    Information on the file systems and best practice in managing you
    data is available in the
    [Data management and transfer](https://docs.archer2.ac.uk/user-guide/data/) section of the User and Best Practice Guide.

## Accessing software

Software on ARCHER2 is principally accessed through *modules*.
These load and unload the desired applications, compilers, tools and libraries through
the `module` command and its subcommands. Some modules will be loaded by
default on login, providing a default working environment; many more
will be available for use but initially unloaded, allowing you to set up
the environment to suit your needs.

At any stage you can check which modules have been loaded by running

    module list

Running the following command will display all environment modules
available on ARCHER2, whether loaded or unloaded

    module avail

The search field for this command may be narrowed by providing the first
few characters of the module name being queried. For example, all
available versions and variants of VASP may be found by running

    module avail vasp

You will see that different versions are available for many modules. For
example, `vasp/5/5.4.4.pl2` and `vasp/6/6.1.0` are two available versions of
VASP on the full system. Furthermore, a default version may be specified;
this is used if no version is provided by the user.

!!! important
    VASP is licensed software, as are other software packages on ARCHER2.
    You must have a valid licence to use licensed software on ARCHER2. Often
    you will need to request access through the SAFE. More on this below.

The `module load` command loads a module for use. Following the above,

    module load vasp/5

would load the default version of VASP 5, while

    module load vasp/5/5.4.4.pl2

would specifically load version `5.4.4.pl2`. A loaded module may be unloaded
through the identical `module remove` command, e.g.

    module unload vasp

The above unloads whichever version of VASP is currently in the
environment. Rather than issuing separate unload and load commands,
versions of a module may be swapped as follows:

    module swap vasp vasp/5/5.4.4.pl2

Other helpful commands are:

  - `module help <modulename>` which provides a short description of the
    module
  - `module show <modulename>` which displays the contents of the
    modulefile

Points to be aware of include:

  - Some modules will conflict with others. A simple example would be
    the conflict arising when trying to load a different version of an
    already loaded module. When a conflict occurs, the loading process
    will fail and an error message will be displayed. Examination of the
    message and the module output (via `module show`) should reveal the
    cause of the conflict and how to resolve it.
  - The order in which modules are loaded *can* matter. Consider two
    modules which set the same variable to a different value. The final
    value would be that set by the module which loaded last. If you
    suspect that two modules may be interfering with one another, you
    can examine their contents with `module show`.

More information on modules and the software environment on 
ARCHER2 can be found in the [Software environment](../user-guide/sw-environment.md)
section of the User and Best Practice Guide.

### Requesting access to licensed software

Some of the software installed on ARCHER2 requires a user to have a
valid licence agreed with the software owners/developers to be able to
use it (for example, VASP). Although you will be able to load this
software on ARCHER2, you will be barred from actually using it until
your licence has been verified.

You request access to licensed software through the [SAFE](http://safe.epcc.ed.ac.uk) 
(the web administration tool you used to apply for your account and
retrieve your initial password) by being added to the appropriate
*Package Group*. To request access to licensed software:

1.  Log in to [SAFE](https://safe.epcc.ed.ac.uk)
2.  Go to the Menu *Login accounts* and select the login account which
    requires access to the software
3.  Click *New Package Group Request*
4.  Select the software from the list of available packages and click
    *Select Package Group*
5.  Fill in as much information as possible about your license; at the
    very least provide the information requested at the top of the
    screen such as the licence holder's name and contact details. If you
    are covered by the license because the licence holder is your
    supervisor, for example, please state this.
6.  Click *Submit*

Your request will then be processed by the ARCHER2 Service Desk who will
confirm your license with the software owners/developers before enabling
your access to the software on ARCHER2. This can take several days
(depending on how quickly the software owners/developers take to
respond) but you will be advised once this has been done.

## Create a job submission script

To run a program on the ARCHER2 compute nodes you need to write a job
submission script that tells the system how many compute nodes you want
to reserve and for how long. You also need to use the `srun` command to
launch your parallel executable.

!!! hint
    For a more details on the Slurm scheduler on ARCHER2 and writing job
    submission scripts see the
    [Running jobs on ARCHER2](https://docs.archer2.ac.uk/user-guide/scheduler/) section of the User
    and Best Practice Guide.

!!! important
    Parallel jobs on ARCHER2 should be run from the work file systems as
    the home file systems are not available on the compute nodes - you
    will see a `chdir` or
    *file not found* error if you try to access data on the home file
    system within a parallel job running on the compute nodes.

Create a job submission script called `submit.slurm` in your space on
the work file systems using your favourite text editor. For example,
using `vim`:

    auser@ln01:~> cd /work/t01/t01/auser
    auser@ln01:/work/t01/t01/auser> vim submit.slurm

!!! tip
    You will need to use your project code and username to get to the
    correct directory. i.e. replace the `t01`
    above with your project code and replace the username
    `auser` with your ARCHER2 username.

Paste the following text into your job submission script, replacing
`ENTER_YOUR_BUDGET_CODE_HERE` with your budget code e.g. `e99-ham`,
`ENTER_PARTITION_HERE` with the partition you wish to run on (e.g
`standard`), and `ENTER_QOS_HERE` with the quality of service you want
(e.g. `standard`).


=== "Full system"
    ```
    #!/bin/bash --login
    
    #SBATCH --job-name=test_job
    #SBATCH --nodes=1
    #SBATCH --tasks-per-node=128
    #SBATCH --cpus-per-task=1
    #SBATCH --time=0:5:0
    
    # Replace [budget code] below with your project code (e.g. t01)
    #SBATCH --account=[budget code]
    #SBATCH --partition=standard
    #SBATCH --qos=standard
    
    # Load the xthi module to get access to the xthi program
    module load xthi
    
    # srun launches the parallel program based on the SBATCH options
    srun --distribution=block:block --hint=nomultithread xthi
    ```

## Submit your job to the queue

You submit your job to the queues using the `sbatch` command:

    auser@ln01:/work/t01/t01/auser> sbatch submit.slurm
    Submitted batch job 23996
    
    The value returned is your *Job ID*.

## Monitoring your job

You use the `squeue` command to examine jobs in the queue. To list all the jobs **you** have in the queue, use:

    auser@ln01:/work/t01/t01/auser> squeue -u $USER

`squeue` on its own lists all jobs in the queue from all users.

## Checking the output from the job

The job submission script above should write the output to a file called
`slurm-<jobID>.out` (i.e. if the Job ID was 23996, the file would be
`slurm-23996.out`), you can check the contents of this file with the
`cat` command. If the job was successful you should see output that
looks something like:

    auser@ln01:/work/t01/t01/auser> cat slurm-23996.out
    Node    0, hostname nid001020
    Node    0, rank    0, thread   0, (affinity =    0)
    Node    0, rank    1, thread   0, (affinity =    1)
    Node    0, rank    2, thread   0, (affinity =    2)
    Node    0, rank    3, thread   0, (affinity =    3)
    Node    0, rank    4, thread   0, (affinity =    4)
    Node    0, rank    5, thread   0, (affinity =    5)
    Node    0, rank    6, thread   0, (affinity =    6)
    Node    0, rank    7, thread   0, (affinity =    7)
    Node    0, rank    8, thread   0, (affinity =    8)
    Node    0, rank    9, thread   0, (affinity =    9)
    Node    0, rank   10, thread   0, (affinity =   10)
    Node    0, rank   11, thread   0, (affinity =   11)
    Node    0, rank   12, thread   0, (affinity =   12)
    Node    0, rank   13, thread   0, (affinity =   13)
    Node    0, rank   14, thread   0, (affinity =   14)
    Node    0, rank   15, thread   0, (affinity =   15)
    Node    0, rank   16, thread   0, (affinity =   16)
    Node    0, rank   17, thread   0, (affinity =   17)
    Node    0, rank   18, thread   0, (affinity =   18)
    Node    0, rank   19, thread   0, (affinity =   19)
    Node    0, rank   20, thread   0, (affinity =   20)
    Node    0, rank   21, thread   0, (affinity =   21)
    ... output trimmed ...

If something has gone wrong, you will find any error messages in the
file instead of the expected output.

## Acknowledging ARCHER2

You should use the following phrase to acknowledge ARCHER2 for all
research outputs that were generated using the ARCHER2 service:

> This work used the ARCHER2 UK National Supercomputing Service (https://www.archer2.ac.uk).

You should also tag outputs with the keyword "ARCHER2" whenever possible.

## Useful Links

If you plan to compile your own programs on ARCHER2, you may also want
to look at [Quickstart for developers](../quickstart-developers).

Other documentation you may find useful:

  - [ARCHER2 User and Best Practice Guide](https://docs.archer2.ac.uk/user-guide/):
    Covers all aspects of use of the ARCHER2 service. This includes
    fundamentals (required by all users to use the system effectively),
    best practice for getting the most out of ARCHER2, and more advanced
    technical topics.
  - [HPE Cray Programming Environment User
    Guide](https://internal.support.hpe.com/hpesc/public/docDisplay?docLocale=en_US&docId=a00115304en_us)
