Trestles User Guide: User Environment
=====================================
The Environment Modules package provides for dynamic modification of your shell environment. Module commands set, change, or delete environment variables, typically in support of a particular application. They also let the user choose between different versions of the same software or different combinations of related codes.

For example, if the Intel module and mvapich module are loaded and the user compiles with mpif90, the generated code is compiled with the Intel Fortran 90 compiler and MPI libraries using the MVAPICH implementation are linked.

Several modules that determine the default Trestles environment are loaded at login time.

Useful Modules Commands
-----------------------
Here are some common module commands and their descriptions:

Command                         |Description
--------------------------------|------------------------------
module list                     |List the modules that are currently loaded
module avail                    |List the modules that are available
module display <module_name>    |Show the environment variables used by <module name> and how they are affected
module unload <module name>     |Remove <module name> from the environment
module load <module name>       |Load <module name> into the environment
module swap <module one> <module two>   |Replace <module one> with <module two> in the environment

Loading and unloading modules
-----------------------------
You must remove some modules before loading others.

Some modules depend on others, so they may be loaded or unloaded as a consequence of another module command. For example, if intel and openmpi are both loaded, running the command module unload intel will automatically unload openmpi. Subsequently issuing the module load intel command does not automatically reload openmpi.

Complete documentation is available in the module(1) and modulefile(4) manpages.

module: command not found
-------------------------
If you get the error module: command not found this is caused by a problem with the module environment when switching from your default shell to a different shell, e.g., from bash to ksh, or csh to bash, etc. If this error is displayed, run the command:

    . /etc/profile.d/modules.sh

Allocations
-----------
Users can find their remaining Service Units (SUs) balance by running the command:

    [user@trestles-login ~]$ show_accounts
    ID name    project           used         available     used_by_proj
    ----------------------------------------------------------------------------
    <user>   <project>   <SUs used by user>  <SUs available> <SUs used by project>
