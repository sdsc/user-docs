Trestles User Guide: Running Jobs
=================================
Trestles uses the TORQUE Resource Manager (also known by its historical name Portable Batch System (PBS)) with the Catalina Manager to define and manage job queues. TORQUE allows the user to submit one or more jobs for execution, using parameters specified in a job script. Users have two options: (a) run in batch mode, or (b) run in interactive mode. Trestles has two queues available:

Queue Name  | Limits                                        | Comments
------------|-----------------------------------------------|------------
normal      | max wallclock = 48 hours; max node count = 32 | Used for exclusive access to entire compute nodes (32 processors). This queue should be used for jobs that require at least 32 processors or large memory. Note: the max wallclock can be extended to up to 2 weeks. Send email to help@xsede.org to request this for your job.
shared      | max wallclock = 48 hours; max node count = 4  | Used for shared access to a node. This queue is useful for debugging codes or for jobs that require less than 32 processors/node. Note: the max wallclock can be extended to up to 2 weeks. Send email to help@xsede.org to request this for your job.

* If submitting to the normal queue, your job will reserve the entire node regardless of the number of processors per node you specify. As such, your job will be charged for a full node, e.g., number_of_nodes * 32 * walltime.
* If submitting to the shared queue, your job will only be charged for the number of processors per node you specify, e.g., number_of_nodes * processors_per_node * walltime.

Note: Production runs should not make use of the home file system, as it is not set up for high performance throughput. Instead, all jobs must run from /oasis/scratch/trestles/<username> or /scratch (if running from a compute node). Please refer to the storage page for more information.

Submitting Interactive Normal jobs:
-----------------------------------
Interactive jobs should be used to test/debug your programs:

    [user@trestles]$ qsub -I -q normal -l nodes=2:ppn=32,walltime=01:00:00
    [user@trestles]$ cd /path/to/executable
    [user@trestles]$ mpirun_rsh -np 32 -hostfile $PBS_NODEFILE <executable>

Submitting Interactive Shared Jobs:
-----------------------------------
    [user@trestles]$ qsub -I -q shared -l nodes=1:ppn=16,walltime=01:00:00
    [user@trestles]$ cd /path/to/executable
    [user@trestles]$ mpirun_rsh -np 16 -hostfile $PBS_NODEFILE <executable>

Batch Jobs
----------
Example Script:

    [user@trestles]$ cat script
    #!/bin/bash
    # the queue to be used.
    #PBS -q normal
    # specify your project allocation
    #PBS -A <allocation>
    # number of nodes and number of processors per node requested
    #PBS -l nodes=8:ppn=32
    # requested Wall-clock time.
    #PBS -l walltime=HH:MM:SS
    # name of the standard out file to be "output-file".
    #PBS -o job_output
    # name of the job
    #PBS -N MPI_JOB
    #PBS -V
    cd $PBS_O_WORKDIR #change to the working directory
    mpirun_rsh -np 256 -hostfile $PBS_NODEFILE <executable>

Submit the job via:

    [user@trestles]$ qsub <batch_script>

Additional Notes
----------------
The above job scripts assume the use of mvapich2. If using OpenMPI, mpirun -np <procs> -hostfile $PBS_NODEFILE <executable> should be used in place of mpirun_rsh -np <procs> -hostfile $PBS_NODEFILE <executable>

Local Disk Only
---------------
Trestles has occasional downtime for preventative maintenance on the Oasis lustre file system. During this time, the compute nodes are still available to run jobs, but without access to Data Oasis. Users that only require the home or local SSD scratch drives will still be able to run their codes *if* they add the local disk only flag to their scripts, e.g.:

    [user@trestles]$ cat script
    #!/bin/bash
    #PBS -q normal
    #PBS -l nodes=1:ppn=32
    #PBS -l walltime=HH:MM:SS
    #PBS -N local_disk_example
    #PBS -v local_disk_only=1
    cd /scratch/$USER/$PBS_JOBID
    cp /home/$USER/<executables> .
    cp /home/$USER/<inputfiles> .
    mpirun_rsh -np 32 -hostfile $PBS_NODEFILE <executable>

Hybrid Jobs
-----------
Jobs that make use of both MPI and OpenMP (hybrid jobs) will need to modify the mpi environment in order to get optimal results. Users should change their run line from:

    mpirun_rsh -np 256 -hostfile $PBS_NODEFILE <executable>

to

    mpirun_rsh -np 256 -hostfile $PBS_NODEFILE MV2_ENABLE_AFFINITY=0 <executable>

Monitoring Batch Queues
-----------------------
Users can monitor batch queues using the qstat and showq commands. Common qstat options are as follows:

Command             | Description
--------------------|-----------------------------------------------
qstat -a            | Display the status of batch jobs
qdel <pbs_jobid>    | Delete (cancel) a queued job
qstat -r            | Show all running jobs on system
qstat -f <pbs_jobid>| Show detailed information of the specified job
qstat -q            | Show all queues on system
qstat -Q            | Show queues limits for all queues
qstat -B            | Show quick information of the server
pbsnodes -a         | Show node status

View the qstat manpage for more options.
