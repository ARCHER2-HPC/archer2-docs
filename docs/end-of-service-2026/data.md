# Data on ARCHER2 at end of service

Summary of impact and advice for data on ARCHER2 at end of service

- All data on ARCHER2 file systems will not be available in any form after end of service -
  any data you wish to keep *must* be transferred to a different location.
  + This applies to the following file systems: ARCHER2 home, ARCHER2 work, solid state scratch and current RDFaaS
- There will be new hardware provisioned for an RDF storage system before the end of the ARCHER2 service. Users can  
  transfer data for storage beyond the end of ARCHER2 - capacity will be limited so projects will need to carefully
  consider what data they want to store on this system.
- Plan and start transfers early to avoid congestion close to end of service

!!! note "RDFaaS: data storage beyond the end of ARCHER2: "
    RDFaaS will be available beyond the end of the ARCHER2 service but users will need to transfer data
    from current file system to it before the end of ARCHER2 - including data
    on /epsrc and /general. Detailed instructions on how to access continuing RDFaaS storage will be
    provided as soon as they are available but you should expect the capacity of this storage to be a
    fraction of current quotas on the ARCHER2 work file system. You should not plan for this storage
    system to be able to store the majority of data you have on ARCHER2.

## File systems on ARCHER2

| File system | End of access | User data locations | Notes |
|---|---|---|---|
| ARCHER2 home | 21 Nov 2026 | `/home/[project ID]/[group ID]/[user ID]` | No access to any data beyond end of access date |
| ARCHER2 work | 21 Nov 2026 | `/work/[project ID]/[group ID]/[user ID]` | No access to any data beyond end of access date |
| ARCHER2 solid state scratch | 21 Nov 2026 | `/mnt/lustre/a2fs-nvme/[project ID]/[group ID]/[user ID]` | No access to any data beyond end of access date |
| RDFaaS | Beyond ARCHER2 end of service | TBC | Data will need to be transferred by users from /epsrc and /general before end of ARCHER2 service. Details will be provided to users once they are available. |

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
    [JASMIN](https://www.jasmin.ac.uk/). Please see teh JASMIN website for details
    on how to transfer data ([Globus Online](https://docs.archer2.ac.uk/user-guide/data/#data-transfer-via-globus)
    is supported for data transfers to JASMIN). If you have any questions on JASMIN
    access for your NERC-supported project, please contact the 
    [NERC HPC Team](mailto:hpc@nerc.ukri.org).