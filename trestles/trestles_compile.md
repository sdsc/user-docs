Trestles User Guide: Compiling
==============================
Trestles provides the Portland Group (PGI), Intel, and GNU compilers. MPI implementations provided are mvapich2 and openMPI. The majority of libraries are compiled for PGI and mvapich2 support, which have been shown to give optimal performance on the system. As such, they are loaded by default in the login shell. Intel and GNU compilers should only be used for compatibility if necessary. Similarly mvapich2 should be used over OpenMPI whenever possible.

Using the PGI Compilers
-----------------------
The PGI compilers should be loaded automatically in your environment; however, if you've modified your %7e/.bashrc or ~/.cshrc you can reload the PGI compilers via:
    [user@trestles ~]$ cat .bashrc 
    ...
    module load pgi mvapich2

And source the environment:

    [user@trestles ~]$ source ~/.bashrc
.

        | Serial    | MPI       | OpenMP
--------|-----------|-----------|----------------------
Fortran | pgf90     | mpif90    | pgf90 -mp
C       | pgcc      | mpicc     | pgcc -mp
C++     | pgCC      | mpicxx    | pgCC -mp
 
Using the Intel Compilers
-------------------------
The Intel compilers can be loaded by modifying your %7e/.bashrc or ~/.cshrc to include the line:

    [user@trestles ~]$ cat .bashrc 
    ...
    module purge
    module load intel mvapich2

And source the environment:

    [user@trestles ~]$ source ~/.bashrc
.

        | Serial    | MPI       | OpenMP
--------|-----------|-----------|----------------------
Fortran | ifort     | mpif90    | ifort -openmp
C       | icc       | mpicc     | icc -openmp
C++     | icpc      | mpicxx    | icpc -openmp
 
Note for C/C++ users: compiler warning - feupdateenv is not implemented and will always fail. For most users, this error can safely be ignored. By default, the Intel C/C++ compilers only link against Intel's optimized version of the C standard math library (libmf). The error stems from the fact that several of the newer C99 library functions related to floating point rounding and exception handling have not been implemented.

Using the GNU Compilers
-----------------------
The GNU compilers can be loaded by modifying your %7e/.bashrc or ~/.cshrc to include the line:

    [user@trestles ~]$ cat .bashrc 
    ...
    module purge
    module load gnu openmpi

And source the environment:

    [user@trestles ~]$ source ~/.bashrc
.

        | Serial    | MPI       | OpenMP
--------|-----------|-----------|----------------------
Fortran | gfortran  | mpif90    | gfortran -fopenmp
C       | gcc       | mpicc     | gcc -fopenmp
C++     | g++       | mpicxx    | g++ -fopenmp
