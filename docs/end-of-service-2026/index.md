# ARCHER2 End of Service

!!! important "Last update"
    This documentation was last updated on 19 August 2026

!!! warning "ARCHER2 End of Service"
    The ARCHER2 service is scheduled to end on 21 November 2026.
    After this date, you will no longer be able to connect to the ARCHER2 login nodes or access any data that
    was stored on the ARCHER2 home, work and solid state scratch file systems.

!!! tip "RDFaaS and NSCDS: Data storage beyond the lifetime of ARCHER2"
    The RDFaaS will retain data until end of Mar 2027. We are also providing a new location for 
    projects to store limited amounts of data beyond the end of ARCHER2: the NSCDS
    (National Supercomputing Centre Data Store). Users will need to transfer data from current file systems to it before
    the end of ARCHER2. More detailed instructions on this are provided in the [Data section](data.md) of this short guide.

!!! tip "NERC-supported projects can use JASMIN for data storage"
    NERC-supported projects have been granted access to data storage resources on
    [JASMIN](https://www.jasmin.ac.uk/). Please see the JASMIN website for details
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
| RDFaaS | 31 Mar 2027 | Data on RDFaaS will be retained until 31 Mar 2027 |
| NSCDS | Starts from 3 Sep 2026 | Location for data that can accessed from other services such as Cirrus beyond the end of ARCHER2 |

## Summary of user impacts

After the end of the ARCHER2 service:

- Data on home, work, solid state scratch file systems will not be accessible beyond the end of the ARCHER2 service
- The RDFaaS will retain data until end of Mar 2027
- NSCDS will extend beyond the lifetime of ARCHER2 - users are responsible for transferring any data they wish to keep to this location
- No login access will be available beyond the end of the ARCHER2 service
- Personal data in SAFE will be retained for 2 years then deleted as per the SAFE Personal Data and Privacy Policy
- SAFE access will continue to be available for you to access historic ARCHER2 use data
- Academic projects with ARCHER2 access that extend beyond the end of service have been contacted by UKRI about continued access to HPC facilities
- Industry projects with ARCHER2 access that extend beyond the end of service have been contacted by EPCC about continued access to HPC facilities
- ARCHER2 website will continue to be available beyond the end of the ARCHER2 service

!!! tip "RDFaaS and NSCDS: Data storage beyond the lifetime of ARCHER2"
    The RDFaaS will retain data until end of Mar 2027. We are also providing a new location for 
    projects to store limited amounts of data beyond the end of ARCHER2: the NSCDS
    (National Supercomputing Centre Data Store). Users will need to transfer data from current file systems to it before
    the end of ARCHER2. More detailed instructions on this are provided in the [Data section](data.md) of this short guide.
    You should not plan for this storage to be able to host all the data you currently have on ARCHER2.

## Recommended actions

We strongly recommend that users and projects review any data held on ARCHER2 file systems and
transfer data you wish to keep to a different location  as soon as possible.

!!! important "Do not leave data transfer too late"
    Do not leave data transfer too late or you may find you do not have time to overcome 
    performance bottlenecks or other issues.

The documentation contains useful advice on tools and techniques for data transfer:

- [Archiving and Data Transfer](https://docs.archer2.ac.uk/user-guide/data/#archiving-and-data-transfer)

You may also be able to access the new *NSCDS* storage to store data beyond the
lifetime of ARCHER2 for use on other UK national HPC facilities. See the [Data section](data.md)
for more details.

If you have any questions about transferring data, please [contact the ARCHER2 Service Desk](https://www.archer2.ac.uk/support-access/servicedesk.html).
