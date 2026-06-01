# Data on ARCHER2 at end of service

Summary of impact and advice for data on ARCHER2 at end of service

- All data on ARCHER2 file systems will not be available in any form after end of service -
  any data you wish to keep *must* be transferred to a different location.
  + This applies to the following file systems: ARCHER2 home, ARCHER2 work, solid state scratch and RDFaaS
- Plan and start transfers early to avoid congestion close to end of service

!!! note "Data storage beyond the end of ARCHER2"
    We are working on plans to provide capacity to store data for some time after the end of
    ARCHER2 on a storage system that will be accessible directly from ARCHER2. We will provide
    more details to users as they become available but you should expect the capacity of this
    storage to be a small fraction of current quotas on the ARCHER2 work file system so you
    should not plan for this storage system to have the capacity to store the majority of data
    you have on ARCHER2. The safest approach is still to transfer any important data off ARCHER2
    to other locations before the end of service.

## File systems on ARCHER2

| File system | End of access | User data locations | Notes |
|---|---|---|
| ARCHER2 home | 21 Nov 2026 | /home/<project ID/<group ID>/<user ID> | No access to any data beyond end of access date |
| ARCHER2 work | 21 Nov 2026 | /work/<project ID/<group ID>/<user ID> | No access to any data beyond end of access date |
| ARCHER2 solid state scratch | 21 Nov 2026 | /mnt/lustre/a2fs-nvme/<project ID/<group ID>/<user ID> | No access to any data beyond end of access date |
| RDFaaS | 21 Nov 2026 | /epsrc/<project ID/<group ID>/<user ID> <br/> /general/<project ID/<group ID>/<user ID> | No access to any data beyond end of access date |

## Data transfer

The ARCHER2 documentation contains specific guidance on archiving and transferring data off 
the system:

- [Data transfer to/from ARCHER2 (ARCHER2 documentation)](https://docs.archer2.ac.uk/user-guide/data/#archiving-and-data-transfer)

The key tools for transferring data off ARCHER2 are:

- [rclone](https://docs.archer2.ac.uk/user-guide/data/#data-transfer-using-rclone) - for parallel data transfers over SSH to remote systems, local laptops/workstations or to object store (e.g. OneDrive)
- [rsync](https://docs.archer2.ac.uk/user-guide/data/#rsync) - for serial data transfers over SSH to remote systems and local laptops/workstations
- [Globus Online](https://docs.archer2.ac.uk/user-guide/data/#data-transfer-via-globus) - for large-scale data transfers to remote facilities with a Globus endpoint (e.g. [JASMIN](https://help.jasmin.ac.uk/docs/data-transfer/globus-transfers-with-jasmin/))
