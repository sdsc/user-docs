Gordon User Guide: Running Jobs on Regular Compute Nodes
========================================================
Gordon uses the TORQUE Resource Manager, together with the Catalina Scheduler, to manage user jobs. If you’re familiar with PBS, note that TORQUE is based on the original PBS project and shares most of its syntax and user interface. Whether you run in batch mode or interactively, you will access the compute nodes using the qsub command as described below. Remember that computationally intensive jobs should be run only on the compute nodes and not the login nodes. Gordon has two queues available:

Queue Name  |   Max Walltime    Max Nodes   Comments
------------|-----------------|-----------|-------------
normal      |   48 hrs        | 64        | Used for exclusive access to regular (non-vsmp) compute nodes
vsmp        |   48 hrs        | 1         | Used for shared access to vamp node

Submitting jobs
---------------
A job can be submitted using the qsub command, with the job parameters either specified on the command line or in a batch script. Except for simple interactive jobs, most users will find it more convenient to use batch scripts.  

    $ qsub options
    $ qsub batch_script

Batch script basics
-------------------
TORQUE batch scripts consist of two main sections. The top section specifies the job parameters (e.g. resources being requested, notification details) while the bottom section contains user commands. A sample job script that can serve as a starting point for most users is shown below. Content that should be modified as necessary is between angled brackets (the brackets are not part of the code).

    #!/bin/bash
    #PBS -q normal
    #PBS -l nodes=<2>:ppn=<16>:native
    #PBS -l walltime=<1:00:00>
    #PBS -N <jobname>
    #PBS -o <my.out>
    #PBS -e <my.err>
    #PBS -A <abc100>
    #PBS -M <email_address>
    #PBS -m abe
    #PBS -V
    # Start of user commands - comments start with a hash sign (#)
    cd /oasis/scratch/$USER/temp_project
    <usercommands>

The first line indicates that the file is a bash script and can therefore contain any legal bash code. The lines starting with “#PBS” are special comments that are interpreted by TORQUE and must appear before any user commands.

The second line states that we want to run in the queue named “normal”. Lines three and four define the resources being requested: 2 nodes with 16 processors per node, for one hour (1:00:00). The next three lines (5-7) are not essential, but using them will make it easier for you to monitor your job and keep track of your output. In this case, the job will be appear as “jobname” in the queue; stdout and stderr will be directed to “my.out” and “my.err”, respectively. The next line specifies that the usage should be charged to account abc123. Lines 9 and 10 control email notification: notices should be sent to “email_address@domain.edu” when the job aborts (a), begins (b), or ends (e).

Finally, “#PBS –V” specifies that your current environment variables should be exported to the job. For example, if the path to your executable is found in your PATH variable and your script contains the line “#PBS –V”, then the path will also be known to the batch job.

The statement “cd /oasis/scratch/$USER/temp_project” changes the working directory to the directory where the job was submitted. This should always be done unless you provide full paths to all executables and input files. The remainder of the script is normally used to run your application.

Interactive jobs
----------------
There are several small but important differences between running batch and interactive jobs:

1. Interactive jobs require the -I flag
2. All node resource requests (specified using -l) must appear as a single, comma-separated list
3. To ensure obtaining nodes with flash storage, the ":flash" specifier must be included in the node properties

The following command shows how to get interactive use of one node, with flash, for 30 minutes (note the comma between "flash" and "walltime"):

    qsub -I -q normal -l nodes=2:ppn=16:native:flash,walltime=30:00 -A abc123

Monitoring and deleting jobs
----------------------------
Use the qstat command to monitor your jobs and qdel to delete a job. Some useful options are described below. For a more detailed understanding of the queues see the User Guide section Torque in Depth.

Running MPI jobs – regular compute nodes
----------------------------------------
MPI jobs are run using the mpirun_rsh command. When your job starts, TORQUE will create a PBS_NODEFILE listing the nodes that had been assigned to your job, with each node name replicated ppn times. Typically ppn will be set equal to the number of physical cores on a node (16 for Gordon) and the number of MPI processes will be set equal to nodes x ppn. Relevant lines from a batch script are shown below.

    #PBS -l nodes=2:ppn=16:native
    cd /oasis/scratch/$USER/temp_project
    mpirun_rsh –np 32 –hostfile $PBS_NODEFILE mpi_app  [command line args]

