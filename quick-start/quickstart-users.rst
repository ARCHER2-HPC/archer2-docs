Quickstart for users
====================

.. warning::

  The ARCHER2 Service is not yet available. This documentation is in
  development.

This guide aims to quickly enable new users to get up and running on 
ARCHER2 by running through the process of getting an ARCHER account,
logging in and running your first job.

Request an account on ARCHER
The first step is to sign up for an account on the ARCHER SAFE Website. This account is used to manage your user accounts and report on your usage and quotas. The video below demonstrates how to do this.


Once you have a SAFE account you will need to request a user account on ARCHER itself. To do this you will require a Project Code (and occaisionally Project Password); you usually obtain these from the Principle Investigator (PI) or project manager for the project you will be working on. The video below demonstrates the process of requesting a user account.


The PI or project manager of project will be asked to approve your request. After your request has been approved, the systems team will create the account, and when this has been done, you will receive an email. You can then come back to SAFE and pick up the initial, one use password for your new account (ARCHER account passwords are also sometimes referred to as LDAP passwords by the system).


Collect your ARCHER password
Login to SAFE at: https://www.archer.ac.uk/safe/
Go to section "Your user accounts".
Choose the relevant account and click "View"
This will display details of your account. In the section labelled "Password", enter your SAFE password and click "View" again, and you will see your password for ARCHER.
This password is generated randomly by the software. It's best to copy-and-paste it across when you log in to the service machine. After you login, you will be prompted to change it. You should enter this password again, and then you will be prompted for your new, easy-to-remember password.

Note: when you change your password on the service machine in this way, this is not reflected on the SAFE.


Quick Start Screencast
A screencast version of the Quick Start:


Login to ARCHER
To log into ARCHER you should use the "login.archer.ac.uk" address:

ssh [userID]@login.archer.ac.uk
More information on connecting to ARCHER is available in the User Guide.


File systems and manipulating data
ARCHER has a number of different file systems mounted and understanding the difference between them is crucial to being able to use the system. In particular, transferring and moving data often requires a bit of thought in advance to ensure that the data is secure and in a useful form.

ARCHER file systems are:

/home: backed up for disaster recovery purposes only, data recovery for accidental deletion is not supported. NFS, available on login and service nodes.
/work: not backed-up. Lustre, available on login, service and compute nodes.
UK-RDF: backed up for disaster recovery purposes only, data recovery for accidental deletion is not supported. GPFS, available on login nodes (and serial nodes).
Top tips for managing data on ARCHER:

Do not generate huge (>1000) numbers of files in a single directory
Archive directories or large numbers of files before moving them between file systems (e.g. using tar)
When using tar or rsync between file systems mounted on ARCHER avoid using the compression options as these slow operations down (as file system bandwidth is generally better than throttling by CPU performance by using compression).
Think about automating the combination and transfer of multiple files output by software on ARCHER to the UK-RDF. The Data Management Guide linked below provides examples of how to automatically verify the integrity of an archive and examples of how to do this.
Much of the performance difference on transferring data is due to numbers of files involved in the transfer. You should ensure that your work flow is set up so that you do not generate huge (>1000) numbers of files in a single directory

Information on best practice in managing you data is available in our Data Management Guide:

Data Management Guide
Write your first program on ARCHER
Open a text file on the system using your favourite text editor called "hello_world.f90". For example, using vi:

auser@eslogin01:~> vi hello_world.f90
Now copy and paste the source code below into the file and save it.

! Example Hello World program
program hello_world
use mpi
implicit none

! Set up the variables
integer :: irank, nrank
integer :: iout, ierr
character(len=5) :: cirank
character(len=30) :: filename

! Initialize MPI and get my rank and total
call mpi_init(ierr)
call mpi_comm_rank(MPI_COMM_WORLD, irank, ierr)
call mpi_comm_size(MPI_COMM_WORLD, nrank, ierr)

! Set the filename from this process and open for writing
write(cirank, "(I0.5)") irank
filename = "output"//cirank//".out"
iout = 10 + irank
open(unit=iout, file=filename, form="FORMATTED")

! Write the output
write(iout,'(A,I5,A,I5)') "Hello from ", irank, " of ", nrank

! Close the output file and finalize MPI
close(iout)
call mpi_finalize(ierr)

end program hello_world

Compile your first program
Now use the Fortran compiler wrapper command "ftn" to compile the code:

