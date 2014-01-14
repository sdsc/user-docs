Gordon User Guide: Monitoring Your Job
======================================
In this section, we describe some standard tools that you can use to monitor your batch jobs. We suggest that you at least familiarize yourself with the section of the user guide that deals with [running jobs](gordon_running.md) to get a deeper understanding of the batch queuing system before starting this section.

Figuring out where your is job running
--------------------------------------
Using the qstat –n jobid command, you’ll be able to see a list of nodes allocated to your job. Note that this output doesn’t reflect actual usage, but rather the resources that had been requested. Knowing where your job is running is valuable information since you’ll be able to access those nodes for the duration of your job to monitor processes, threads, and resource utilization.

The output from qstat –n shown below lists the nodes that have been allocated for job 362256. Note that we have 16 replicates of the node gcn-5-22, each followed by a number corresponding to a compute core on the node.

    [sinkovit@gordon-ln1 ~]$ qstat -n 362256
    gcn-5-22/15+gcn-5-22/14+gcn-5-22/13+gcn-5-22/12
    +gcn-5-22/11+gcn-5-22/10+gcn-5-22/9+gcn-5-22/8
    +gcn-5-22/7+gcn-5-22/6+gcn-5-22/5+gcn-5-22/4
    +gcn-5-22/3+gcn-5-22/2+gcn-5-22/1+gcn-5-22/1

Connecting to a compute node
----------------------------
Under normal circumstances, users cannot login directly to the compute nodes. This is done to prevent users from bypassing the batch scheduler and possibly interfering with other jobs that may be running on the nodes.

    $ ssh gcn-5-22
    Connection closed by 10.1.254.187
 
Access is controlled by the contents of the /etc/security/access.conf file, which for idle nodes denies access to everyone except root and other administrative users.

    $ cat /etc/security/access.conf 
    -:ALL EXCEPT root diag :ALL
 
The situation is different though once a batch job transitions from the queued state to the run state. A special script known as the prologue makes a number of changes to the compute environment, including altering the access.conf file so that the owner of the job running on the node can login.

    $ cat /etc/security/access.conf 
    -:ALL EXCEPT root diag username:ALL 
 
Once the job terminates, a corresponding script known as the epilogue undoes the changes made by the prologue. If you happen to be logged in to a compute node when your job terminates, your session will be terminated.

Static view of threads and processes with ps
--------------------------------------------
Once you login into a compute node, the ps command can provide information about current processes. This is useful if you want to confirm that you are running the expected number of MPI processes, that all MPI processes are in the run state, and that processes are consuming relatively equal amounts of CPU time. The ps command has a large number of options, but two that are suggested are –l (long output) and –u user (restrict output to processes owned by username or user ID).

In the following example, we see that the user is running 16 MPI processes, named pmemd.MPI in the CMD column, on the compute node gcn-5-22. The TIME column shows that the processes are all using very nearly the same amount of CPU time and the S (state) column shows that these are all in the run (R) state. Not all applications will exhibit such ideal load balancing and processes may sometimes go into a sleep state (D or S) while performing I/O or waiting for an event to complete.

    [diag@gcn-5-22 ~]$ ps -lu username
    F S   UID   PID  PPID  C PRI  NI ADDR SZ  WCHAN  TTY    TIME CMD
    5 S 503412 23686 23684  0 76   0 - 23296  stext  ?  00:00:00 sshd
    0 S 503412 23689 23686  0 78   0 - 16473  wait   ?  00:00:00 bash
    0 S 503412 23732 23689  0 75   0 -  6912  stext  ?  00:00:00 mpispawn
    0 R 503412 23735 23732 99 85   0 - 297520 stext  ?  12:40:50 pmemd.MPI
    0 R 503412 23736 23732 99 85   0 - 213440 stext  ?  12:40:55 pmemd.MPI
    0 R 503412 23737 23732 99 85   0 - 223276 stext  ?  12:40:54 pmemd.MPI
    0 R 503412 23738 23732 99 85   0 - 255149 stext  ?  12:40:55 pmemd.MPI
    [ --- 12 additional lines very similar to previous line not shown --- ]
 
