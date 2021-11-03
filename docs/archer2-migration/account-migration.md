# Accessing the ARCHER2 full system

This section covers the following questions:

  - When will I be able to access ARCHER2 full service? 
  - Has my project been enabled on ARCHER2 full service?
  - How much resource will my project have on ARCHER2 full service?
  - How do I set up an ARCHER2 account?
  - How do I log into ARCHER2 for the first time?

!!! tip
    If you need help or have questions on using ARCHER2 4-cabinet system and ARCHER2 full system
    please [contact the ARCHER2 service desk](https://www.archer2.ac.uk/support-access/servicedesk.html)

## When will I be able to access ARCHER2 full service?

We anticipate that users will have access from mid-late November. Users will have access to both the ARCHER2-4Cabinet and ARCHER2 full service for at least 30 days. UKRI will confirm the dates and these will be communicated to you as they are confirmed. There will be at least 14 days notice before access to the ARCHER2-4Cabinet system is removed. 

!!! important "Dates (tbc with UKRI)"
    mid-late November: Access to ARCHER2 full service
    30 days later tbc: with 14 days notice, access to ARCHER2-4C and the archer2-work filesystem removed 
    
## Has my project been enabled on ARCHER2 full service?

If you have an active ARCHER2-4Cabinet allocation on 1st October 2021 then your project will be enabled on the ARCHER2 full service. The project code is the same on the full service as it is on ARCHER2-4Cabinet service. 

Some further information that may be useful:

   - If you are a member of the EPSRC or NERC consortia on ARCHER2 then all of these consortia *have* been enabled on ARCHER2 full service.
     [A list of the consortia and their ARCHER2 codes can be found on the ARCHER2 website.](https://www.archer2.ac.uk/research/consortia/)
   
## How much resource will my project have on ARCHER2 full service?

The unit of allocation on ARCHER2 is called the ARCHER2 Compute Unit (CU) and, in general, 1 CU will be worth 1 ARCHER2 node hour.
The same allocation unit and resource pool is used on both the ARCHER2-4Cabinet and ARCHER2 full service. 

There will be an initial no-charging period on the ARCHER2 full service but projects will need to have a positive budget to use this. Users will be notified before the no-charging period ends.


## How do I set up an ARCHER2 full service account?

Users will keep the same username and SSH keys that they use on archer2-4c and these will be enabled for the archer2 full service.
Your archer2 machine account will be enabled for the ARCHER2 full service without you having to do anything. 

Once you have been notified that the ARCHER2 full service is open, you will be able to check that your account has been enabled by logging into the unified SAFE. 
The URL for the unified SAFE is:
   - [https://safe.epcc.ed.ac.uk](https://safe.epcc.ed.ac.uk)

!!! important
    To check that your account has been enabled on the ARCHER2 full system, <br>
    log into [SAFE](https://safe.epcc.ed.ac.uk):<br>
    Select the login account from the pulldown menu, Login Accounts <br>
    You will see the list of Machines which the account is enabled on: archer2-4c and archer2 should both be listed
      
## How do I log into ARCHER2 for the first time?

The ARCHER2 documentation covers logging in to ARCHER2 from a variety of operating systems:
   - [Logging in to ARCHER2 from macOS/Linux](https://docs.archer2.ac.uk/user-guide/connecting/#logging-in-from-linux-and-macos)
   - [Logging in to ARCHER2 from Windows](https://docs.archer2.ac.uk/user-guide/connecting/#logging-in-from-windows-using-mobaxterm)

!!! tip
    The ordering of the two factors for conencting to the full service is the reverse than that on ARCHER2-4Cabinet: <br>
    archer2-4c: machine password, passphrase for SSH key pair<br>
    archer2: passphrase for SSH key pair, machine password

!!! important "login address for the system"  
    ARCHER2 4Cabinet: login-4c.archer2.ac.uk<br>
    ARCHER2 full service : login.archer2.ac.uk


## What will happen to ARCHER2 data? 

There are three filsystems associated with the ARCHER2 Service: 

/home: 
Users /home directory will be mounted on both the ARCHER2-4C service and the ARCHER2 full service. Users will be able to access the /home filesystem from both services and no action is required to move data. The /home filesystem will be read and writeable on both services during the transition period.  

/work:
The work filesystem on archer2-4c (archer2-4c-work) will remain available on the 4cabinet service. There will be a new /work filesystem on the ARCHER2 full service. 
Users will have a new allocation on this new /work filesystem on the ARCHER2 full service (a2fs-work1, a2fs-work2, a2fs-work3).  The initial quota will typically be double the quota of ARCHER2-4C /work (archer2-4c-work).

!!! important
Users are responsible for transferring their /work data from archer2-4c-work to their new archer2 /work filesystem (a2fs-work1, a2fs-work2, a2fs-work3).
archer2-4c-work will be available for the users to transfer their work from for at least 30 days and 14 days notice will be given before archer2-4c is removed.
There is a data migration guide which can be found at: <link to Julien's guide>. 
 
RDFaaS: 
The RDFaaS filesystems (/epsrc and /general) are read and writeable from both archer2-4c and archer2 full system.  


## Will my code run on ARCHER2 full service? 

We expect compiled code that runs successfully on archer2-4c to run successfully on archer2 full service. 
More details about the differences between archer2-4c and archer2 full service can be found at<link to Andy's page on system differences>


