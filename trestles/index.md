Trestles User Guide: Technical Summary
======================================
Trestles is a dedicated XSEDE cluster designed by Appro and SDSC consisting of 324 compute nodes. Each compute node contains four sockets, each with a 8-core 2.4 GHz AMD Magny-Cours processor, for a total of 32 cores per node and 10,368 total cores for the system. Each node has 64 GB of DDR3 RAM, with a theoretical memory bandwidth of 171 Gb/s. The compute nodes are connected via QDR InfiniBand interconnect, fat tree topology, with each link capable of 8 Gb/s (bidrectional). Trestles has a theoretical peak performance of 100 TFlop/s.

System Component        | Configuration
------------------------|-----------------------------------
AMD Magny-Cours Compute Nodes |
                        |
Sockets                 | 4
Cores                   | 32
Clock speed             | 2.4 GHz
Flop speed              | 307 Gflop/s
Memory capacity         | 64 GB
Memory bandwidth        | 171 GB/s
STREAM Triad bandwidth  | 100 GB/s
Flash memory (SSD)      | 120 GB
Full System             |
Total compute nodes     | 324
Total compute cores     | 10,368
Peak performance        | 100 Tflop/s
Total memory            | 20.7 TB
Total memory bandwidth  | 55.4 TB/s
Total flash memory      | 39 TB
QDR InfiniBand Interconnect     |
Topology                | Full-bisection fat tree
Link bandwidth          | 8 Gb/s (bidirectional)
Peak bisection bandwidth| 5.2 Tb/s (bidirectional)
MPI latency             | 1.3 µs
DISK I/O Subsystem      |
File Systems            | NFS, Lustre
Storage capacity (usable)|  4 PB
I/O bandwidth           | 8 GB/s

Systems Software Overview
-------------------------

Software Function       | Description
------------------------|-----------------------------------
Cluster Management      | Rocks
Operating System        | CentOS
File Systems            | NFS, Lustre
Scheduler and Resource Manager |  Catalina, TORQUE
XSEDE Software          | CTSS
User Environment        | Modules
Compilers               | Intel, PGI, and GNU; Fortran, C, C++
Message Passing         | MVAPICH2, OpenMPI
Debugger                | DDT
Performance             | IPM, PAPI, TAU

