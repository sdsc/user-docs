Trestles User Guide: Storage Overview
=====================================
Home File System
---------------
After logging in, users are placed in their home directory, /home, also referenced by the environment variable $HOME. The home directory is limited in space and should be used only for source code storage. Production runs should not make use of the home file system, as it is not set up for high performance throughput. Users should keep usage on $HOME under 100GB. Backups are currently being stored on a rolling 8-week period. In case of file corruption/data loss, please contact us at help@xsede.org to retrieve the requested files.

Parallel Lustre File System
---------------------------
The parallel file system on Trestles is part of SDSC's 100 GB/s, 4 PB Data Oasis Lustre-based file system. Data Oasis consists of 64 Object Storage Servers (OSSs), with 2 10GigE connections per server (128 total) and is shared between Gordon and Trestles. The system contains project space and scratch space to be used for production runs and large scale output.

### LUSTRE PROJECT SPACE
The SDSC Projects Storage is available to all allocated users of Trestles and Gordon, and is accessible at:

    /oasis/projects/nsf/<allocation>/<user>

Where <allocation> is your 6 character allocation name, found by running show_accounts. The default allocation is 500 GB of Project Storage to be shared among all users of a project. Projects that require more than 500 GB of Project Storage must request additional space by sending an email to help@xsede.org. This e-mail from the project PI should provide a 500 words-or-less justification that includes:

1) amount of storage being requested;
2) how the storage will be used; and
3) how this augments other non-SDSC storage resources available to the project.

The projects storage requests will be reviewed by SDSC staff, and a decision will be made within 5 business days.

Project Storage is single copy, not backed up, not purged, and users should ensure that critical data are duplicated elsewhere. Space is provided on a per-project basis and is available for the duration of the associated compute allocation period. Data will be retained for 3 months beyond the end of the project, by which time data must be migrated elsewhere.

### LUSTRE SCRATCH SPACE
In order to ensure the highest levels of performance, capacity, and stability the scratch files system is subject to routine purge. We therefore strongly encourage users to monitor their usage and delete files as soon as possible, as this allows the space to be used more flexibly by all users. Users are reminded that this is scratch space and is not backed up; files that must be retained long term should be moved to alternate locations. The Trestles scratch file system is configured as follows:

    /oasis/scratch/trestles/$USER/$PBS_JOBID

This directory is created at the start of a job and users should use this space for files which require shared scratch space, or their data needs require more than 80 GB. Otherwise, the local SSD scratch space (see below) should be used. As users can only access the scratch space after the job starts, executables and other input data can be copied here from the user's home directory. Unlike SSD scratch storage, the data stored in lustre scratch is not purged immediately after job completion. Instead, users will have time after the job completion to copy back data they wish to retain to their projects directories, home directories, or to their home institution.

### Local SSD Scratch Space

Each compute node on Trestles has 120 GB of local flash storage. This storage is used to hold the operating system, software installs, and can also be used to support extremely low-latency checkpointing. The latency to the local SSD is 85 microseconds (as opposed to milliseconds for spinning disk). As Trestles will be running long jobs (up to two weeks) and for applications that support user-level checkpointing, users may want to save just a few key variables frequently to local node flash, which has a lower latency by orders of magnitude over spinning disk. Users can access the scratch space only during job execution under the directory /scratch/$USER/$PBS_JOBID. The directory is purged immediately after the job exits, therefore any desired files should be copied to either the home or parallel file system before the end of the job.

Moving data to Trestles
-----------------------
For moving larger files onto/off of Trestles, users are encouraged to make use of the free service Globus Online or the command line tool gridftp - a high-performance, secure, reliable data transfer protocol optimized for high-bandwidth, wide-area networks.

### GLOBUS ONLINE
Globus Online is a fast, reliable service for large file transfer. This free service provides a solution to data movement challenges over GridFTP by providing a robust, secure, and highly monitored environment for file transfers that has powerful yet easy-to-use interfaces. Globus Online simplifies secure data movement without requiring construction of custom end-to-end systems. Users have reported achieving up to 4-5 Gb/sec moving terabytes worth of data among DOE and other sites. For details about the service, visit https://www.globusonline.org/about. To sign up, visit https://www.globusonline.org/SignUp.

Additional information on Globus Online can be found at the XSEDE [Globus Online User Guide](https://www.xsede.org/web/guest/globus-online).

### COMMAND LINE
Trestles provides two nodes dedicated to transferring files: trestles-dm1.sdsc.edu and trestles-dm2.sdsc.edu. To use gridftp:

Install globus on the system where the data currently resides; instructions can be found at [XSEDE](https://www.xsede.org/web/guest/accessing-resources). If moving from a different XSEDE system to Trestles, globus will already be installed.

Next, retrieve a certificate from the XSEDE proxy server. You only need to enter your XSEDE username and password once. They are the same as your User Portal username and password:

    $ myproxy-logon -T -l <user>
    Enter MyProxy pass phrase:
    A credential has been received for user <user> in /tmp/x509up_uXXXXX.
    Trust roots have been installed in /home/<user>/.globus/certificates/

To transfer data to Trestles:

    globus-url-copy -cd -p 8 -stripe -vb file:///path/to/file gsiftp://trestles-dm1.sdsc.edu/oasis/projects/nsf/<allocation>/<user>/

where the options

* -cd: create destination directory if needed.
* -p 8: specify the number of parallel data connections to 8.
* -stripe: enable striping.
* -vb: during the transfer, display the number of bytes transferred and the transfer rate.
* file:///path/to/file: the source file or directory to be transferred to Trestles.
* gsiftp://trestles-dm1.sdsc.edu/oasis/projects/nsf/<allocation>/<user>/: the destination file or directory on Trestles.

More information on using globus-url-copy can be found at [www.globus.org](http://www.globus.org/).
