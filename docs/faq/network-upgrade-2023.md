# ARCHER2 data centre network upgrade: 2023

During September 2023 the data centre that houses ARCHER2 will be undergoing a 
major network upgrade.

On this page we describe the impact this will have and links to further information.

If you have any questions or concerns, please
[contact the ARCHER2 Service Desk](https://www.archer2.ac.uk/support-access/servicedesk.html).

## When will the upgrade happen and how long will it take?

### The outage dates will be:

 - Start:  Monday 18th September 2023  09:00
 - Scheduled End:  Friday 22nd September 2023

We will notify users if we are able to complete this work ahead of schedule and restore 
ARCHER2 access earlier than expected.

## What are the impacts on users from the upgrade?

### During the upgrade process

- No login access
- No access to any data on the system for users
- User jobs will continue to run during the upgrade process - we provide more information on this below
- SAFE will be available during the outage - there will be reduced functionality due to the unavailability of the connection to ARCHER2 such as resetting of passwords or new account creation. 

### Submitting new work, and running work

- With no login access, it will not be possible to submit new jobs to the queues
- Jobs will continue to run, and queued jobs will be started as usual
- Serial QoS will not be available. However, serial jobs can be submitted using the standard and low-priority queues.

We will therefore be encouraging users to submit jobs in the period prior to the work, so that
your work can continue on the system during the upgrade process.


###  Relaxing of queue limits

In preparation for the Data Centre Network (DCN) upgrade we have relaxed the queue limits on all the QoS’s, so that users can submit a significantly larger number of jobs to ARCHER2. These changes are intended to allow users to submit jobs that they wish to run during the upgrade, in advance of the start of the upgrade. The changes will be in place until the end of the Data Centre Network upgrade.

For the low priority QoS, as well as relaxing the number of jobs you can submit, we have also increased the maximum job length to 48 hours and the maximum number of nodes per job to 5,860, so users can submit using their own allocation or using the low-priority QoS.

| QoS        | Max Nodes Per Job | Max Walltime | Jobs Queued | Jobs Running | Partition(s) | Notes |
| ---------- | ----------------- | ------------ | ----------- | ------------ | ------------ | ------|
| standard   | 1024               | 24 hrs       |  **320**          | 16           | standard     | Maximum of 1024 nodes in use by any one user at any time |
| highmem   | 256               | 24 hrs       |  **80**          | 16           | highmem     | Maximum of 512 nodes in use by any one user at any time |
| taskfarm   | 16               | 24 hrs       |  **640**          | 32           | standard     | Maximum of 256 nodes in use by any one user at any time |
| short      | 32                 | 20 mins      |  **80**           | 4            | standard     | |
| long       | 64                | 48 hrs       | **80**          | 16           | standard     | Minimum walltime of 24 hrs, maximum 512 nodes in use by any one user at any time, maximum of 2048 nodes in use by QoS |
| largescale | 5860               | 12 hrs        |**160**           | 1            | standard     | Minimum job size of 1025 nodes |
| lowpriority |  **5,860**               |   **48 hrs**       | **320**           | 16            | standard     | Jobs not charged but requires at least 1 CU in budget to use. |
| serial | **disabled**   | -       | -          | -           | -    | - |
| reservation | **not available**  | -       | -           | -           | -   |  |


Can we encourage users to make use of these changes, this is a good opportunity for users to queue and run a greater number of jobs than usual. The relaxation of limits on the low-priority queue also offers an opportunity to run a wider range of jobs through this queue than is normally possible.

Due to the unavailability of the DCN, users will not be able to connect to ARCHER2 via the login nodes during the upgrade. The serial QoS will be disabled during the upgrade period. However, serial jobs can be submitted using the standard and low-priority queues.
