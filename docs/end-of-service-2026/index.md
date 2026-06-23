# ARCHER2 End of Service

!!! important "Last update"
    This documentation was last updated on 23 June 2026

!!! warning "ARCHER2 End of Service"
    The ARCHER2 service is scheduled to end on 21 November 2026.
    After this date, you will no longer be able to connect to the ARCHER2 login nodes or access any data that
    was stored on the ARCHER2 home, work and solid state scratch file systems.

    RDFaaS will be available beyond the end of the ARCHER2 service but users will need to transfer data
    from current file system to it before the end of ARCHER2 - including data
    on /epsrc and /general. Detailed instructions on how to access continuing RDFaaS storage will be
    provided as soon as they are available.

!!! tip "NERC-supported projects can use JASMIN for data storage"
    NERC-supported projects have been granted access to data storage resources on
    [JASMIN](https://www.jasmin.ac.uk/). Please see teh JASMIN website for details
    on how to transfer data ([Globus Online](https://docs.archer2.ac.uk/user-guide/data/#data-transfer-via-globus)
    is supported for data transfers to JASMIN). If you have any questions on JASMIN
    access for your NERC-supported project, please contact the 
    [NERC HPC Team](mailto:hpc@nerc.ukri.org).

!!! important "Do not leave data transfer too late"
    Do not leave data transfer too late or you may find you do not have time to overcome 
    performance bottlenecks or other issues.

These pages provide information for users on the end of the ARCHER2 
UK National Supercomputing Service. They will be updated with information
as more details become available.

This documentation currently covers:

- [Timeline for end of ARCHER2 service](#timeline-overview)
    + When will login access end?
    + When will job submission end?
    + When will data access end?
- [Data on ARCHER2](data.md)
    + What happens to the data on the ARCHER2 file systems at the end of service?
    + What about data on RDFaaS file systems?
    + Is there storage available on ARCHER2 where I can store data beyond ARCHER2 lifetime?
    + How can I transfer data off ARCHER2 efficiently and how long will it take?
- [EPCC SAFE](safe.md)
    + What will happen to the personal data I have stored on SAFE?
    + Will I be able to access my historical ARCHER2 use data in SAFE beyond the end of the service?
- [HPC beyond ARCHER2](next-services.md)
    + What other HPC services can I apply for access to for my work?
    + What if my ARCHER2 allocation extends beyond the end of the service?
- [Getting help](support.md)
    + Where can I ask questions about the end of the ARCHER2 service?

## Timeline overview

The table below gives more detail on the ARCHER2 end of service timeline. More precise timings
will be added closer to the end of service.

| Item | Time/Date | Notes |
|---|---|---|
| End of home, work, solid state scratch file system access | 21 Nov 2026 | There will be no access to any data on these file systems beyond the end of the ARCHER2 service. |
| End of job submission | 21 Nov 2026 | While job submission will be available to end of service, you will not be able to access data produced by jobs that run to the end once the service has ended. |
| End of login access | 21 Nov 2026 |  |
| RDFaaS | Continues beyond the end of ARCHER2 service | Data will need to be transferred by users from /epsrc and /general to new local mount location before end of ARCHER2 service. Details will be provided to users once they are available. |

## Summary of user impacts

After the end of the ARCHER2 service:

- Data on home, work, solid state scratch file systems will not be accessible beyond the end of the ARCHER2 service
- RDFaaS will continue beyond the lifetime of ARCHER2 but users will need to transfer data from /epsrc and /general to a different local mount point on ARCHER2 - more details will be provided as soon as they are available
- No login access will be available beyond the end of the ARCHER2 service
- Personal data in SAFE will be retained for 2 years then deleted as per the SAFE Personal Data and Privacy Policy
- SAFE access will continue to be available for you to access historic ARCHER2 use data
- Academic projects with ARCHER2 access that extend beyond the end of service have been contacted by UKRI about continued access to HPC facilities
- Industry projects with ARCHER2 access that extend beyond the end of service have been contacted by EPCC about continued access to HPC facilities
- ARCHER2 website will continue to be available beyond the end of the ARCHER2 service

!!! note "RDFaaS: data storage beyond the end of ARCHER2"
    RDFaaS will be available beyond the end of the ARCHER2 service but users will need to transfer data
    on /epsrc and /general to an updated local mount path before the end of the ARCHER2 service. Detailed
    instructions on how to access continuing RDFaaS storage will be
    provided as soon as they are available but you should expect the capacity of this storage to be a
    fraction of current quotas on the ARCHER2 work file system. You should not plan for this storage
    system to be able to store the majority of data you have on ARCHER2.

## Recommended actions

We strongly recommend that users and projects review any data held on ARCHER2 file systems and
transfer data you wish to keep to a different location  as soon as possible.

!!! important "Do not leave data transfer too late"
    Do not leave data transfer too late or you may find you do not have time to overcome 
    performance bottlenecks or other issues.

The documentation contains useful advice on tools and techniques for data transfer:

- [Archiving and Data Transfer](https://docs.archer2.ac.uk/user-guide/data/#archiving-and-data-transfer)

You may also be able to access the new *Research Data Facility (RDF)* hardware to store data beyond the
lifetime of ARCHER2 for use on other UK national HPC facilities. We will add more information here
as it becomes available.

If you have any questions about transferring data, please [contact the ARCHER2 Service Desk](https://www.archer2.ac.uk/support-access/servicedesk.html).