The ps command with the m (not –m) option gives thread-level information, with the summed CPU usage for the threads in each process followed by per-thread usage. In the examples below, we first run ps to see the five MPI processes, then use the m option to see the six threads per process.

    [diag@gcn-4-28 ~]$ ps -lu cipres # Processes only
    F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY    TIME CMD
    0 R 503187 22355 22354 99 85   0 - 245402 stext ?  06:05:46 raxmlHPC
    0 R 503187 22356 22354 99 85   0 - 246114 stext ?  06:05:47 raxmlHPC
    0 R 503187 22357 22354 99 85   0 - 245325 stext ?  06:05:47 raxmlHPC
    0 R 503187 22358 22354 99 85   0 - 222089 stext ?  06:05:47 raxmlHPC
    0 R 503187 22359 22354 99 85   0 - 245630 stext ?  06:05:47 raxmlHPC
 
    [diag@gcn-4-28 ~]$ ps m -lu cipres # Processes and threads
    F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY    TIME CMD
    0 - 503187 22355 22354 99  -   - - 245402 -     ?    402:38 raxmlHPC
    0 R 503187    -     - 99  85   0 -     - stext  -     67:06 -
    1 R 503187    -     - 99  85   0 -     - stext  -     67:06 -
    1 R 503187    -     - 99  85   0 -     - stext  -     67:06 -
    1 R 503187    -     - 99  85   0 -     - stext  -     67:06 -
    1 R 503187    -     - 99  85   0 -     - stext  -     67:06 -
    1 R 503187    -     - 99  85   0 -     - stext  -     67:06 -
    [ --- similar output for other processes/threads not shown --- ]
 
Dynamic view of threads and processes with top
----------------------------------------------
While ps gives a static view of the processes, top can be used to provide a dynamic view. By default the output of top is updated every three seconds and lists processes in order of decreasing CPU utilization. Another advantage of top is that it displays the per-process memory usage thereby providing a very easy way to confirm that your job is not exceeding the available physical memory on a node.

In the first example below, the CPU usage is around 600% for all five processes, indicating that each process had spawned multiple threads. We can confirm this by enabling top to display thread-level information with the H option. (This option can either be specified on the top command line or entered after top is already running)

    [diag@gcn-4-28 ~]$ top # Processes only
        PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
    22356 cipres    25   0  961m 251m 2316 R 600.0  0.4 481:32.53 raxmlHPC
    22359 cipres    25   0  959m 252m 2312 R 600.0  0.4 481:31.90 raxmlHPC                                                               
    22355 cipres    25   0  958m 256m 7448 R 599.6  0.4 481:31.55 raxmlHPC                                                            
    22357 cipres    25   0  958m 249m 2300 R 599.6  0.4 481:32.17 raxmlHPC                                                            
    22358 cipres    25   0  867m 249m 2312 R 599.6  0.4 481:32.42 raxmlHPC
    [ --- Additional lines not shown ---]

    [diag@gcn-4-28 ~]$ top H # Processes and threads
        PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
    22355 cipres    25   0  958m 256m 7448 R 100.1  0.4  82:35.68 raxmlHPC                                                              
    22387 cipres    25   0  958m 256m 7448 R 100.1  0.4  82:35.70 raxmlHPC                                                              
    22388 cipres    25   0  958m 256m 7448 R 100.1  0.4  82:35.93 raxmlHPC                                                               
    22375 cipres    25   0  961m 252m 2316 R 100.1  0.4  82:36.03 raxmlHPC                                                               
    22377 cipres    25   0  961m 252m 2316 R 100.1  0.4  82:36.04 raxmlHPC 
    [ --- output for other 9 threads and other lines not shown ---] 
 
Additional Resources
--------------------
We have not covered all of the options and capabilities of qstat, ps, and top. For more information consult the man pages for these commands.
