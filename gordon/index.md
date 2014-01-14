Gordon User Guide: Technical Summary
====================================
Gordon is a dedicated XSEDE cluster designed by Appro and SDSC consisting of 1024 compute nodes and 64 I/O nodes. Each compute node contains two 8-core 2.6 GHz Intel EM64T Xeon E5 (Sandy Bridge) processors and 64 GB of DDR3-1333 memory. The I/O nodes each contain two 6-core 2.67 GHz Intel X5650 (Westmere) processors, 48 GB of DDR3-1333 memory, and sixteen 300 GB Intel 710 solid state drives. The network topology is a 4x4x4 3D torus with adjacent switches connected by three 4x QDR InfiniBand links (120 Gb/s). Compute nodes (16 per switch) and I/O nodes (1 per switch) are connected to the switches by 4x QDR (40 Gb/s). The theoretical peak performance of Gordon is 341 TFlop/s.

Technical Summary
-----------------

|System Component          | Configuration      |
|--------------------------|--------------------|
|Intel EM64T Xeon E5 Compute Nodes              |
|Sockets                   |2                   |
|Cores                     |16                  |
|Clock speed               |2.6 GHz             |
|Flop speed                |333 Gflop/s         |
|Memory capacity           |64 GB               |
|Memory bandwidth          |85 GB/s             |
|STREAM Triad bandwidth    |60 GB/s             |
|I/O Nodes                                      |
|Sockets                   |2                   |
|Cores                     |12                  |
|Clock speed               |2.67 GHz            |
|Memory capacity           |48 GB               |
|Memory bandwidth          |64 GB/s             |
|Flash memory              |4.8 TB              |
|Full System                                    |
|Total compute nodes       |1024                |
|Total compute cores       |16384               |
|Peak performance          |341 Tflop/s         |
|Total memory              |64 TB               |
|Total memory bandwidth    |87 TB/s             |
|Total flash memory        |300 TB              |
|QDR InfiniBand Interconnect                    |
|Topology                  |3D Torus            |
|Link bandwidth            |8 Gb/s (bidirectional)              |
|Peak bisection bandwidth  |TB/s (bidirectional)|
|MPI latency               |1.3 µs              |
|DISK I/O Subsystem                             |
|File Systems              |NFS, Lustre         |
|Storage capacity (usable) |1.5 PB              |
|I/O bandwidth             |100 GB/s            |

Gordon supports the XSEDE core software stack, which includes remote login, remote computation, data movement, science workflow support, and science gateway support toolkits.

Systems Software Environment
----------------------------
Software Function |  Description
-------------------|------------
Cluster Management  |Rocks
Operating System    |CentOS
File Systems    |NFS, Lustre
Scheduler and Resource Manager  |Catalina, TORQUE
XSEDE Software  |CTSS
User Environment    |Modules
Compilers   |Intel and PGI Fortran, C, C++
Message Passing |Intel MPI, MVAPICH, Open MPI
Debugger    |DDT
Performance |IPM, mpiP, PAPI, TAU

