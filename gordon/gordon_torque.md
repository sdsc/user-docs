Gordon User Guide: Torque in Depth
==================================

This section takes a closer look at the some of the more useful TORQUE commands. Basic information on job submission can be found in the User Guide section [Running Jobs](gordon_running.md).  For a comprehensive treatment, please see the official [TORQUE documentation](http://www.clusterresources.com/torquedocs/) and the man pages for qstat, qmgr, qalter, and pbsnodes. We also describe several commands that are specific to the Catalina scheduler and not part of the TORQUE distribution.

Listing jobs (qstat -a)
-----------------------
Running the qstat command without any arguments shows the status of all batch jobs. The -a flag is suggested since this provides additional information such as the number of nodes being used and the required time. The following output from qstat -a has been edited slightly for clarity.

    $ qstat -a
                                           Req'd  Req'd  Elap
    Job ID   Username Queue   Jobname NDS  Memory Time S Time
    -------- -------- ------  ------- --- ------ ----- - -----
    1.gordon user1    normal  g09       1    1gb 26:00 R 23:07
    2.gordon user2    normal  task1    32    --  48:00 R 32:15
    3.gordon user2    normal  task2     2    1gb 24:00 H   --
    4.gordon user3    normal  exp17     1    --  12:00 C 01:32
    5.gordon user3    normal  stats1    8    --  12:00 Q   --
    6.gordon user3    normal  stats2    8    --  12:00 E 15:27

The output is mostly self-explanatory, but a few points are worth mentioning. The Job ID listed in the first column will be needed if you want to alter, delete, or obtain more information about a job.  On SDSC resources, only the numeric portion of the Job ID is needed. The queue, number of nodes, wall time, and required memory specified in your batch script are also listed. For jobs that have started running, the elapsed time is shown. The column labeled "S" lists the job status.

* R = running
* Q = queued
* H = held
* C = completed after having run
* E = exiting after having run

Jobs can be put into a held state for a number of reasons including job dependencies (e.g. task2 cannot start until task1 completes) or a user exceeding the number of jobs that can be in a queued state.

On a busy system, the qstat output can get to be quite long. To limit the output to just your own jobs, use the -u $USER option

    $ qstat -a -u $USER
                                           Req'd  Req'd  Elap
    Job ID   Username Queue   Jobname NDS  Memory Time S Time
    -------- -------- ------  ------- --- ------ ----- - -----
    2.gordon user2    normal  task1    32    --  48:00 R 32:15
    3.gordon user2    normal  task2     2    1gb 24:00 H   --

Detailed information for a job (qstat -f jobid)
-----------------------------------------------
Running qstat -f <jobid> provides the full status for a job. In addition to the basic information listed by qstat -a, this includes the job's start time, compute nodes being used, CPU and memory usage, and account being charged.

Nodes allocated to a job (qstat -n jobid)
-----------------------------------------
To see the list of nodes allocated to a job, use qstat -n. Note that this output doesn't reflect actual usage, but rather the resources that had been requested. Knowing where your job is running is valuable information since you'll be able to access those nodes for the duration of your job to monitor processes, threads, and resource utilization.

Altering job properties (qalter)
--------------------------------
The qalter command can be used to modify the properties of a job. Note that the modifiable attributes will depend on the job state (e.g. number of nodes requested cannot be changed after a job starts running). See the qalter man page for more details.

    $ qstat -a 8
                                           Req'd  Req'd  Elap
    Job ID   Username Queue   Jobname NDS  Memory Time S Time
    -------- -------- ------  ------- --- ------ ----- - -----
    8.gordon user4    normal  task1    32    --  10:00 R  6:15
  
    $ qalter -l walltime=9:00 8
    $ qstat -a 8
                                           Req'd  Req'd  Elap
    Job ID   Username Queue   Jobname NDS  Memory Time S Time
    -------- -------- ------  ------- --- ------ ----- - -----
    8.gordon user2    normal  task1    32    --  09:00 R  6:15

Specifying job dependencies
---------------------------
In some cases, you may wish to submit multiple jobs that have dependencies. For example, a number of mutually independent tasks must be completed before a final application is run. This can be easily accomplished using the depend attribute for qsub. In the example below, the job described in pbs_script_d is not allowed to start until jobs 273, 274 and 275 have terminated, with or without errors.

    qsub -W depend=afterany:273:274:275 pbs_script_d

While this is useful, it requires the user to manually submit the jobs and construct the job list. To easily automate this process, capture the jobids using back ticks when the earlier jobs are submitted (recall that qsub writes the jobid to stdout). Using the previous example and assuming that the earlier jobs were launched using the scripts pbs_script_[abc], we can do the following:

    JOBA = `qsub pbs_script_a`
    JOBB = `qsub pbs_script_b`
    JOBC = `qsub pbs_script_c`
    qsub -W depend=afterany:$JOBA:$JOBB:$JOBC pbs_script_d

This is only a very brief introduction to controlling job dependencies. The depend attribute provides a rich variety of options to launch jobs before or after other jobs have started or terminated in a specific way. For more details, see the additional attributes section of the qsub User Guide.

Node attributes (pbsnodes -a)
-----------------------------
The full set of attributes for the nodes can be listed using pbsnodes -a. To limit output to a single node, provide the node name.

Node states (pbsnodes -l)
-------------------------
Running pbsnodes -l lists nodes that are unavailable to the batch systems (e.g. have a status of "down", "offline", or "unknown"). To see the status of all nodes, use pbsnodes -l all. Note that nodes with a "free" status are not necessarily available to run jobs. This status applies both to nodes that are idle and to nodes that are running jobs using a ppn value smaller than the number of physical cores.  This command, in combination with qstat -n <jobid>, is an easy way to identify if your job has aborted due to a node failure.
