Connecting to ARCHER2
=====================

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

On the ARCHER2 system interactive access can be achieved via SSH, either
directly from a command line terminal or using an SSH client. In
addition data can be transferred to and from the ARCHER2 system using
``scp`` from the command line or by using a file transfer client

.. seealso:: 

   see the :doc:`data` chapter of the User Guide for more information
   on transferring data.

This section covers the basic connection methods. The connection
procedure is then expanded on and the use of SSH agent is described for
ease of access.

Interactive access
------------------

To log into ARCHER2 you should use the "login.archer2.ac.uk" address:

::

    ssh [userID]@login.archer2.ac.uk

(where you replace ``[userID]`` with your ARCHER2 user name).

Initial passwords
~~~~~~~~~~~~~~~~~

.. TODO Update link to SAFE documentation

The SAFE web interface is used to provide your initial password for
logging onto ARCHER2 (see the ARCHER2 SAFE Documentation
for more details on requesting accounts and picking up passwords).

When you log into ARCHER2 for the first time you will be asked to
change your password. Once you have logged in, the password change 
sequence is:

.. TODO Add link to ARCHER2 password policy

1. Enter your **current** password (this is your one-shot password
   from the SAFE.
2. Enter a new password that conforms with the ARCHER2 password 
   policy.
3. Re-enter the same new password.
4. You will be logged out and can now log back in with your new
   password.

**Note:** you may now change your password on the ARCHER2 machine itself
using the *passwd* command. This change will not be reflected in the
SAFE. If you forget your password, you should use the SAFE to request a
new one-shot password.

SSH Clients
-----------

Interaction with ARCHER2 is done remotely, over an encrypted
communication channel, Secure Shell version 2 (SSH-2). This allows
command-line access to one of the login nodes of a ARCHER2, from which
you can run commands or use a command-line text editor to edit files.
SSH can also be used to run graphical programs such as GUI text editors
and debuggers when used in conjunction with an X client.

Logging in from Linux and macOS
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Linux distributions and macOS each come installed with a terminal
application that can be use for SSH access to the login nodes. Linux
users will have different terminals depending on their distribution and
window manager (e.g. GNOME Terminal in GNOME, Konsole in KDE). Consult
your Linux distribution's documentation for details on how to load a
terminal.

macOS users can use the Terminal application, located in the Utilities
folder within the Applications folder.

You can use the following command from the terminal window to login into
ARCHER2:

::

    ssh [userID]@login.archer2.ac.uk

To allow remote programs, especially graphical applications to control
your local display, such as being able to open up a new GUI window (such
as for a debugger), use:

::

    ssh -X username@login.ARCHER2.ac.uk 

Some sites recommend using the ``-Y`` flag. While this can fix some
compatibility issues, the ``-X`` flag is more secure.

Current macOS systems do not have an X window system installed by default.
Users should install the XQuartz package to allow for SSH with X11
forwarding on macOS systems:

* `XQuartz website <http://www.xquartz.org/>`__

Logging in from Windows using MobaXterm
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. TODO Do we need to add Windows bash instructions?

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
during the installation process.

If you would like to run any small remote GUI applications, then make
sure to use ``-X`` option along with the ssh command (see above) to enable
X11 forwarding, which allows you to run graphical clients on your local
X server.

Making access more convenient using a SSH Agent
-----------------------------------------------

Using a SSH Agent makes accessing the resources more convenient as you
only have to enter your passphrase once per day to access any remote
resource - this can include accessing resources via a chain of SSH
sessions.

This approach combines the security of having a passphrase to access
remote resources with the convenience of having password-less access.
Having this sort of access set up makes it extremely convenient to use
client applications to access remote resources, for example:

-  the `Tramp <http://www.gnu.org/software/tramp/>`__ Emacs plugin that
   allows you to access and edit files on a remote host as if they are
   local files;
-  the `Parallel Tools Platform <http://www.eclipse.org/ptp/>`__ for the
   Eclipse IDE that allows you to edit your source code on a local
   Eclipse installation and compile and test on a remote host;

.. TODO check about agents and MobaXterm

**Note:** this description applies if your local machine is Linux or macOS.
The procedure can also be used on Windows using the PuTTY SSH
terminal with the PuTTYgen key pair generation tool and the Pageant SSH
Agent. See the `PuTTY
documentation <http://the.earth.li/~sgtatham/putty/0.62/htmldoc/>`__ for
more information on how to use these tools.

**Note:** not all remote hosts allow connections using a SSH key pair.
If you find this method does not work it is worth checking with the
remote site that such connections are allowed.

Setup a SSH key pair protected by a passphrase
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Using a terminal (the command line), set up a key pair that contains
your e-mail address and enter a passphrase you will use to unlock the
key:

::

    ssh-keygen -t rsa -C "your@email.com"
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

(remember to replace ``your@email.com`` with your e-mail address).

Copy the public part of the key to the remote host
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Using you normal login password, add the public part of your key pair to
the ``authorized\_keys`` file on the remote host you wish to connect to
using the SSH Agent. This can be achieved by appending the contents of
the public part of the key to the remote file:

::

    -bash-4.1$ cat ~/.ssh/id_rsa.pub | ssh user@login.archer2.ac.uk 'cat - >> ~/.ssh/authorized_keys'
    Password: [Password]

(remember to replace ``user`` with your username).

Now you can test that your key pair is working correctly by attempting
to connect to the remote host and run a command. You should be asked
for your key pair *passphase* (which you entered when you created the
key pair) rather than your remote machine *password*.

::

    -bash-4.1$ ssh user@login.archer2.ac.uk 'date'
    Enter passphrase for key '/Home/user/.ssh/id_rsa': [Passphrase]
    Wed May  8 10:36:47 BST 2013

(remember to replace ``user`` with your username).

Enabling the SSH Agent
~~~~~~~~~~~~~~~~~~~~~~

So far we have just replaced the need to enter a password to access a
remote host with the need to enter a key pair passphrase. The next step
is to enable an SSH Agent on your local system so that you only have to
enter the passphrase once per day and after that you will be able to
access the remote system without entering the passphrase.

Most modern Linux distributions (and macOS) should have ssh-agent
running by default. If your system does not then you should find the
instructions for enabling it in your distribution using Google.

To add the private part of your key pair to the SSH Agent, use the
'ssh-add' command (on your local machine), you will need to enter your
passphrase one more time:

::

    -bash-4.1$ ssh-add ~/.ssh/id_rsa
    Enter passphrase for Home/user.ssh/id_rsa: [Passphrase]
    Identity added: Home/user.ssh/id_rsa (Home/user.ssh/id_rsa)

Now you can test that you can access the remote host without needing to
enter your passphrase:

::

    -bash-4.1$ ssh user@login.ARCHER2.ac.uk 'date'
    Warning: Permanently added the RSA host key for IP address '192.62.216.27' to the list of known hosts.
    Wed May  8 10:42:55 BST 2013

(remember to replace ``user`` with your username).

Adding access to other remote machines
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you have more than one remote host that you access regularly, you can
add the public part of your key pair to the 'authorized\_keys'
file on any hosts you wish to access by repeating step 2 above.

SSH Agent forwarding
~~~~~~~~~~~~~~~~~~~~

Now that you have enabled an SSH Agent to access remote resources you
can perform an additional configuration step that will allow you to
access all hosts that have your public key part uploaded from any host
you connect to with the SSH Agent without the need to install the
private part of the key pair anywhere except your local machine.

This increases the security of the key pair as the private part is only
stored in one place (your local machine) and makes access more
convenient (as you only need to enter your passphrase once on your local
machine to enable access between all machines that have the public part
of the key pair).

Forwarding is controlled by a configuration file located on your local
machine at ``.ssh/config``. Each remote site (or group of sites) can have
an entry in this file which may look something like:

::

    Host archer2
      HostName login.ARCHER2.ac.uk
      User user
      ForwardAgent yes

(remember to replace `user` with your username).

The ``Host archer2`` line defines a short name for the entry. In this case,
instead of typing ``ssh login.archer2.ac.uk`` to access the ARCHER2 login
nodes, you could use ``ssh archer2`` instead. The remaining lines define
the options for the ``archer2`` host.

-  ``Hostname login.archer2.ac.uk`` - defines the full address of the
   host
-  ``User username`` - defines the username to use by default for this
   host (replace "username" with your own username on the remote host)
-  ``ForwardAgent yes`` - tells SSH to forward the local SSH Agent to
   the remote host, this is the option that allows you to store the
   private part of your key on your local machine only and export the
   access to remote sites

Now you can use SSH to access ARCHER2 without needing to enter my
username or the full hostname every time:

.. TODO replace time below with something appropriate for ARCHER2

::

    -bash-4.1$ ssh archer2 'date'
    Tue Dec 20 16:48:32 GMT 2016

You can set up as many of these entries as you need in your local
configuration file. Other options are available. See the `ssh_config
man page <http://linux.die.net/man/5/ssh_config>`__ (or ``man
ssh_config`` on any machine with SSH installed) for a description of the
SSH configuration file.

