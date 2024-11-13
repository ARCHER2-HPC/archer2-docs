# Functional accounts on ARCHER2

Functional accounts are used to enable persistent services, controlled by users running on 
ARCHER2. For example, running a licence server to allow jobs on compute nodes to check out
a licence for restricted software.

There are a number of steps involved in setting up functional accounts:

1. Submit a request to service desk for review and award of functional account entitlement
2. Creation of the functional account and associating authorisation for your standard ARCHER2
   account to access it
3. Test that you can access the persistent service node (`dvn04`) and the functional account
4. Setup of the persistent service on the persistent service node (`dvn04`)

We cover these steps in detail below with the concrete example of setting up a licence server
using the FlexLM software but the process should be able to be generalised for other 
persistent services.

!!! note
    If you have any questions about functional accounts and persistent services on ARCHER2
    please [contact the ARCHER2 Service Desk](https://www.archer2.ac.uk/support-access/servicedesk.html).

## Submit a request to service desk 

If you wish to have access to a functional account for persistent services on ARCHER2 you should
[email the ARCHER2 Service Desk](mailto:support@archer2.ac.uk) with a case for why you want to 
have this functionality. You should include the following information in your email:

- The ARCHER2 project you are associated with
- The username(s) that would require access to manage the functional account
- The use case for the functional account
- Technical requirements of the service that the functional account will support

## Creation of the functional account

If your request for a functional account is approved then the ARCHER2 user administration
team will setup the account and enable access for the standard user accounts named in the
application. They will then inform you of the functional account name.

## Test access to functional account

The process for accessing the functional account is:

1. Log into ARCHER2 using normal user account
2. Setup an SSH key pair for login access to persistent service node (`dvn04`)
3. Log into persistent service node (`dvn04`)
4. Use `sudo` to access the functional account

### Login to ARCHER2

[Log into ARCHER2 in the usual way](connecting.md) using a normal user account that has been given access
to manage the functional account. 

### Setup SSH key pair for `dvn04` access

You can create a passphrase-less SSH key pair to use for access to the persistent service 
node using the `ssh-keygen` command. As long as you place the public and private key parts
in the default location, you will not need any additional SSH options to access `dvn04` from 
the ARCHER2 login nodes. Just hit enter when prompted for a passphrase to create a key with
no passphrase.

Once the key pair has been created, you add the public part to the `$HOME/.ssh/authorized_keys`
file on ARCHER2 to make it valid for login to `dvn04` using the command
`cat $HOME/.ssh/id_rsa.pub >> $HOME/.ssh/authorized_keys`.

Example commands to setup SSH key pair:

```
auser@ln04:~> ssh-keygen -t rsa

Generating public/private rsa key pair.
Enter file in which to save the key (/home/t01/t01/auser/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/t01/t01/auser/.ssh/id_rsa
Your public key has been saved in /home/t01/t01/auser/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:wX2bgNElbsPaT8HXKIflNmqnjSfg7a8BPM1R56b4/60 auser@ln02
The key's randomart image is:
+---[RSA 3072]----+
|        ..... o .|
|       . *.o = = |
|        + B B B +|
|         * * % + |
|        S * X o  |
|         . O *   |
|          . B +  |
|           . + ..|
|            ooE.=|
+----[SHA256]-----+

auser@ln04:~> cat $HOME/.ssh/id_rsa.pub >> $HOME/.ssh/authorized_keys
```

### Login to the persistent service node (`dvn04`)

Once you are logged into an ARCHER2 login node, and assuming the SSH key is in the 
default location, you can now login to `dvn04`:

```bash
auser@ln04:~> ssh dvn04
```

!!! note
    You will need to enter the TOTP for your ARCHER2 account to login to `dvn04` unless
    you have logged in to the node recently.

### Access the functional account

Once you are logged into `dvn04`, you use `sudo` to access the functional account.

!!! important
    You must use the normal user account account password to use the `sudo` command.
    This password was set on your first ever login to ARCHER2 (and not used subsequently).
    If you have forgotten this password, you can [reset it in SAFE](https://epcced.github.io/safe-docs/safe-for-users/#how-to-reset-a-password-on-your-machine-account).
    
For example, if the functional account is called `testlm`, you would access it (on `dvn04`) with:

```bash
auser@dvn04:~> sudo -iu testlm
```

To exit the functional account, you use the `exit` command which will return you to your normal
user account on `dvn04`.

## Setup the persistent service

You should use `systemctl` to manage your persistent service on `dvn04`. In order to use the
`systemctl` command, you need to add the following lines to the `~/.bashrc` for the 
*functional account*:

```
export XDG_RUNTIME_DIR=/run/user/$UID
export DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/$UID/bus
```

Next, create a service definition file for the persistent service and save it to a plain text
file. Here is the example used for the QChem licence server:

```
[Unit]
Description=Licence manger for QChem
After=network.target
ConditionHost=dvn04
 
[Service]
Type=forking
ExecStart=/work/y07/shared/apps/core/qchem/6.1/bin/flexnet/lmgrd -l +/work/y07/shared/apps/core/qchem/6.1/var/log/qchemlm.log -c /work/y07/shared/apps/core/qchem/6.1/etc/flexnet/
ExecStop=/work/y07/shared/apps/core/qchem/6.1/bin/flexnet/lmutil lmdown -all -c /work/y07/shared/apps/core/qchem/6.1/etc/flexnet/
SuccessExitStatus=15
Restart=always
RestartSec=30
 
[Install]
WantedBy=default.target
```

Enable the licence server service, e.g. for the QChem licence server service:

```bash
testlm@dvn04:~> systemctl --user enable /work/y07/shared/apps/core/qchem/6.1/etc/flexnet/qchem-lm.service
 
Created symlink /home/y07/y07/testlm/.config/systemd/user/default.target.wants/qchem-lm.service → /work/y07/shared/apps/core/qchem/6.1/etc/flexnet/qchem-lm.service.
Created symlink /home/y07/y07/testlm/.config/systemd/user/qchem-lm.service → /work/y07/shared/apps/core/qchem/6.1/etc/flexnet/qchem-lm.service.
```

Once it has been enabled, you can start the licence server service, e.g. for the QChem licence server service:

```bash
testlm@dvn04:~> systemctl --user start qchem-lm.service
```

Check the status to make sure it is running:

```bash
testlm@dvn04:~> systemctl --user status qchem-lm
● qchem-lm.service - Licence manger for QChem
     Loaded: loaded (/home/y07/y07/testlm/.config/systemd/user/qchem-lm.service; enabled; vendor preset: disabled)
     Active: active (running) since Thu 2024-05-16 15:33:59 BST; 8s ago
    Process: 174248 ExecStart=/work/y07/shared/apps/core/qchem/6.1/bin/flexnet/lmgrd -l +/work/y07/shared/apps/core/qchem/6.1/var/log/qchemlm.log -c /work/y07/shared/apps/core/qchem/6.1/etc/flexnet/ (code=exited, status=0/SUCCESS)
   Main PID: 174249 (lmgrd)
      Tasks: 8 (limit: 39321)
     Memory: 5.6M
        CPU: 18ms
     CGroup: /user.slice/user-35153.slice/user@35153.service/app.slice/qchem-lm.service
             ├─ 174249 /work/y07/shared/apps/core/qchem/6.1/bin/flexnet/lmgrd -l +/work/y07/shared/apps/core/qchem/6.1/var/log/qchemlm.log -c /work/y07/shared/apps/core/qchem/6.1/etc/flexnet/
             └─ 174253 qchemlm -T 10.252.1.77 11.19 10 -c :/work/y07/shared/apps/core/qchem/6.1/etc/flexnet/: -lmgrd_port 6979 -srv mdSVdgushTnAjHX1s1PTj0ppCjHJw1Uk9ylvs1j13zkaUzhDBFlbv4thnqEIAXV --lmgrd_start 66461957 -vdrestart 0 -l /work/y07/shar>
```


