Gordon User Guide: System Access
--------------------------------
As an XSEDE computing resource, Gordon is accessible to XSEDE users who are given time on the system. In order to get an account, users will need to submit a proposal through the XSEDE User Portal [https://portal.xsede.org].

Interested users may contact SDSC User Support for assistance with applying for time on Gordon (see sidebar for contact information).

Logging in to Gordon
--------------------
Gordon supports Single Sign On through the XSEDE User Portal and from the command line using an XSEDE-wide password. To login to Gordon from the command line, use the hostname:

    gordon.sdsc.edu 

The following are examples of Secure Shell (ssh) commands that may be used to log in to Gordon:

    ssh <your_username>@gordon.sdsc.edu
    ssh -l <your_username> gordon.sdsc.edu 

Notes and hints
---------------
When you login to gordon.sdsc.edu, you will be assigned one of the four login nodes gordon-ln[1-4].sdsc.edu. These nodes are identical in both architecture and software environment. Users should normally login to gordon.sdsc.edu, but can directly access one of the four nodes directly if they see poor performance.

Secure shell users should feel free to append their public RSA key to their ~/.ssh/authorized_keys file to enable access from authorized hosts without having to enter their password.

Do not use the login nodes for computationally intensive processes.  These nodes are meant for compilation, file editing, simple data analysis, and other tasks that use minimal compute resources. All computationally demanding jobs should be submitted and run through the batch queuing system.
