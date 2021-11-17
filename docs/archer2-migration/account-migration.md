# Accessing the ARCHER2 full system

This section covers the following questions:

  - When will I be able to access ARCHER2 full system? 
  - Has my project been enabled on ARCHER2 full system?
  - How much resource will my project have on ARCHER2 full system?
  - How do I set up an account on the full system?
  - How do I log into the different ARCHER2 systems?

!!! tip
    If you need help or have questions on using ARCHER2 4-cabinet system and ARCHER2 full system
    please [contact the ARCHER2 service desk](https://www.archer2.ac.uk/support-access/servicedesk.html)

## When will I be able to access ARCHER2 full system? 

We anticipate that users will have access from mid-late November. Users will have access to both the ARCHER2 4-cabinet system and ARCHER2 full system for at least 30 days. UKRI will confirm the dates and these will be communicated to you as they are confirmed. There will be at least 14 days notice before access to the ARCHER2 4-Cabinet system is removed. 

## Has my project been enabled on ARCHER2 full system?

If you have an active ARCHER2 4-cabinet system allocation on 1st October 2021 then your project will be enabled on the ARCHER2 full system. The project code is the same on the full service as it is on ARCHER2 4-cabinet system. 

Some further information that may be useful:

   - If you are a member of the EPSRC or NERC consortia on ARCHER2 then all of these consortia *have* been enabled on ARCHER2 full system. [A list of the consortia and their ARCHER2 codes can be found on the ARCHER2 website.](https://www.archer2.ac.uk/research/consortia/)
   
## How much resource will my project have on ARCHER2 full system?

The unit of allocation on ARCHER2 is called the ARCHER2 Compute Unit (CU) and 1 CU is equivalent to 1 ARCHER2 node hour.
Your time budget will be shared on both systems. This means that any existing allocation available to your project on the 4-cabinet system will also be available on the full system. 

There will be a period of at least 30 days where users will have access to both the 4-cabinet system and the full system. During this time, use on the full system will be uncharged and use on the 4-cabinet system will be a charged in the usual way.  Users will be notified before the no-charging period ends.


## How do I set up an account on the full system?

You will keep the same usernames, passwords and SSH keys that you use on the 4-cabinet system on the full system.

You do not need to do anything to enable your account, these will be made available automatically once access to the full system is available. 
 
You will connect to the full system in the same way as you connect to the 4-cabinet system except for switching the ordering of the credentials: 

* 4-cabinet system: enter machine account password then passphrase for SSH key pair
* Full system: enter passphrase for SSH key pair then machine account password
 
## How do I log into the different ARCHER2 systems?

The ARCHER2 documentation covers logging in to ARCHER2 from a variety of operating systems:
   - [Logging in to ARCHER2 from macOS/Linux](https://docs.archer2.ac.uk/user-guide/connecting/#logging-in-from-linux-and-macos)
   - [Logging in to ARCHER2 from Windows](https://docs.archer2.ac.uk/user-guide/connecting/#logging-in-from-windows-using-mobaxterm)

Login addresses:
 
* ARCHER2 4-cabinet system: login-4c.archer2.ac.uk
* ARCHER2 full system: login.archer2.ac.uk

!!! tip
    When logging into the ARCHER2 full system for the first time, you may see an error 
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


## What will happen to ARCHER2 data? 

There are three file systems associated with the ARCHER2 Service: 
 
### home file systems

The home file systems will be mounted on both the 4-cabinet system and the full system; so users’ directories are shared across the two systems. Users will be able to access the home file systems from both systems and no action is required to move data. The home file systems will be read and writeable on both services during the transition period. 
 
### work file systems

There are different work file systems for the 4-cabinet system and the full system. 
 
The work file system on the 4-cabinet system (labelled “archer2-4c-work” in SAFE) will remain available on the 4-cabinet system during the transition period.

There will be new work file systems on the full system and you will have new directories on the new work file systems. Your initial quotas will typically be double your quotas for the 4-cabinet work file system.
 
Important: you are responsible for transferring any required data from the 4-cabinet work file systems  to your new directories on the work file systems on the full system.

The work file system on the 4-cabinet system will be available for you to transfer your data from for at least 30 days from the start of the ARCHER2 full system access and 14 days notice will be given before the 4-cabinet work file system is removed.

### RDFaaS file systems

For users who have access to the RDFaaS, your RDFaaS data will be available on both the 4-cabinet system and the full system during the transition period and will be readable and writeable on both systems.



