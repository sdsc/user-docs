Trestles User Guide: Compiling
==============================

Trestles provides the Portland Group (PGI), Intel, and GNU compilers along with multiple MPI implementations (MVAPICH2 and OpenMPI). Most applications will achieve the best performance on Trestles using the PGI compilers and MVAPICH2, and the majority of libraries installed on Trestles have been built using this combination.   Although other compilers and MPI implementations are available, we suggest using these only for compatibility purposes.

Using the PGI Compilers (Default/Suggested)
-------------------------------------------
The PGI compilers and the MVAPICH2 MPI implementation will be loaded by default.  If you have modified your environment, you can reload them by executing the following commands at the Linux prompt or by placing them in your startup file (~/.cshrc or ~/.bashrc):

    module purge
    module load gnubase pgi mvapich2_ib

For more information on the PGI compilers: [pgf90 | pgcc | pgCC] -help

        | Serial | MPI    | OpenMP        | MPI+OpenMP
--------|--------|--------|---------------|---------------
Fortran | pgf90  | mpif90 | pgf90 -mp     | mpif90 -mp
C       | pgcc   | mpicc  | pgcc -mp      | mpicc -mp
C++     | pgCC   | mpicxx | pgCC -mp      | mpicxx -mp

 
Using the Intel Compilers
-------------------------
The Intel compilers can be loaded by executing the following commands at the Linux prompt or placing them in your startup file (~/.cshrc or ~/.bashrc)

    module purge
    module load gnubase intel mvapich2_ib

For more information on the Intel compilers: [ifort | icc | icpc] -help

        | Serial | MPI    | OpenMP        | MPI+OpenMP
--------|--------|--------|---------------|---------------
Fortran | ifort  | mpif90 | ifort -openmp | mpif90 -openmp
C       | icc    | mpicc  | icc -openmp   | mpicc -openmp
C++     | icpc   | mpicxx | icpc -openmp  | mpicxx -openmp



Using the GNU Compilers
-----------------------
The GNU compilers can be loaded by executing the following commands at the Linux prompt or placing them in your startup file (~/.cshrc or ~/.bashrc)

    module purge
    module load gnubase gnu openmpi_ib


For more information on the GNU compilers: man [gfortran | gcc | g++]

        | Serial   | MPI    | OpenMP         | MPI+OpenMP
--------|----------|--------|----------------|----------------
Fortran | gfortran | mpif90 | ifort -fopenmp | mpif90 -fopenmp
C       | gcc      | mpicc  | icc -fopenmp   | mpicc -fopenmp
C++     | g++      | mpicxx | icpc -fopenmp  | mpicxx -fopenmp

Notes and hints
---------------
* The mpif90, mpicc, and mpicxx commands are actually wrappers that call the appropriate serial compilers and load the correct MPI libraries. While the same names are used for the Intel, PGI and GNU compilers, keep in mind that these are completely independent scripts.
* If you use the Intel or GNU compilers or switch between compilers for different applications, make sure that you load the appropriate modules before running your executables.
* When building OpenMP applications and moving between different compilers, one of the most common errors is to use the wrong flag to enable handling of OpenMP directives. Note that PGI, Intel, and GNU compilers use the -mp, -openmp, and -fopenmp flags, respectively.
* Explicitly set the optimization level in your makefiles or compilation scripts. Most well written codes can safely use the highest optimization level (-O3), but many compilers set lower default levels (e.g. GNU compilers use the default -O0, which turns off all optimizations).
* Turn off debugging, profiling, and bounds checking when building executables intended for production runs as these can seriously impact performance. These options are all disabled by default. The flag used for bounds checking is compiler dependent, but the debugging (-g) and profiling (-pg) flags tend to be the same for all major compilers.
