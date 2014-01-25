Gordon User Guide: Using the vSMP nodes
=======================================
Most applications will be run on the regular compute nodes, but in certain instances you will want to use the large-memory vSMP nodes:

* Serial or threaded applications requiring more than 64 GB of memory
* MPI applications requiring more than 64 GB per process
* Applications that can take advantage of a RAM file system (ramfs)

Before reading this section, we suggest that you first become familiar with the user guide sections that cover [compiling](gordon_compiling.md) and [running](gordon_running.md) jobs.

Compilation instructions are the same for the regular compute nodes and the vSMP nodes, although ScaleMP's specially tuned version of MPICH2 should be used when building MPI applications.

Jobs are submitted to the vSMP nodes using TORQUE and, with the exception of specifying a different queue name, all of the instructions given in the user guide sections describing the batch submission system still apply. In the remainder of this section, we describe the additional steps needed to make effective use of the vSMP nodes. Note that we do not provide complete scripts below, but rather just the critical content needed to run under vSMP.

Memory and core usage
---------------------
Since multiple user jobs can be run simultaneously on a single vSMP node, there has to be a mechanism in place to ensure that jobs do not use more than their fair share of the memory. In addition, jobs should run on distinct sets of physical nodes to prevent contention for resources. Our approach is to implement the following policies:

* Cores are requested in units of 16
* Memory is allocated in proportion to number of cores requested

Each physical node has 64 GB of memory, but after accounting for the vSMP overhead the amount available for user jobs is closer to 60 GB. A serial job requiring 120 GB of memory should request 32 cores.

    #PBS -l nodes=1:ppn=32:vsmp

OpenMP jobs
-----------
The example below shows a partial batch script for an OpenMP job that will use 16 threads. Note that the queue name is set to vsmp.

    #PBS -q vsmp 
    #PBS -l nodes=1:ppn=16:vsmp
    export LD_PRELOAD=/opt/ScaleMP/libvsmpclib/0.1/lib64/libvsmpclib.so
    export LD_PRELOAD=/usr/lib/libhoard.so:$LD_PRELOAD
    export PATH=/opt/ScaleMP/numabind/bin:$PATH
    export OMP_NUM_THREADS=16
    export KMP_AFFINITY=compact,verbose,0,`numabind --offset 16`
    ./openmp-app

The first export statement preloads the vSMP libraries needed for an application to run on the vSMP nodes. The second export statement is not strictly required, but is suggested since using the Hoard library can lead to improved performance particularly when multiple threads participate in dynamic memory management. The third export statement prepends the PATH variable with the location of the numabind command, while the fourth sets the number of OpenMP threads.

The final export statement requires a more in depth explanation and contains the "magic" for running on a vSMP node. Setting the KMP_AFFINITY variable determines how the threads will be mapped to compute cores. Note that it is used only for OpenMP jobs and will not affect the behavior of pThreads codes.

