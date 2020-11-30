# Migrating your account from ARCHER to ARCHER2

!!! warning
    Access to ARCHER2 is not yet available for most users. We
    are planning for user access to be enabled by
    **Friday 4th December 2020**.

This section covers the following questions:

  - When will I be able to access ARCHER2?
  - Has my project has been migrated from ARCHER to ARCHER2?
  - How much resource will my project have on ARCHER2?
  - How do I set up an ARCHER2 account?
  - How do I log into ARCHER2 for the first time?

!!! tip
    If you need help or have questions on ARCHER to ARCHER2 migration, 
    please [contact the ARCHER2 service desk](https://www.archer2.ac.uk/support-access/servicedesk.html)

## When will I be able to access ARCHER2?

We anticipate that users will have access by Friday 4th December 2020. Notification
of activation of ARCHER2 projects will be sent to the project leaders/PIs for
them to contact the project users.

## Has my project been migrated to ARCHER2?

If you have an active ARCHER allocation at the end of the ARCHER service
then your project will very likely be migrated to ARCHER2. If your project
is migrated to ARCHER2 then it will have the same project code as it had
on ARCHER.

Some further information that may be useful:

   - If you are a member of the EPSRC or NERC consortia on ARCHER then
     all of these consortia *have* been migrated to ARCHER2.
     [A list of the consortia and their ARCHER/ARCHER2 codes can be found on the ARCHER2 website.](https://www.archer2.ac.uk/research/consortia/)
   - If your project code begins with *i* or *d* then you are a member
     of an industrial project or a Director's Time project, these
     projects will be contacted individually to discuss arrangements.
     Please speak to your project leader or PI in the first instance.

## How much resource will my project have on ARCHER2?

The unit of allocation on ARCHER2 is called the ARCHER2 Compute Unit (CU)
and, in general, 1 CU will be worth 1 ARCHER2 node hour.

UKRI have determined the conversion rates which will be used to transfer
existing ARCHER allocations onto ARCHER2. These will be: 

   - 1.5156 kAU = 4.21 ARCHER node hour = 1 ARCHER2 node hour = 1 CU

In identifying these conversion rates UKRI has endeavoured to ensure that
no user will be disadvantaged by the transfer of their allocation from
ARCHER to ARCHER2.

A nominal allocation will be provided to all projects during the initial
no-charging period. Users will be notified before the no-charging period ends.

When the ARCHER service ends, any unused ARCHER allocation in kAUs will be
converted to ARCHER2 CUs and transferred to ARCHER2 project allocation.

## How do I set up an ARCHER2 account?

Once you have been notified that you can go ahead and setup an ARCHER2 
account you will do this through SAFE. Note that you should use the new
unified SAFE interface rather than the ARCHER SAFE. The correct URL for
the new SAFE is:

   - [https://safe.epcc.ed.ac.uk](https://safe.epcc.ed.ac.uk)

Your access details for this SAFE are the same as those for the ARCHER
SAFE. You should log in in exactly the same way as you did on the 
ARCHER SAFE.

!!! important
    You should make sure you request the **same** account name in your
    project on ARCHER2 as you have on ARCHER. This is to ensure that
    you have seamless access to your ARCHER /home data on ARCHER2. See
    the [ARCHER to ARCHER2 Data Migration page](data-migration.md) for
    details on data transfer from ARCHER to ARCHER2

Once you have logged into SAFE, you will need to complete the following
steps before you can log into ARCHER2 for the first time:

   1. Request an ARCHER2 account through SAFE
      1. See: [How to request a machine account (SAFE documentation)](https://epcced.github.io/safe-docs/safe-for-users/#how-to-request-a-machine-account)
   2. (Optional) Create a new SSH key pair and add it to your ARCHER2 account in SAFE
      1. See: [SSH key pairs (ARCHER2 documentation)](https://docs.archer2.ac.uk/user-guide/connecting/#ssh-key-pairs)
      2. If you do not add a new SSH key to your ARCHER2 account, then
         your account will use the same key as your ARCHER account
   3. Collect your initial, one-shot password from SAFE
      1. See: [Intial passwords (ARCHER2 documentation)](https://docs.archer2.ac.uk/user-guide/connecting/#initial-passwords)

## How do I log into ARCHER2 for the first time?

The ARCHER2 documentation covers logging in to ARCHER from a variety
of operating systems:

   - [Logging in to ARCHER2 from macOS/Linux](https://docs.archer2.ac.uk/user-guide/connecting/#logging-in-from-linux-and-macos)
   - [Logging in to ARCHER2 from Windows](https://docs.archer2.ac.uk/user-guide/connecting/#logging-in-from-windows-using-mobaxterm)