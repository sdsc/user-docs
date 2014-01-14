Gordon User Guide: Modules
==========================
The Environment Modules package provides for dynamic modification of your shell environment. Module commands set, change, or delete environment variables, typically in support of a particular application. They also let the user choose between different versions of the same software or different combinations of related codes.

For example, if the Intel module and mvapich2_ib module are loaded and the user compiles with mpif90, the generated code is compiled with the Intel Fortran 90 compiler and linked with the mvapich2_ib MPI libraries.

Several modules that determine the default Gordon environment are loaded at login time. These include the MVAPICH implementation of the MPI library and the Intel compilers. We strongly suggest that you use this combination whenever possible to get the best performance.

Useful Modules Commands
-----------------------
Here are some common module commands and their descriptions:

Command | Description
--------|------------
module list | List the modules that are currently loaded
module avail | List the modules that are available
module display <module_name> | Show the environment variables used by <module name> and how they are affected
module unload <module name> | Remove <module name> from the environment
module load <module name> | Load <module name> into the environment
module swap <module one> <module two> | Replace <module one> with <module two> in the environment

Loading and unloading modules 
-----------------------------
You must remove some modules before loading others.

Some modules depend on others, so they may be loaded or unloaded as a consequence of another module command. For example, if intel and mvapich are both loaded, running the command module unload intel will automatically unload mvapich. Subsequently issuing the module load intel command does not automatically reload mvapich.

If you find yourself regularly using a set of module commands, you may want to add these to your configuration files (.bashrc for bash users, .cshrc for C shell users). Complete documentation is available in the module(1) and modulefile(4) manpages.

Module: command not found
-------------------------
The error message module: command not found is sometimes encountered when switching from one shell to another or attempting to run the module command from within a shell script or batch job.  The reason that the module command may not be inherited as expected is that it is defined as a function for your login shell. If you encounter this error execute the following from the command line (interactive shells) or add to your shell script (including Torque batch scripts)

    source /etc/profile.d/modules.sh
