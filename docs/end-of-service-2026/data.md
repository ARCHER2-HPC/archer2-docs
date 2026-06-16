# Data on ARCHER2 at end of service

Summary of impact and advice for data on ARCHER2 at end of service

- All data on ARCHER2 file systems will not be available in any form after end of service -
  any data you wish to keep *must* be transferred to a different location.
  + This applies to the following file systems: ARCHER2 home, ARCHER2 work, solid state scratch and RDFaaS
- The Research Data Facility as a Service (RDFaaS) will be available for EPSRC users to store data on beyond the ARCHER2 Service. Users will be responsible for transferring their own data and capacity will be limited so projects will need to carefully consider what data they 
  want to store on this system.
- The JASMIN Data Facility will be available for NERC users to store data on beyond the ARCHER2 Service.   
- Plan and start transfers early to avoid congestion close to end of service

!!! note "Data storage beyond the end of ARCHER2: "
    We will provide more detailed instructions to users on how to get access and transfer data to RDFaaS and JASMIN before the end of the ARCHER2 Service. This will include user documentation and webinars where users can ask questions. 
    Users should expect the capacity of this storage to be a fraction of current quotas on the ARCHER2 work file system so you
    should carefully consider what data you need to store.

## File systems on ARCHER2

| File system | End of access | User data locations | Notes |
|---|---|---|---|
| ARCHER2 home | 21 Nov 2026 | `/home/[project ID]/[group ID]/[user ID]` | No access to any data beyond end of access date |
| ARCHER2 work | 21 Nov 2026 | `/work/[project ID]/[group ID]/[user ID]` | No access to any data beyond end of access date |
| ARCHER2 solid state scratch | 21 Nov 2026 | `/mnt/lustre/a2fs-nvme/[project ID]/[group ID]/[user ID]` | No access to any data beyond end of access date |
| RDFaaS | beyond ARCHER2 Service | `/epsrc/[project ID]/[group ID]/[user ID]` <br/> `/general/[project ID]/[group ID]/[user ID]` | Data will need to be transferred from ARCHER2 |


## Data transfer

The ARCHER2 documentation contains specific guidance on archiving and transferring data off 
the system:

- [Data transfer to/from ARCHER2 (ARCHER2 documentation)](https://docs.archer2.ac.uk/user-guide/data/#archiving-and-data-transfer)

The key tools for transferring data off ARCHER2 are:

- [rclone](https://docs.archer2.ac.uk/user-guide/data/#data-transfer-using-rclone) - for parallel data transfers over SSH to remote systems, local laptops/workstations or to object store (e.g. OneDrive)
- [rsync](https://docs.archer2.ac.uk/user-guide/data/#rsync) - for serial data transfers over SSH to remote systems and local laptops/workstations
- [Globus Online](https://docs.archer2.ac.uk/user-guide/data/#data-transfer-via-globus) - for large-scale data transfers to remote facilities with a Globus endpoint (e.g. [JASMIN](https://help.jasmin.ac.uk/docs/data-transfer/globus-transfers-with-jasmin/))
