# Data on ARCHER2 at end of service

Summary of impact and advice for data on ARCHER2 at end of service

- All data on ARCHER2 file systems will not be available in any form after end of service -
  any data you wish to keep *must* be transferred to a different location.
  + This applies to the following file systems: ARCHER2 home, ARCHER2 work and solid state scratch
- RDFaaS data will be retained until end of Mar 2027
  + Projects/users with data on RDFaaS will be able to access the storage to transfer data to different
    locations after ARCHER2 ends using standard tools such as scp/sftp/rsync/rclone.
- Projects/users can use the new NSCDS/EPCCfs to store limited data
  beyond the lifetime of ARCHER2. This storage system will be accessible from other systems hosted at EPCC
  such as Cirrus. Capacity will be limited so projects will need to carefully
  consider what data they want to store on this system.
  + Projects/users with data on NSCDS/EPCCfs will be able to access the storage to transfer data to different
    locations after ARCHER2 ends using standard tools such as scp/sftp/rsync/rclone and Globus Online.
- Plan and start transfers early to avoid congestion close to end of service

!!! tip "RDFaaS and NSCDS/EPCCfs: Data storage beyond the lifetime of ARCHER2"
    The RDFaaS will retain data until end of Mar 2027. We are also providing a new location for 
    projects to store limited amounts of data beyond the end of ARCHER2: the NSCDS/EPCCfs.
    Users will need to transfer data from current file systems to it before
    the end of ARCHER2. You should not plan for this storage to be able to host all the data
    you currently have on ARCHER2.

## File systems on ARCHER2

| File system | End of access | User data locations | Notes |
|---|---|---|---|
| ARCHER2 home | 17:00 GMT, Fri 20 Nov 2026 | `/home/[project ID]/[group ID]/[user ID]` | No access to any data beyond end of access date |
| ARCHER2 work | 17:00 GMT, Fri 20 Nov 2026 | `/work/[project ID]/[group ID]/[user ID]` | No access to any data beyond end of access date |
| ARCHER2 solid state scratch | 17:00 GMT, Fri 20 Nov 2026 | `/mnt/lustre/a2fs-nvme/[project ID]/[group ID]/[user ID]` | No access to any data beyond end of access date |
| RDFaaS | 31 Mar 2027 | `/epsrc` and `/general`  | Data on RDFaaS will be retained until 31 Mar 2027. Access via scp/sftp/rsync/rclone to download data beyond end of ARCHER2. |
| NSCDS/EPCCfs | at least 31 Mar 2027 and likely to mid-2028 | `/nscds/[project ID]/[group ID]/[user ID]` | Location for data that can accessed from other services (e.g. Cirrus NCR) beyond the end of ARCHER2. Also access via scp/sftp/rsync/rclone and Globus Online beyond end of ARCHER2. Available from 9 Sep 2026. |

## NSCDS/EPCCfs

NSCDS/EPCCfs will be available on ARCHER2 from 9 Sep 2026 and provides
limited capacity for projects to store data beyond the lifetime of ARCHER2 on a storage
platform that will be available on other facilities hosted by
[EPCC, UK National Supercomputing Centre](https://www.epcc.ed.ac.uk), for example 
the [Cirrus National Compute Resource (NCR)](https://www.cirrus.ac.uk). Access will also
be available via scp/sftp/rsync/rclone and Globus Online beyond end of ARCHER2.

!!! important "NSCDS/EPCCfs available from 9 Sep 2026"
    NSCDS/EPCCfs will be available to users from 9 Sep 2026 and will be accessible to ARCHER2
    projects with data on it until at least 31 Mar 2027 and likely to mid-2028.

### Requesting access to the NSCDS/EPCCfs

If you do not already have access to NSCDS/EPCCfs, you should ask your project PI or project manager
to request access via the [ARCHER2 Service Desk](https://www.archer2.ac.uk/support-access/servicedesk.html).

### Location of directories on NSCDS/EPCCfs

If you have access to NSCDS/EPCCfs, your directories will be at:

```
/nscds/<project code>/<project code>/<username>
```

For example, if your username is `auser` and you are in the `e05` project, then
your NSCDS/EPCCfs directory will be at:

```
/nscds/e05/e05/auser
```

!!! important "NSCDS/EPCCfs not on compute nodes"
    NSCDS/EPCCfs is not available on the ARCHER2 compute nodes. It is available on the
    ARCHER2 login nodes and the data analysis nodes available via the "serial" QoS.

### Organising your data on NSCDS/EPCCfs

As NSCDS/EPCCfs will be available across multiple services, we advise that you create an `archer2`
subdirectory in your space to ensure that your ARCHER2 data does not get accidentally overwritten
or confused with data you generate on other services where NSCDS/EPCCfs is available. 

### Transferring data to NSCDS/EPCCfs

You can use standard tools such as `cp` to copy small datasets to NSCDS/EPCCfs. For larger amounts of 
data, you may wish to consider using `rclone` to copy data in a parallel way. This use of rclone
is documented at:

- [Using rclone for local data transfer](../user-guide/data.md#local-file-transfer)

As NSCDS/EPCCfs is available on the data analysis nodes so you can put data transfer processes in 
serial jobs if they are going to take a long time, see:

- [Running serial jobs](user-guide/analysis.md#requesting-resources-on-the-data-analysis-nodes-using-slurm)

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
