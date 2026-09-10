# Data on ARCHER2 at end of service

Summary of impact and advice for data on ARCHER2 at end of service

- All data on ARCHER2 file systems will not be available in any form after end of service -
  any data you wish to keep *must* be transferred to a different location.
    + This applies to the following file systems: ARCHER2 home, ARCHER2 work and solid state scratch
- RDFaaS data will be retained until end of Mar 2027
    + Projects/users with data on RDFaaS will be able to access the storage to transfer data to different
      locations after ARCHER2 ends using standard tools such as scp/sftp/rsync/rclone.
- Projects/users can use the new NSCDS/EPCCFS to store limited data
  beyond the lifetime of ARCHER2.  Capacity will be limited so projects will need to carefully
  consider what data they want to store on this system.
    + Projects/users with data on NSCDS/EPCCFS will be able to access the storage to transfer data to different
      locations after ARCHER2 ends using standard tools such as scp/sftp/rsync/rclone and Globus Online.
    + This storage system will also be directly accessible from other systems hosted at EPCC such as Cirrus.
- Plan and start transfers early to avoid congestion close to end of service

!!! tip "RDFaaS and NSCDS/EPCCFS: Data storage beyond the lifetime of ARCHER2"
    The RDFaaS will retain data until end of Mar 2027. We are also providing a new location for 
    projects to store limited amounts of data beyond the end of ARCHER2: the NSCDS/EPCCFS.
    Users will need to transfer data from current file systems to it before
    the end of ARCHER2. You should not plan for this storage to be able to host all the data
    you currently have on ARCHER2.

## File systems on ARCHER2

| File system | End of access | User data locations | Notes |
|---|---|---|---|
| ARCHER2 home | 17:00 GMT, Fri 20 Nov 2026 | `/home/[project ID]/[group ID]/[user ID]` | No access to any data beyond end of access date |
| ARCHER2 work | 17:00 GMT, Fri 20 Nov 2026 | `/work/[project ID]/[group ID]/[user ID]` | No access to any data beyond end of access date |
| ARCHER2 solid state scratch | 17:00 GMT, Fri 20 Nov 2026 | `/mnt/lustre/a2fs-nvme/[project ID]/[group ID]/[user ID]` | No access to any data beyond end of access date |
| RDFaaS | 31 Mar 2027 | `/epsrc` and `/general`  | Data on RDFaaS will be retained until 31 Mar 2027. Access via scp/sftp/rsync/rclone to download data beyond end of ARCHER2. Access and quotas will be frozen in state as they are at end of ARCHER2 service. |
| NSCDS/EPCCFS | at least 31 Mar 2027 and likely to mid-2028 | `/nscds/[project ID]/[group ID]/[user ID]` | Location for data that can accessed from other services (e.g. Cirrus NCR) beyond the end of ARCHER2. Also access via scp/sftp/rsync/rclone and Globus Online beyond end of ARCHER2.  Access and quotas can be managed via SAFE even beyond end of ARCHER2 service. |

## NSCDS/EPCCFS

