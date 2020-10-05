Connecting to ARCHER2
=======================

On the ARCHER2 system, interactive access can be achieved via SSH, either
directly from a command line terminal or using an SSH client. In
addition data can be transferred to and from the ARCHER2 system using
``scp`` from the command line or by using a file transfer client.

This section covers the basic connection methods.

Before following the process below, we assume you have setup an account on ARCHER2
through the EPCC SAFE. Documentation on how to do this can be found at:

  - `SAFE Guide for Users <https://epcced.github.io/safe-docs/safe-for-users/>`__

Access credentials
------------------

To access ARCHER2, you need to use two credentials: your password **and** an SSH
key pair protected by a passphrase. You can find more detailed instructions on
how to set up your credentials to access ARCHER2 from Windows, macOS and Linux
below.

SSH Key Pairs
~~~~~~~~~~~~~

You will need to generate an SSH key pair protected by a passphrase to access
ARCHER2.

Using a terminal (the command line), set up a key pair that contains
your e-mail address and enter a passphrase you will use to unlock the
key:

::

    $ ssh-keygen -t rsa -C "your@email.com"
    ...
    -bash-4.1$ ssh-keygen -t rsa -C "your@email.com"
    Generating public/private rsa key pair.
    Enter file in which to save the key (/Home/user/.ssh/id_rsa): [Enter]
    Enter passphrase (empty for no passphrase): [Passphrase]
    Enter same passphrase again: [Passphrase]
    Your identification has been saved in /Home/user/.ssh/id_rsa.
    Your public key has been saved in /Home/user/.ssh/id_rsa.pub.
    The key fingerprint is:
    03:d4:c4:6d:58:0a:e2:4a:f8:73:9a:e8:e3:07:16:c8 your@email.com
    The key's randomart image is:
    +--[ RSA 2048]----+
    |    . ...+o++++. |
    | . . . =o..      |
    |+ . . .......o o |
    |oE .   .         |
    |o =     .   S    |
    |.    +.+     .   |
    |.  oo            |
    |.  .             |
    | ..              |
    +-----------------+

(remember to replace "your@email.com" with your e-mail address).

Upload public part of key pair to SAFE
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You should now upload the public part of your SSH key pair to the SAFE by following the instructions at:

`Login to SAFE <https://safe.epcc.ed.ac.uk>`__. Then:

  1. Go to the Menu *Login accounts* and select the ARCHER2 account you want to add the SSH key to
  2. On the subsequent Login account details page click the *Add Credential* button
  3. Select *SSH public key* as the Credential Type and click *Next*
  4. Either copy and paste the public part of your SSH key into the *SSH Public key* box or use the button to select the public key file on your computer.
  5. Click *Add* to associate the public SSH key part with your account

Once you have done this, your SSH key will be added to your ARCHER2 account.

Remember, you will need to use both an SSH key and password to log into ARCHER2 so you will
also need to collect your initial password before you can log into ARCHER2. We cover this next.

Initial passwords
~~~~~~~~~~~~~~~~~

The SAFE web interface is used to provide your initial password for
logging onto ARCHER2 (see the `SAFE Documentation <https://epcced.github.io/safe-docs>`__
for more details on requesting accounts and picking up passwords).

**Note:** you may now change your password on the ARCHER2 machine itself
using the *passwd* command or when you are prompted the first time you login.
This change will not be reflected in the SAFE. If you forget your password,
you should use the SAFE to request a new one-shot password.

SSH Clients
-----------

Interaction with ARCHER2 is done remotely, over an encrypted
communication channel, Secure Shell version 2 (SSH-2). This allows
command-line access to one of the login nodes of a ARCHER2, from which
you can run commands or use a command-line text editor to edit files.
SSH can also be used to run graphical programs such as GUI text editors
and debuggers when used in conjunction with an X client.

Logging in from Linux and MacOS
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Linux distributions and MacOS each come installed with a terminal
application that can be use for SSH access to the login nodes. Linux
users will have different terminals depending on their distribution and
window manager (e.g. GNOME Terminal in GNOME, Konsole in KDE). Consult
your Linux distribution's documentation for details on how to load a
terminal.

MacOS users can use the Terminal application, located in the Utilities
folder within the Applications folder.

You can use the following command from the terminal window to login into
ARCHER2:

