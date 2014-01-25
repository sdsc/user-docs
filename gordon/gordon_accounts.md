Gordon User Guide: Managing Your Accounts
=========================================
The show_accounts command lists the accounts that you are authorized to use, together with a summary of the used and remaining time.

    [user@gordon-ln ~]$ show_accounts
    ID name   project   used          available    used_by_proj
    ---------------------------------------------------------------
    <user> <project> <SUs by user> <SUs available> <SUs by proj> 

To charge your job to one of these projects replace  << project >> with one from the list and put this PBS directive in your job script:

    #PBS -A <project>

Many users will have access to multiple accounts (e.g. an allocation for a research project and a separate allocation for classroom or educational use). On some systems a default account is assumed, but please get in the habit of explicitly setting an account for all batch jobs. Awards are normally made for a specific purpose and should not be used for other projects.

Adding users to an account
--------------------------
Project PIs and co-PIs can add or remove users from an account. To do this, log in to your XSEDE portal account and go to the [add user page](https://www.xsede.org/group/xup/add-remove-user).

Charging
--------
The charge unit for all SDSC machines, including Gordon, is the SU (service unit) and corresponds to the use of one compute core for one hour. Keep in mind that your charges are based on the resources that are tied up by your job and don't necessarily reflect how the resources are used.

Unlike some of SDSC's other major compute resources, Gordon does not provide for shared use of a compute node. A serial job that requests a compute node for one hour will be charged 16 SUs (16 cores x 1 hour), regardless of how the processors-per-node parameter is set in the batch script.

The large memory vSMP nodes on the other hand will be charged only for the cores they request.  Be sure to request as many cores as your job needs, but be aware that if you request all 256 cores within a vSMP node your job will be charged at the rate of 256 SUs per hour.
