Trestles User Guide: System Access
==================================
As an XSEDE computing resource, Trestles is accessible to XSEDE users who are given time on the system. In order to get an account, users will need to submit a proposal through the [XSEDE Allocation Request System](https://portal.xsede.org/group/xup/allocations-overview). Interested users may contact SDSC User Support for assistance on applying for time on Trestles (see sidebar for contact information).

In order to serve a large number of moderate-scale users and improve turnaround time, Trestles limits the number of cores per job to 1,024, caps the allocation per individual project to 1.5M SUs/year (gateway projects are exempt from this cap), and limits startup allocations to 50k SUs.

Logging in to Trestles
----------------------
Trestles supports Single Sign On through the XSEDE User Portal and from the command line using a XSEDE-wide password. Users wanting to SSH into trestles directly will need to first receive a local Trestles password by completing the form.

To log in to Trestles from the command line, use the following hostname:

    trestles.sdsc.edu

The following are examples of Secure Shell (ssh) commands that may be used to log in to Trestles:

    ssh <your_username>@trestles.sdsc.edu
    ssh -l <your_username> trestles.sdsc.edu
