# Data on ARCHER2 at end of service

Summary of impact and advice for data on ARCHER2 at end of service

- All data on ARCHER2 file systems will not be available in any form after end of service -
  any data you wish to keep *must* be transferred to a different location.
  + This applies to the following file systems: ARCHER2 home, ARCHER2 work, solid state scratch and RDFaaS
- Plan and start transfers early to avoid congestion close to end of service

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

- link

The key tools for transferring data off ARCHER2 are likely to be:

- [rclone]() - for parallel data transfers over SSH to remote systems, local laptops/workstations or to object store (e.g. OneDrive)
- [rsync]() - for serial data transfers over SSH to remote systems and local laptops/workstations
- [Globus Online]() - for large-scale data transfers to remote facilities with a Globus endpoint (e.g. [JASMIN]())