auser@eslogin01:~> ftn -o hello_world.x hello_world.f90
Note: for C programs you would use the "cc" command and for C++ programs you would use the "CC" command.

More information on compilers on ARCHER is available in the User Guide.


Create a job submission script
To run a program on the ARCHER compute nodes you need to write a job submission script that tells the system how many compute nodes you want to reserve and for how long. You also need to use the "aprun" command to tell ARCHER how to place the parallel processes and threads onto the cores you have reserved.

More information on job submission and process/thread placement on ARCHER is available in the User Guide.

Parallel jobs on ARCHER should be run from the /work filesystem as /home is not mounted on the compute nodes - you will see a chdir error if you try to run a job from the /home filesystem.

Create a job submission script called "submit.pbs" in your space on the work filesystem using your favourite text editor. For example, using vi:

auser@eslogin01:~> cd /work/z01/z01/auser
auser@eslogin01:/work/z01/z01/auser> vi submit.pbs
(You will need to use your project code and username to get to the correct directory. i.e. replace the "z01" above with your project code and replace the username "auser" with your ARCHER username.

Paste the following text into your job submission script, replacing ENTER_YOUR_BUDGET_CODE_HERE with your budget code e.g. e99-ham.

#!/bin/bash --login

#PBS -N hello_world
#PBS -l select=1
#PBS -l walltime=0:5:0
#PBS -A ENTER_YOUR_BUDGET_CODE_HERE

# This shifts to the directory that you submitted the job from
cd $PBS_O_WORKDIR

aprun -n 24 $HOME/hello_world.x

cat output*.out > helloworld.out
The bolt job submission script creation tool can be used to automatically create job submission scripts with the correct options and parameters. See:

bolt: Job submission script creation tool
You can also use the checkScript command to check any job scripts you have written for correctness before you submit them. See:

checkScript: Script validation tool

Submit your job to the queue
You submit your job to the job submission using the "qsub" command:

auser@eslogin01:/work/z01/z01/auser> qsub submit.pbs
72136.sdb
The value retuned is your Job ID.


Monitoring your job
You use the "qstat" command to examine jobs in the queue. Use:

auser@eslogin01:/work/z01/z01/auser> qstat -u $USER
To list all the jobs you have in the queue. PBS will also provide an estimate of the start time for any queued jobs that the system is actively scheduling for by adding the "-T" option:

auser@eslogin01:/work/z01/z01/auser> qstat -Tu $USER
Note: the majority of jobs will not have an estimated start time as the system will be aiming to schedule them in an opportunistic manner (i.e. as soon as resources become available).

To see more details about the queued job, Use:

auser@eslogin01:/work/z01/z01/auser> qstat -f $JOBID
If your job does not enter a running state in the queues, this option may be useful as it contains a Comment field which may explain the reason why.

You can use the checkQueue utility to access information on all your jobs quickly, see:

Using checkQueue

Checking the output from the job
The job submission script above should write the output to a file called "helloworld.out", you can check this with the "cat" command. If the job was successful you should see output that looks something like:

auser@eslogin01:/work/z01/z01/auser> cat helloworld.out
Hello from     0 of    24
Hello from     1 of    24
Hello from     2 of    24
Hello from     3 of    24
Hello from     4 of    24
Hello from     5 of    24
Hello from     6 of    24
Hello from     7 of    24
Hello from     8 of    24
Hello from     9 of    24
Hello from    10 of    24
Hello from    11 of    24
Hello from    12 of    24
Hello from    13 of    24
Hello from    14 of    24
Hello from    15 of    24
Hello from    16 of    24
Hello from    17 of    24
Hello from    18 of    24
Hello from    19 of    24
Hello from    20 of    24
Hello from    21 of    24
Hello from    22 of    24
Hello from    23 of    24
If something has gone wrong, you will find any error messages in the file "hello_world.e[jobID]".

Acknowledging ARCHER
You should use the following phrase to acknowledge ARCHER in all reseach outputs that have used the facility:

This work used the ARCHER UK National Supercomputing Service (http://www.archer.ac.uk).

You should also tag outputs with the keyword ARCHER whenever possible.

Useful Links
Links to other documentation you may find useful:

ARCHER User Guide - Covers basic use of ARCHER: e.g. compilation, running jobs and using Python
ARCHER Best Practice Guide - Covers optimisation, debugging, performance monitoring and other advanced topics.
UK-RDF User Guide - Covers using the UK Research Data Facility including the Data Analytic Cluster and the Data Transfer Nodes.