::

    ssh username@login.archer2.ac.uk

You will first be prompted for the passphrase associated with your
SSH key pair. Once you have entered your passphrase successfully, you
will then be prompted for your password. You need to enter both 
correctly to be able to access ARCHER2.

.. note::

  If your SSH key pair is not stored in the default location (usually
  ``~/.ssh/id_rsa``) on your local system, you may need to specify the
  path to the private part of the key wih the ``-i`` option to ``ssh``.
  For example, if your key is in a file called ``keys/id_rsa_ARCHER2``
  you would use the command
  ``ssh -i keys/id_rsa_ARCHER2 username@login.archer2.ac.uk``
  to log in.

.. note::

  When you first log into ARCHER2, you will be prompted to change your
  initial password. This is a three step process:
  
  1. When promoted to enter your *ldap password*: Re-enter the password you retrieved from SAFE
  2. When prompted to enter your new password: type in a new password
  3. When prompted to re-enter the new password: re-enter the new password
  
  Your password has now been changed

To allow remote programs, especially graphical applications to control
your local display, such as being able to open up a new GUI window (such
as for a debugger), use:

::

    ssh -X username@login.archer2.ac.uk

Some sites recommend using the ``-Y`` flag. While this can fix some
compatibility issues, the ``-X`` flag is more secure.

Current MacOS systems do not have an X window system. Users should
install the XQuartz package to allow for SSH with X11 forwarding on MacOS
systems:

* `XQuartz website <http://www.xquartz.org/>`__

Logging in from Windows using MobaXterm
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A typical Windows installation will not include a terminal client,
though there are various clients available. We recommend all our Windows
users to download and install MobaXterm to access ARCHER2. It is very
easy to use and includes an integrated X server with SSH client to run
any graphical applications on ARCHER2.

You can download MobaXterm Home Edition (Installer Edition) from the
following link:

* `Install MobaXterm <http://mobaxterm.mobatek.net/download-home-edition.html>`__

Double-click the downloaded Microsoft Installer file (.msi), and the
Windows wizard will automatically guides you through the installation
process. Note, you might need to have administrator rights to install on
some Windows OS. Also make sure to check whether Windows Firewall hasn't
blocked any features of this program after installation.

Start MobaXterm using, for example, the icon added to the Start menu
during the installation process. Use your ARCHER2 username, the login
host should be set to ``login.archer2.ac.uk``.

If you would like to run any small remote GUI applications, then make
sure to use -X option along with the ssh command (see above) to enable
X11 forwarding, which allows you to run graphical clients on your local
X server.

Making access more convenient using the SSH configuration file
--------------------------------------------------------------

Typing in the full command to login or transfer data to ARCHER2 can become tedious
as it often has to be repeated many times. You can use the SSH configuration file,
usually located on your local machine at ``.ssh/config`` to make things a bit more
convenient.

Each remote site (or group of sites) can have an entry in this file which may look
something like:

::

 Host archer2
   HostName login.archer2.ac.uk
   User username

(remember to replace ``username`` with your actual username!).

The ``Host archer2`` line defines a short name for the entry. In this case, instead
of typing ``ssh username@login.archer2.ac.uk`` to access the ARCHER2 login nodes,
you could use ``ssh archer2`` instead. The remaining lines define the options for the
``archer2`` host.

 - ``Hostname login.archer2.ac.uk`` - defines the full address of the host
 - ``User username`` - defines the username to use by default for this host (replace
   ``username`` with your own username on the remote host)

Now you can use SSH to access ARCHER2 without needing to enter your username or the full
hostname every time:

::

  $ ssh archer2

You can set up as many of these entries as you need in your local configuration file.
Other options are available. See the ssh_config man page (or ``man ssh_config`` on any
machine with SSH installed) for a description of the SSH configuration file. You may
find the ``IdentityFile`` option useful if you have to manage multiple SSH key pairs
for different systems as this allows you to specify which SSH key to use for each
system.

.. note::

  There is a known bug with Windows ssh-agent. If you get the error message: ``Warning: 
  agent returned different signature type ssh-rsa (expected rsa-sha2-512)``, you will
  need to either specify the path to your ssh key in the command line (using the ``-i``
  option as described above) or add the path to your SSH config file by using the
  ``IdentityFile`` option.
