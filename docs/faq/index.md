# ARCHER2 Frequently Asked Questions

This section documents some of the questions raised to the Service Desk on ARCHER2, and the advice and solutions.


## Username already in use
**Q.** I created a machine account on ARCHER2 for a training course, but now I want to use that machine username for my main ARCHER2 project, and the system will not let me, saying "that name is already in use".  How can I re-use that username.

**A.**  Send an email to the [service desk](mailto:support@archer2.ac.uk), letting us know the username and project that you set up previously, and asking for that account and any associated data to be deleted.  Once deleted, you can then re-use that username to request an account in your main ARCHER2 project.


## RDF Data
**Q.** What is happening to data on the RDF (`/epsrc` and `/general`) and how can I access data on the RDF from ARCHER2?

**A.** Data on the RDF will persist beyond the lifetime of ARCHER. Although there are plans to make the RDF data directly available on ARCHER2 in the same way as they were on ARCHER, this functionality is not available yet. For the moment, you can transfer data from the RDF to ARCHER2 using scp/rsync as you would for any other remote host. You should use the host `login.rdf.ac.uk` to access the RDF data and use your ARCHER login credentials (you need to use both an SSH key and password as you do for ARCHER and ARCHER2). More information on transferring data to ARCHER2 using scp or rsync can be found [in the ARCHER2 User and Best Practice Guide](https://docs.archer2.ac.uk/user-guide/data/).