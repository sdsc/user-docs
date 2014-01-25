Gordon User Guide: Storage Overview
===================================
Home File System
----------------
After logging in, users are placed in their home directory, /home, also referenced by the environment variable $HOME. The home directory is limited in space and should be used only for source code storage. Jobs should never be run from the home file system, as it is not set up for high performance throughput. Users should keep usage on $HOME under 100GB. Backups are currently being stored on a rolling 8-week period. In case of file corruption/data loss, please contact us at help@xsede.org to retrieve the requested files.

Parallel Lustre File System
---------------------------
SDSC provides several Lustre-based parallel file systems. These include a shared Projects Storage area that is mounted on both Gordon and Trestles plus separate scratch file systems. Collectively known as Data Oasis, these file systems are accessed through 64 Object Storage Servers (OSSs) and have a capacity of 4 PB.

### Lustre Project Space

SDSC Projects Storage has 1.4 PB of capacity and is available to all allocated users of Trestles and Gordon. It is accessible at:

    /oasis/projects/nsf/<allocation>/<user>

Where <allocation> is your 6-character allocation name, found by running show_accounts. The default allocation is 500 GB of Project Storage to be shared among all users of a project. Projects that require more than 500 GB of Project Storage must request additional space via the XSEDE POPS system in the form of a storage allocation request (for new allocations) or supplement (for existing allocations).

Project Storage is not backed up and users must ensure that critical data are duplicated elsewhere. Space is provided on a per-project basis and is available for the duration of the associated compute allocation period. Data will be retained for 3 months beyond the end of the project, by which time data must be migrated elsewhere.

### Lustre Scratch Space

The Gordon Scratch space has a capacity of 1.6 PB and is currently configured as follows. 

    /oasis/scratch/$USER/$PBS_JOBID
    /oasis/scratch/$USER/temp_project

Users cannot write directly to the /oasis/scratch/$USER directory and must instead write to one of the two subdirectories listed above.

The former is created at the start of a job and should be used for applications that require a shared scratch space or need more storage than can be provided by the flash disks.  Because users can only access this scratch space after the job starts, executables and other input data must be copied here from the user's home directory from within a job's batch script. Unlike SSD scratch storage, the data stored in Lustre scratch is not purged immediately after job completion. Instead, users will have time after the job completes to copy back data they wish to retain to their projects directories, home directories, or to their home institution.  This directory can be referred to within job scripts as $OASIS_SCRATCH.

The latter is intended for medium-term storage outside of running jobs, but is subject to purge with a minimum of five days notice if it begins to approach capacity.  We therefore strongly encourage users to monitor their usage and delete files as soon as possible, as this allows the space to be used more flexibly by all users. 

Note that both directories ($PBS_JOBID and temp_project) are part of the same file system and therefore served by the same set of OSSs. User jobs can read or write to either location with the same performance. To avoid the overhead of unnecessary data movement, read directly from temp_project rather than copying to the $PBS_JOBID directory. Similarly, files that should be retained after completion of a job should be written directly to temp_project.

SSD Scratch Space
-----------------
Both the native (non-vSMP) compute nodes and the vSMP nodes on Gordon have access to fast flash storage. Most of the native compute nodes mount a single 300 GB SSD (280 GB usable space), but some of the native nodes, labeled with the "bigflash" property, have access to a 4.4 TB SSD filesystem. The vSMP nodes have access to one or more RAID 0 devices each built from sixteen 300 GB SSDs (4.4 TB usable space).  

The latency to the SSDs is several orders of magnitude lower than that for spinning disk (<100 microseconds vs. milliseconds) making them ideal for user-level check pointing and applications that need fast random I/O to large scratch files. Users can access the SSDs only during job execution by referencing the $LOCAL_SCRATCH variable or directly accessing the following directories:

### Regular native compute nodes:

    /scratch/$USER/$PBS_JOBID

Note that the regular compute nodes have 280GB of usable space in the scratch SSD location. For jobs needing larger scratch space, the options are "bigflash" compute nodes and the vSMP nodes.

### Bigflash native compute nodes:

    /scratch/$USER/$PBS_JOBID

Additionally, users will have to change the PBS node request line:

    #PBS -l nodes=1:ppn=16:native:bigflash

If more than one bigflash node is needed for a single job, the following line also needs to be added since not all bigflash nodes necessarily share the same switch:

    #PBS -v Catalina_maxhops=None

### vSMP nodes:

    /scratch1/$USER/$PBS_JOBID (16-way and larger)

    /scratch2/$USER/$PBS_JOBID (32-way and larger)

These directories are purged immediately after the job terminates, therefore any desired files must be copied back to the parallel file system before the end of the job.

Moving data to Gordon
---------------------
For moving larger files onto/off of Gordon, users are encouraged to make use of the free Globus Online service or the gridftp command line tool - a high-performance, secure, reliable data transfer protocol optimized for high-bandwidth, wide-area networks.

### Globus Online
Globus Online is a fast, reliable service for large file transfers.  This free service provides a robust, secure, and highly monitored environment for file transfers that has powerful yet easy-to-use interfaces.  Additional information on Globus Online can be found at the XSEDE [Globus Online User Guide](https://www.xsede.org/web/guest/globus-online).

### Command Line
Gordon provides several nodes dedicated to transferring files, all of which can be accessed by connecting to oasis-dm.sdsc.edu.

To use GridFTP, both the source and destination for the transfer must have Globus installed.  All XSEDE systems (including Trestles and Gordon) already have this done, but to transfer files to your local campus machine, you may have to consult with your local systems administrator.

To initiate a transfer, first load the globus module and retrieve a certificate from the XSEDE proxy server.  You will need to enter your XSEDE username and password.

    $ module load globus
    $ myproxy-logon -T -l <XSEDE Portal username>
    Enter MyProxy pass phrase:
    A credential has been received for user <user> in /tmp/x509up_uXXXXX.
    Trust roots have been installed in /home/<user>/.globus/certificates/

Once you have this certificate, you can use it multiple times to initiate transfers.  The general syntax for the globus-url-copy command is

    globus-url-copy -cd -p 8 -stripe -vb <source file> <destination>

For example, to transfer a file called mydata.tar.gz from Stampede at TACC to your projects space on Gordon:

    globus-url-copy -cd -p 8 -stripe -vb gsiftp://gridftp.stampede.tacc.utexas.edu/work/02255/<user>/mydata.tar.gz gsiftp://oasis-dm.sdsc.edu/oasis/projects/nsf/<allocation>/<user>/

where the options

* -cd: create destination directory if needed
* -p 8: specify the number of parallel data connections to 8
* -stripe: enable striping
* -vb: during the transfer, display the number of bytes transferred and the transfer rate
* gsiftp://gridftp.stampede.tacc.utexas.edu/work/02255/<user>/mydata.tar.gz: the source file (or directory if you specify -r) to be transferred to Gordon
* gsiftp://gordon-dm.sdsc.edu/oasis/projects/nsf/<allocation>/<user>/: the destination file or directory on Gordon

More information on using globus-url-copy can be found at [www.globus.org](http://toolkit.globus.org/toolkit/docs/).
