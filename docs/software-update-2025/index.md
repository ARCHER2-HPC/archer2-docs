# Software Update 2025

This page describes the impact of the update to ARCHER2 system software happening on
16 October 2025.

If you encounter any issues after the update, you should
[contact the service desk](https://www.archer2.ac.uk/support-access/servicedesk.html).

## User impact summary

- Users *should not* need to recompile software on updated ARCHER2
- The default Cray Programming Environment (CPE) will change from 22.12 to 23.09
- No changes are required to job submission scripts or module commands
- There will be a short interruption (5-10 mins) of login access on 16 Oct 2025
- There is no impact on file system access or data stored on the ARCHER2 file systems

## What is happening?

The system software on all user-facing ARCHER2 nodes (login nodes, data analysis nodes,
and compute nodes) is being updated to a version of SUSE Linux Enterprise Server
(SLES 15 SP4) that is decoupled from the HPE Cray sources. The HPE Cray Programming
Environment (CPE) is being updated from 22.12 to 23.09.

## Why is this happening?

This change is to allow ARCHER2 to continue to receive critical security updates
for the remainder of its lifetime.

## Description of user-facing changes and impacts

* OS version: Nodes will be updated to use SLES 15 SP4 direct from SLES sources
  instead of SLES 15 SP4 from the HPE Cray sources

    * As the base OS version is the same, software recompilation is not generally
     needed. This has been verified by extensive testing by ARCHER2 CSE service at
     EPCC and HPE
    * If users see issues with software following the update, we will ask them to
     recompile in the first instance to eliminate this as a potential issue

* Default CPE version changed from 22.12 to 23.09

    * This change also does not require recompilation from users
    * The old default version (22.12) is still available via `module load cpe/22.12`.
     [Documentation on using non-default CPE versions is available](https://docs.archer2.ac.uk/user-guide/dev-environment/ #switching-to-a-different-hpe-cray-programming-environment-cpe-release)

<!-- * New version (1.20.1) of `libfabric` available but default is not changing
    * libfabric 1.20.1 is available via `module swap libfabric libfabric/1.20.1`
    * The default version remains at libfabric 1.12.1.2.2.0.0
    * In testing, some applications saw performance improvements with the new
     libfabric while others experienced worse performance -->