Sometimes you may want to run fewer than 16 MPI processes per node; for instance, if the per-process memory footprint is too large (> 4 GB on Gordon) to execute one process per core or you are running a hybrid application that had been developed using MPI and OpenMP. In these cases you will want to set ppn to reflect the reduced number of processes per node.

    # Running 16 MPI processes across two nodes
    #PBS -l nodes=2:ppn=8:native
    cd /oasis/scratch/$USER/temp_project
    mpirun_rsh –np 16 –hostfile $PBS_NODEFILE mpi_app [command line args]

If ppn had been set to 16 in the previous example, the PBS_NODEFILE would have contained 16 replicates of the first node followed by 16 replicates of the second node. As a result all MPI processes would run on the first node leaving the second node idle.

Running OpenMP jobs – regular compute nodes
-------------------------------------------
For an OpenMP application, set and export the number of threads. Relevant lines from a batch script are shown below.

    #PBS -l nodes=1:ppn=16:native
    cd /oasis/scratch/$USER/temp_project
    export OMP_NUMTHREADS=16
    openmp_app [command line args]

Running hybrid (MPI + OpenMP) jobs – regular compute nodes
----------------------------------------------------------
For hybrid parallel applications (MPI+OpenMP), use mpirun_rsh to launch the job and specify both the number of MPI processes and the number of threads per process. To pass environment variables through mpirun_rsh, list the key/value pairs before the executable name. The number of MPI processes should equal nodes x ppn, just as in a regular MPI job. Ideally the product of the MPI process count and the threads per process should equal the number of physical cores on the nodes being used.

Hybrid jobs will typically use one MPI process per node with the threads per node equal to the number of physical cores per node, but as the examples below show this is not required.

    # 2 MPI processes x 16 threads/node = 2 nodes x 16 cores/node = 32
    #PBS -l nodes=2:ppn=1:native
    cd /oasis/scratch/$USER/temp_project
    mpirun_rsh –np 2 –hostfile $PBS_NODEFILE OMP_NUMTHREADS=16 executable [command line args]

    # 8 MPI processes x 4 threads/node = 2 nodes x 16 cores/node = 32
    #PBS -l nodes=2:ppn=4:native
    cd /oasis/scratch/$USER/temp_project
    mpirun_rsh –np 8 –hostfile $PBS_NODEFILE OMP_NUMTHREADS=4 executable [command line args]

Network topology and communication sensitive jobs
-------------------------------------------------
The Gordon network topology is a 4x4x4 3D torus of switches with 16 compute nodes attached to each switch. For applications where the locality of the compute nodes is important, the user can control the layout of the job. Note that requesting a smaller number of maximum switch hops rather than relying on the default behavior may increase the length of time that a job waits in the queue. The maxhops option is specified in the job script as follows:

    #PBS -v Catalina_maxhops=HOPS

Where HOPS is set to the integer values 0-3 or the string None. The behavior is described below:
 * 0: only nodes connected to the same switch will be used
 * 1: only nodes connected by at most one inter-switch link will be used
 * 2: only nodes connected by at most two inter-switch links will be used
 * 3: only nodes connected by at most three inter-switch links will be used
 * None: any nodes may be used, regardless of switch proximity

To get nodes spanning the minimum number of hops for jobs of different sizes, use the following guidelines:
 * <=16 nodes: Catalina_maxhops=0 (1 switch, default for jobs up to 16 nodes)
 * <=32 nodes: Catalina_maxhops=1 (2 neighbor switches)
 * <=112 nodes: Catalina_maxhops=2 (7 switches in a star)
 * <=192 nodes: Catalina_maxhops=3 (12 switches, two stars connected)
 * >192 nodes: Catalina_maxhops=None (with wraparound, all switches are within 4 hops)

Note and hints
--------------
Try to provide a realistic upper estimate for the wall time required by your job. This will often improve your turnaround time, especially on a heavily loaded machine. Do not rely on the default values for the wall time since they tend to be quite long, vary across queues and machines, and are subject to change.