The assignment maps the threads compactly to cores, as opposed to spreading out across cores, provides verbose output (will appear in the job's stderr file), and starts the mapping with the first core in the set. The numabind statement looks for an optimal set of 16 contiguous cores, where optimal typically means that the cores span the smallest number of physical nodes and have the lightest load. Enclosing within the back ticks causes the numabind output to appear in place as the final argument in the definition.

For most users, this KMP_AFFINITY assignment will be adequate, but be aware that there are many options for controlling the placement of OpenMP threads. For example, to run on 16 cores evenly spaced and alternating over 32 contiguous cores, set the ppn and offset values to 32, the number of threads to 16, and the affinity type to scatter. Relevant lines are shown below.

    #PBS -l nodes=1:ppn=32:vsmp   
    export OMP_NUM_THREADS=16  
    export KMP_AFFINITY=scatter,verbose,0,`numabind --offset 32`

pThreads jobs
-------------

As mentioned in the previous section, setting the KMP_AFFINITY variable has no effect on pThreads jobs. Instead you will need to create an additional configuration file with all of the contents appearing on a single line. It is absolutely critical that the executable name assigned to the pattern is the same as that used in the script.

    $ cat config  

    (output shown on two lines for clarity)
    name=myconfig pattern=pthreads-app verbose=0 process_allocation=multi   
                    task_affinity=cpu rule=RULE-procgroup.so flags=ignore_idle  

The sleep statement in the following batch script ensures that enough time has elapsed to create all of the threads before they are bound to cores by numabind.

    #PBS -q vsmp  
    #PBS -l nodes=1:ppn=16:vsmp
    export LD_PRELOAD=/opt/ScaleMP/libvsmpclib/0.1/lib64/libvsmpclib.so
    export LD_PRELOAD=/usr/lib/libhoard.so:$LD_PRELOAD
    export PATH=/opt/ScaleMP/numabind/bin:$PATH
    ./pthreads-app &
    sleep 10
    numabind --config config 

Serial jobs
-----------
Serial job submission is very similar to that for OpenMP jobs, except that the taskset command is used together with numabind to bind the process to a core. Even though a single core will be used for the computations, remember to request a sufficient number of cores to obtain the memory required (~60 GB per 16 cores).

    #PBS -q vsmp  
    #PBS -l nodes=1:ppn=16:vsmp 
    export LD_PRELOAD=/opt/ScaleMP/libvsmpclib/0.1/lib64/libvsmpclib.so
    export PATH=/opt/ScaleMP/numabind/bin:$PATH
    taskset -c `numabind --offset 1` ./scalar-app

MPI jobs
--------
If you require less than 64 GB of memory per MPI process, we suggest that you run on the regular compute nodes. While ScaleMP does provide a specially tuned version of the MPICH2 library designed to achieve optimal performance under vSMP, the vSMP foundation software does add a small amount of unavoidable communications latency (applies not just to vSMP, but any hypervisor layer).

Before compiling your MPI executable to run under vSMP, unload the mvapich module and replace with mpich2

    $ module swap mvapich2_ib mpich2_ib
 
To run an MPI job, where the number of processes equals the number of compute cores requested, use the following options. Note that VSMP_PLACEMENT is set to PACKED, indicating that the MPI processes will be mapped to a set of contiguous compute cores.

    #PBS -q vsmp  
    #PBS -l nodes=1:ppn=32:vsmp
    export PATH=/opt/ScaleMP/mpich2/1.3.2/bin:$PATH
    export VSMP_PLACEMENT=PACKED 
    export VSMP_VERBOSE=YES 
    export VSMP_MEM_PIN=YES 
    vsmputil --unpinall 
    time mpirun -np 32 ./mpitest 

The situation is slightly more complicated if the number of MPI processes is smaller than the number of requested cores. Typically you will want to spread the MPI processes evenly across the requested cores (e.g. when running 2 MPI processes across 64 cores, the processes will be mapped to cores 1 and 33). In the example below the placement has been changed from "PACKED" to "SPREAD^2^32". The SPREAD keyword indicates that the processes should be spread across the cores and "^2^32" is a user defined topology of 2 nodes with 32 cores per node.

    #PBS -q vsmp  
    #PBS -l nodes=1:ppn=64:vsmp
    export PATH=/opt/ScaleMP/mpich2/1.3.2/bin:$PATH
    export VSMP_PLACEMENT=SPREAD^2^32 
    export VSMP_VERBOSE=YES 
    export VSMP_MEM_PIN=YES 
    vsmputil --unpinall 
    mpirun -np 2 ./mpitest 
 
Note, hints, and additional resources
-------------------------------------
We have only touched on the basics required for job submission. For additional information see the following external resources:

* [Thread Affinity Interface (KMP_AFFINITY)](http://software.intel.com/sites/products/documentation/studio/composer/en-us/2011Update/compiler_c/optaps/common/optaps_openmp_thread_affinity.htm)
* [The Hoard Memory Allocator](http://people.cs.umass.edu/~emery/hoard/hoard-documentation.html)
