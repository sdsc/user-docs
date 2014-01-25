Trestles User Guide: System Access
==================================
As an XSEDE computing resource, Trestles is accessible to XSEDE users who are given time on the system. In order to get an account, users will need to submit a proposal through the [XSEDE Allocation Request System](https://portal.xsede.org/group/xup/allocations-overview). 

Interested users may contact SDSC User Services for assistance with applying for time on Trestles (see sidebar for contact information).

In order to serve a large number of moderate-scale users and improve turnaround time, Trestles limits the number of cores per job to 1,024, caps the allocation per individual project to 1.5M SUs/year (gateway projects are exempt from this cap), and limits startup allocations to 50k SUs.

Logging in to Trestles
----------------------
Trestles supports Single Sign On through the XSEDE User Portal and from the command line using a XSEDE-wide password.  To log in to Trestles from the command line, use the hostname:

    trestles.sdsc.edu

The following are examples of Secure Shell (ssh) commands that may be used to log in to Trestles:

    ssh <your_username>@trestles.sdsc.edu
    ssh -l <your_username> trestles.sdsc.edu

Notes and hints
---------------
When you login to trestles.sdsc.edu, you will be assigned one of the two login nodes trestles-login[1-2].sdsc.edu. These nodes are identical in both architecture and software environment. Users should normally login to trestles.sdsc.edu, but can directly access one of the two nodes directly if they see poor performance.

You should feel free to append their public RSA key to their ~/.ssh/authorized_keys file to enable access from authorized hosts without having to enter your password.

Do not use the login nodes for computationally intensive processes.  These nodes are meant for compilation, file editing, simple data analysis, and other tasks that use minimal compute resources. All computationally demanding jobs should be submitted and run through the batch queuing system.