NSCDS/EPCCFS has been available since 9 Sep 2026 and provides
limited capacity for projects that have national HPC resources allocated
beyond the end of ARCHER2 to store data beyond the lifetime of ARCHER2 on a storage
platform that will be available on other facilities hosted by
[EPCC, UK National Supercomputing Centre](https://www.epcc.ed.ac.uk), for example 
the [Cirrus National Compute Resource (NCR)](https://www.cirrus.ac.uk). Access will also
be available via scp/sftp/rsync/rclone and Globus Online beyond end of ARCHER2.

!!! important "NSCDS/EPCCFS available from 9 Sep 2026"
    NSCDS/EPCCFS has been available to users from 9 Sep 2026 and will be accessible to ARCHER2
    projects with data on it until at least 31 Mar 2027 and likely to mid-2028.

### Requesting access to the NSCDS/EPCCFS

If you do not already have access to NSCDS/EPCCFS, you should ask your project PI or project manager
to request access via the [ARCHER2 Service Desk](https://www.archer2.ac.uk/support-access/servicedesk.html).

!!! important "Only available to projects running beyond ARCHER2"
    Only projects with national HPC allocations running beyond the end of ARCHER2 will
    be granted access to NSCDS/EPCCFS. Projects that finish at or before the end of 
    ARCHER2 should ensure they have moved all data off the system before it ends.

### Setting up your account for access to NSCDS/EPCCFS

Once your project has been granted access to NSCDS/EPCCFS, you will need to setup your
account to access the storage system.

As the NSCDS/EPCCFS is based off a different authentication system from ARCHER2, you
*must* request a new account on the Cirrus NCR system to be able to access the storage
system - this new account must be in the same project you are storing data for and must 
have an identical username to your ARCHER2 account.

Step-by-step instructions:

1. Login to [SAFE](https://safe.epcc.ed.ac.uk)
2. From the top menu, select *Login accounts -> Request login account*
3. Enter the project ID (**this must match the project ID on ARCHER2**), click "Next"
4. Select "Cirrus" as the machine, click "Next"
5. Enter **the same username as you have on ARCHER2**, click "Request". (Note: you do not need to supply an SSH key or set MFA token at this stage.)
6. You will receive an email as soon as your account has been setup - once this has happened, you will 
   be able to access NSCDS/EPCCFS on ARCHER2

### Location of directories on NSCDS/EPCCFS

If you have access to NSCDS/EPCCFS, your directories will be at:

```
/nscds/<project code>/<project code>/<username>
```

For example, if your username is `auser` and you are in the `e05` project, then
your NSCDS/EPCCFS directory will be at:

```
/nscds/e05/e05/auser
```

!!! important "NSCDS/EPCCFS not on compute nodes"
    NSCDS/EPCCFS is not available on the ARCHER2 compute nodes. It is available on the
    ARCHER2 login nodes and the data analysis nodes available via the "serial" QoS.

### Organising your data on NSCDS/EPCCFS

As NSCDS/EPCCFS will be available across multiple services, we advise that you create an `archer2`
subdirectory in your space to ensure that your ARCHER2 data does not get accidentally overwritten
or confused with data you generate on other services where NSCDS/EPCCFS is available. 

### Transferring data to NSCDS/EPCCFS

You can use standard tools such as `cp` to copy small datasets to NSCDS/EPCCFS. For larger amounts of 
data, you may wish to consider using `rclone` to copy data in a parallel way. This use of rclone
is documented at:

- [Using rclone for local data transfer](../user-guide/data.md#local-file-transfer)

As NSCDS/EPCCFS is available on the data analysis nodes so you can put data transfer processes in 
serial jobs if they are going to take a long time, see:

- [Running serial jobs](user-guide/analysis.md#requesting-resources-on-the-data-analysis-nodes-using-slurm)

### Snapshots on NSCDS/EPCCFS

NSCDS/EPCCFS retain snapshots which can be used to recover past versions of files.
Snapshots are taken weekly (for each of the past two weeks), daily (for each
of the past two days) and hourly (for each of the last 6 hours). You can
access the snapshots at `.snapshot` from any given directory on NSCDS/EPCCFS.

## Data transfer

The ARCHER2 documentation contains specific guidance on archiving and transferring data off 
the system:

- [Data transfer to/from ARCHER2 (ARCHER2 documentation)](https://docs.archer2.ac.uk/user-guide/data/#archiving-and-data-transfer)

The key tools for transferring data off ARCHER2 are:

- [rclone](https://docs.archer2.ac.uk/user-guide/data/#data-transfer-using-rclone) - for parallel data transfers over SSH to remote systems, local laptops/workstations or to object store (e.g. OneDrive)
- [rsync](https://docs.archer2.ac.uk/user-guide/data/#rsync) - for serial data transfers over SSH to remote systems and local laptops/workstations
- [Globus Online](https://docs.archer2.ac.uk/user-guide/data/#data-transfer-via-globus) - for large-scale data transfers to remote facilities with a Globus endpoint (e.g. [JASMIN](https://help.jasmin.ac.uk/docs/data-transfer/globus-transfers-with-jasmin/))

!!! tip "NERC-supported projects can use JASMIN for data storage"
    NERC-supported projects have been granted access to data storage resources on
    [JASMIN](https://www.jasmin.ac.uk/). Please see the JASMIN website for details
    on how to transfer data ([Globus Online](https://docs.archer2.ac.uk/user-guide/data/#data-transfer-via-globus)
    is supported for data transfers to JASMIN). If you have any questions on JASMIN
    access for your NERC-supported project, please contact the 
    [NERC HPC Team](mailto:hpc@nerc.ukri.org).
