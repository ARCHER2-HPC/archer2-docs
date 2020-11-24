# Data migration from ARCHER to ARCHER2

This short guide explains how to move data from the ARCHER service to the ARCHER2 service.

## Log on to ARCHER

[Log on to ARCHER](http://www.archer.ac.uk/documentation/user-guide/connecting.php#sec-2.1) as usual


## Use tar to combine small files 

It is quicker to tar up large numbers of small files and transfer the single resultant large file, rather than copying each file separately

    tar -czf all_my_files.tar.gz file1.txt file2.txt file3.txt

## Load up-to-date version of ssh on ARCHER

This version of ssh has been identified through testing as offering superior performance.
 
    module load ssh


## scp your file

scp from /home

Use a less heavyweight encryption cypher, e.g., via option

    scp -c aes128-gcm@openssh.com <archer home>  archer2_username@transfer.dyn.archer2.ac.uk:<path>

where <archer home> is the /home/archer_username/files 
and <path> is the path where you are placing the files.  

(Remember to replace **archer_username** and **archer2_username** with your ARCHER and ARCHER2 usernames in the example above.)

Use the address **transfer.dyn.archer2.ac.uk** in preference to login.archer2.ac.uk. The former provides a route with a higher peak bandwidth. The same ssh key pair can be used for either.

For full details of ARCHER to ARCHER2 transfers, please see  the [ssh data transfer example](../data#ssh-data-transfer-example).


