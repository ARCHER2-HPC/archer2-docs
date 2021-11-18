# Connecting to ARCHER2

This section covers the basic connection methods.

On the ARCHER2 system, interactive access is achieved using SSH, either directly from a command-line terminal or using an SSH client. In addition, data can be transferred to and from the ARCHER2 system using
`scp` from the command line or by using a file-transfer client.

Before following the process below, we assume you have set up an account on ARCHER2 through the EPCC SAFE. Documentation on how to do this can be
found at:

   - [SAFE Guide for Users](https://epcced.github.io/safe-docs/safe-for-users/)


## Command line terminal

### Linux 

Linux distributions include a terminal application that can be used for SSH access to the ARCHER2 login nodes. Linux users will have different terminals depending on their distribution and window manager (e.g., GNOME Terminal in GNOME, Konsole in KDE). Consult your Linux distribution's documentation for details on how to load a terminal.

### MacOS

MacOS users can use the Terminal application, located in the Utilities folder within the Applications folder.

### Windows

A typical Windows installation will not include a terminal client, though there are various clients available. We recommend Windows users download and install MobaXterm to access ARCHER2. It is very easy to use and includes an integrated X Server, which allows you to run graphical applications on ARCHER2.

You can download MobaXterm Home Edition (Installer Edition) from the following link:

  - [Install MobaXterm](http://mobaxterm.mobatek.net/download-home-edition.html)

Double-click the downloaded Microsoft Installer file (.msi) and follow
the instructions from the Windows Installation Wizard. Note, you might
need to have administrator rights to install on some versions of Windows. Also,
make sure to check whether Windows Firewall has blocked any
features of this program after installation (Windows will warn you if the built-in firewall blocks an action, and gives you the opportunity to override the behaviour).

Once installed, start MobaXterm and then click "Start local terminal".

!!! Tips
    - If you download the .zip file rather than the .msi, make sure you unzip it before attempting to run the installer.

    - If you do not have administrator rights, you can use the [Portable edition of MobaXterm](https://mobaxterm.mobatek.net/download-home-edition.html).

    - If this is your first time using MobaXterm, you should check that a permanent /home directory has been set up (otherwise, all saved info will be lost from session to session). Go to "Settings" -> "Configuration" and check that a path is set in the field marked "Persistent home directory". If prompted, make sure path is set as "private".

    - Any SSH key generated in MobaXterm will, by default, be stored in the permanent /home directory (see above). That is, if your /home directory is `_MyDocuments_\MobaXterm\home` then within that folder you will find a folder named `_MyDocuments_\MobaXterm\home\.ssh` containing your keys.  This folder will be 'hidden' by default, so you may need to tick 'Hidden items' under 'View' in Windows Explorer to see it.

    - MobaXterm also allows you to set up pre-configured SSH sessions with the username, login host and key details saved.  You are welcome to use this, rather than using the "Local terminal", but we are not able to assist with debugging connection issues if you choose this method.


## Access credentials

To access ARCHER2, you need to use two sets of credentials: a password **and** an SSH key pair protected by a passphrase. You can find more detailed instructions on how to set up your credentials to access ARCHER2 from Windows, MacOS and Linux below.

### SSH Key Pairs

You will need to generate an SSH key pair protected by a passphrase to access ARCHER2.

Using a [terminal](#command-line-terminal) (the command line), set up a key pair that contains
your e-mail address and enter a passphrase you will use to unlock the
key:

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

### Upload public part of key pair to SAFE

You should now upload the public part of your SSH key pair to the SAFE
by following the instructions at:

[Login to SAFE](https://safe.epcc.ed.ac.uk).

Then:

 1.  Go to the Menu *Login accounts* and select the ARCHER2 account you
     want to add the SSH key to.
 2.  On the subsequent Login Account details page, click the *Add
     Credential* button.
 3.  Select *SSH public key* as the Credential Type and click *Next*
 4.  Either copy and paste the public part of your SSH key into the
     *SSH Public key* box or use the button to select the public key
     file on your computer.
 5.  Click *Add* to associate the public SSH key with your account.

Once you have done this, your SSH key will be added to your ARCHER2 account.

Remember, you need both an SSH key and a password to log in to ARCHER2. You will need to collect an initial password before you can log into ARCHER2. We cover this next.

!!! note
    If you want to connect to ARCHER2 from more than one machine---for example, from your home laptop as well as your work laptop---you should generate an SSH key on each machine, and add each of the public keys into SAFE.

### Initial passwords

The SAFE web interface is used to provide your initial password for logging onto ARCHER2 (see the [SAFE
Documentation](https://epcced.github.io/safe-docs) for more details on requesting accounts and picking up passwords).

!!! note

    You will be prompted to change your password the first time
    that you log in to ARCHER2. You may also change your password, at
    any time, on ARCHER2, using the `passwd` command. This change is
    not be reflected in SAFE so, if you forget your password, you
    should use SAFE to request a new one-shot password.

## SSH Clients

As noted above, you interact with ARCHER2, over an encrypted
communication channel (specifically, Secure Shell version 2
(SSH-2)). This allows command-line access to one of the login nodes of
ARCHER2, from which you can run commands or use a command-line text
editor to edit files.  SSH can also be used to run graphical programs
such as GUI text editors and debuggers, when used in conjunction with
an X Server.

### Logging in 

The login addresses for ARCHER2 are:

- ARCHER2 full system: login.archer2.ac.uk
- ARCHER2 4-cabinet system: login-4c.archer2.ac.uk


You can use the following command from the [terminal](#command-line-terminal) window to log in to ARCHER2:

=== "Full system"
    ```bash
    ssh username@login.archer2.ac.uk
    ```
=== "4-cabinet system"
    ```bash
    ssh username@login-4c.archer2.ac.uk
    ```

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

=== "4-cabinet system"
    You will first be prompted for your machine account password. Once you have entered your password successfully, you will then be prompted for the passphrase associated with your SSH key pair. You need to enter both credentials correctly to be able to access the ARCHER2 4-cabinet system.

!!! warning
    If your SSH key pair is not stored in the default location (usually
    `~/.ssh/id_rsa`) on your local system, you may need to specify the path
    to the private part of the key wih the `-i` option to `ssh`. For
    example, if your key is in a file called `keys/id_rsa_ARCHER2` you would
    use the command `ssh -i keys/id_rsa_ARCHER2 username@login.archer2.ac.uk`
    to log in (or the equivalent for the 4-cabinet system).

!!! tip
    When you first log into ARCHER2, you will be prompted to change your
    initial password. This is a three-step process:

    1.  When promoted to enter your *ldap password*: Re-enter the password
        you retrieved from SAFE.
    2.  When prompted to enter your new password: type in a new password.
    3.  When prompted to re-enter the new password: re-enter the new
        password.

    Your password will now have been changed

To allow remote programs, especially graphical applications, to control your local display, such as for a debugger, use:

=== "Full system"
    ```bash
    ssh -X username@login.archer2.ac.uk
    ```
=== "4-cabinet system"
    ```bash
    ssh -X username@login-4c.archer2.ac.uk
    ```

Some sites recommend using the `-Y` flag. While this can fix some
compatibility issues, the `-X` flag is more secure.

Current MacOS systems do not have an X window system. Users should install the XQuartz package to allow for SSH with X11 forwarding on MacOS systems:

  - [XQuartz website](http://www.xquartz.org/)


## Host Keys

A host key adds an extra security layer for users over SSH. Using one enables users to log in to ARCHER2 if the login node has the same corresponding host key. The host key can be found in the entries in `~/.ssh/known_hosts`, as given in the example below: 

    AAAAB3NzaC1yc2EAAAADAQABAAABAQC/zGWlNKRmbGcH3j/+wQ/3vytRJnautfshhKNx6naoymVxmXSg9CvtsJQUCNsNMnYu7NvZwOu1SqouXUNbpXZbOxikPLooRmM6JmCiJ72Zz5ylsXaFaIPmU7nl40J8YP5xcmlW6+HP6/gcnrZeCGLOcCSGHIIAAPotL1hwF9ab0RFbHV1+IyNPc5LYwslwmtn1zU5BY6xKISL8cMy+tAxBExY07xKZ6k+7bNPc4Ia4GfoU+8U9/2ZpN6wpNZVCNOsQ92nyELKveO9PIzLPJvxkxnRYaEfYshnRPCauBEnhZbixqrlnyWQsShbjfxBac3XEgQlg0XIAvHfFLUQNL1bv

Host key verification can fail if this key is out of date, a problem which can be fixed by removing the offending entry in `~/.ssh/known_hosts`.

## Making access more convenient using the SSH configuration file

Typing in the full command to log in or transfer data to ARCHER2 can
become tedious as it often has to be repeated several times. You can use
the SSH configuration file, usually located on your local machine at
`.ssh/config` to make the process more convenient.

Each remote site (or group of sites) can have an entry in this file,
which may look something like:

=== "Full system"
    ```
    Host archer2
        HostName login.archer2.ac.uk
        User username
    ```
=== "4-cabinet system"
    ```
    Host archer2-4c
        HostName login-4c.archer2.ac.uk
        User username
    ```

(remember to replace `username` with your actual username\!).

Taking the full-system example: the `Host` line defines a short name for the entry. In this
case, instead of typing `ssh username@login.archer2.ac.uk` to access the
ARCHER2 login nodes, you could use `ssh archer2` instead. The remaining
lines define the options for the host.

   - `Hostname login.archer2.ac.uk` --- defines the full address of the
     host
   - `User username` --- defines the username to use by default for this
     host (replace `username` with your own username on the remote
     host)

Now you can use SSH to access ARCHER2 without needing to enter your
username or the full hostname every time:

```
ssh archer2
```

You can set up as many of these entries as you need in your local
configuration file. Other options are available. See the ssh\_config manual
page (or `man ssh_config` on any machine with SSH installed) for a
description of the SSH configuration file. For example, you may find the
`IdentityFile` option useful if you have to manage multiple SSH key
pairs for different systems as this allows you to specify which SSH key
to use for each system.

!!! bug
    There is a known bug with Windows ssh-agent. If you get the error
    message: `Warning: agent returned different signature type ssh-rsa
    (expected rsa-sha2-512)`, you will need to either specify the path to
    your ssh key in the command line (using the `-i` option as described
    above) or add that path to your SSH config file by using the
    `IdentityFile` option.

## SSH debugging tips

If you find you are unable to connect to ARCHER2, there are some simple checks you may use to diagnose the issue, which are described below. If you are having difficulties connecting, we suggest trying these before
contacting the ARCHER2 Service Desk.

In the examples below, we have assumed you are connecting to the full
ARCHER2 system at `login.archer2.ac.uk`. If you are trying to log into
the 4-cabinet system instead, please replace the login address with
`login-4c.archer2.ac.uk`.

### Use the `user@login.archer2.ac.uk` syntax rather than `-l user login.archer2.ac.uk`

We have seen a number of instances where people using the syntax

```
ssh -l user login.archer2.ac.uk
```

have not been able to connect properly and get prompted for a password many
times. We have found that using the alternative syntax:

```
ssh user@login.archer2.ac.uk
```

works more reliably.

### Can you connect to the login node?

Try the command `ping -c 3 login.archer2.ac.uk`, on Linux or MacOS, or
`ping -n 3 login.archer2.ac.uk` on Windows. If you successfully
connect to the login node, the output should include:

    --- login.archer2.ac.uk ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 38ms

(the ping time '38ms' is not important). If not all packets are received
there could be a problem with your Internet connection, or the login
node could be unavailable.

### Password

If you are having trouble entering your password, consider using a
password manager, from which you can copy and paste it. If you need to
reset your password, instructions for doing so can be found in [the
SAFE
documentation](https://epcced.github.io/safe-docs/safe-for-users/\#reset\_machine)

Windows users should note that the `Ctrl+V` shortcut does not work to paste in to
PuTTY, MobaXterm, or PowerShell. Instead use `Shift+Ins` to paste.
Alternatively, right-click and select 'Paste' in PuTTY and MobaXterm, or
simply right-click to paste in PowerShell.

### SSH key

If you get the error message `Permission denied (publickey)`, this may
indicate a problem with your SSH key. Some things to check:

   - Have you uploaded the key to SAFE? Please note that if the same
     key is re-uploaded, SAFE will not map the "new" key to ARCHER2. If
     for some reason this is required, please delete the key first,
     then re-upload.
 
   - Is SSH using the correct key? You can check which keys are
     being found and offered by SSH using `ssh -vvv`. If your private
     key has a non-default name, you should use the `-i` option to
     provide it to ssh. For example, `ssh -i path/to/key
     username@login.archer2.ac.uk`.
 
   - Are you entering the passphrase correctly? You will be asked for
     your private key's passphrase first. If you enter it incorrectly
     you will usually be asked to enter it again (usually you will get
     three chances, after which SSH will fail with `Permission denied
     (publickey)`). If you would like to confirm your passphrase
     without attempting to connect, you can use `ssh-keygen -y -f
     /path/to/private/key`. If successful, this command will print the
     corresponding public key. You can also use this to check that you
     have uploaded the correct public key to SAFE.
 
   - Are permissions correct on the SSH key? One common issue is that
     the permissions are set incorrectly on either the key files or
     the directory it is contained in. On Linux and MacOS, if
     your private keys are held in `~/.ssh/` you can check this with
     `ls -al ~/.ssh`. This should give something similar to the
     following output:
     
         $ ls -al ~/.ssh/
         drwx------.  2 user group    48 Jul 15 20:24 .
         drwx------. 12 user group  4096 Oct 13 12:11 ..
         -rw-------.  1 user group   113 Jul 15 20:23 authorized_keys
         -rw-------.  1 user group 12686 Jul 15 20:23 id_rsa
         -rw-r--r--.  1 user group  2785 Jul 15 20:23 id_rsa.pub
         -rw-r--r--.  1 user group  1967 Oct 13 14:11 known_hosts
     
     The important section here is the string of letters and dashes at
     the start, for the lines ending in `.`, `id_rsa`, and
     `id_rsa.pub`, which indicate permissions on the containing
     directory, private key, and public key, respectively. If your
     permissions are not correct, they can be set with `chmod`. Consult
     the table below for the relevant `chmod` command. 


     | Target      | Permissions  | `chmod` Code |
     | ----------- | ------------ | ------------ |
     | Directory   | `drwx------` | 700          |
     | Private Key | `-rw-------` | 600          |
     | Public Key  | `-rw-r--r--` | 644          |

`chmod` can be used to set permissions on the target in the following
way: `chmod <code> <target>`. So for example to set correct
permissions on the private key file `id_rsa_ARCHER2`, use the command
`chmod 600 id_rsa_ARCHER2`.

=======
On Windows, permissions are handled differently but can be set by
     right-clicking on the file and selecting Properties \> Security \>
     Advanced. The user, SYSTEM, and Administrators should have `Full
     control`, and no other permissions should exist for both the public
     and private key files, as well as the containing folder.
     
!!! tip
    Unix file permissions can be understood in the following way. There are
    three groups that can have file permissions: (owning) *users*, (owning)
    *groups*, and *others*. The available permissions are *read*, *write*,
    and *execute*. The first character indicates whether the target is a
    file `-`, or directory `d`. The next three characters indicate the
    owning user's permissions. The first character is `r` if they have read
    permission, `-` if they don't, the second character is `w` if they have
    write permission, `-` if they don't, the third character is `x` if they
    have execute permission, `-` if they don't. This pattern is then
    repeated for *group*, and *other* permissions. For example the pattern
    `-rw-r--r--` indicates that the owning user can read and write the file,
    members of the owning group can read it, and anyone else can also read
    it. The `chmod` codes are constructed by treating the user, group, and
    owner permission strings as binary numbers, then converting them to
    decimal. For example the permission string `-rwx------` becomes
    `111 000 000` -\> `700`.

### SSH verbose output

The verbose-debugging output from `ssh` can be very useful for
diagnosing issues. In particular, it can be used to distinguish
between problems with the SSH key and password. To enable verbose
output, add the `-vvv` flag to your SSH command. For example:

    ssh -vvv username@login.archer2.ac.uk

The output is lengthy, but somewhere in there you should see lines
similar to the following:

    debug1: Next authentication method: publickey
    debug1: Offering public key: RSA SHA256:<key_hash> <path_to_private_key>
    debug3: send_pubkey_test
    debug3: send packet: type 50
    debug2: we sent a publickey packet, wait for reply
    debug3: receive packet: type 60
    debug1: Server accepts key: pkalg rsa-sha2-512 blen 2071
    debug2: input_userauth_pk_ok: fp SHA256:<key_hash>
    debug3: sign_and_send_pubkey: RSA SHA256:<key_hash>
    Enter passphrase for key '<path_to_private_key>':
    debug3: send packet: type 50
    debug3: receive packet: type 51
    Authenticated with partial success.
    debug1: Authentications that can continue: password, keyboard-interactive

In the text above, you can see which files ssh has checked for private
keys, and you can see if any key is accepted. The line `Authenticated
succeeded` indicates that the SSH key has been accepted. By default
SSH will go through a list of standard private-key files, as well as
any you have specified with `-i` or a config file. To succeed, one of
these private keys needs to match to the public key uploaded to SAFE.

If your SSH key passphrase is incorrect, you will be asked to try again
up to three times in total, before being disconnected with `Permission
denied (publickey)`. If you enter your passphrase correctly, but still
see this error message, please consider the advice under *SSH key*
above.

You should next see something similiar to:

    debug1: Next authentication method: keyboard-interactive
    debug2: userauth_kbdint
    debug3: send packet: type 50
    debug2: we sent a keyboard-interactive packet, wait for reply
    debug3: receive packet: type 60
    debug2: input_userauth_info_req
    debug2: input_userauth_info_req: num_prompts 1
    Password:
    debug3: send packet: type 61
    debug3: receive packet: type 60
    debug2: input_userauth_info_req
    debug2: input_userauth_info_req: num_prompts 0
    debug3: send packet: type 61
    debug3: receive packet: type 52
    debug1: Authentication succeeded (keyboard-interactive).

If you do not see the `Password:` prompt you may have connection issues,
or there could be a problem with the ARCHER2 login nodes. If you do not
see `Authenticated with partial success` it means your password was not
accepted. You will be asked to re-enter your password, usually two more
times before the connection will be rejected. Consider the suggestions
under *Password* above. If you *do* see `Authenticated with partial
success`, it means your password was accepted, and your SSH key will now
be checked.

The equivalent information can be obtained in PuTTY by
enabling All Logging in settings.

## Related Software 

### tmux

[tmux](https://github.com/tmux/tmux/wiki) is a multiplexer application 
available on the ARCHER2 login nodes. It allows for multiple sessions to 
be open concurrently and these sessions can be detached and run in the 
background. Furthermore, sessions will continue to run after a user logs 
off and can be reattached to upon logging in again. It is particularly 
useful if you are connecting to ARCHER2 on an unstable Internet connection 
or if you wish to keep an arrangement of terminal applications running 
while you disconnect your client from the Internet -- for example, when 
moving between your home and workplace. 
