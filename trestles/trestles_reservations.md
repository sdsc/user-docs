Trestles User Guide: Reservations
=================================
Trestles allows users to create advanced reservations for the system. These reservations guarantee the availability of nodes for a specified amount of time. Users will need to define the number of nodes, amount of time the nodes are needed, and the window of when to create the reservation.

Reservations are charged for the duration of the reservation * number of nodes * 32 (processors per node) in addition to any jobs that are submitted that make use of the reservation (unless the reservation is cancelled 24 hours before its start time).

Creating a Reservation
----------------------
* Users are limited to 2 reservations, with up to 4 nodes per reservation up to 4 hours.

* The command to create a reservation is user_set_res. The format is:

        $ user_set_res --account=<allocation>  --nodes=<#nodes> 
            --duration=<time_in_seconds> --earliest_start=<time_MM/DD/YYYY>
            --latest_end=<time_MM/DD/YYYY> --email=<email>

    For Example:

        $ user_set_res --account=TG-XXXXXXX --nodes=2 --duration=3600 --earliest_start=10:30_03/10/2011
            --latest_end=15:00_03/10/2011 --email=user@sdsc.edu

    creates a reservation that requests 2 nodes for 1 hour starting at earliest 10:30 on March 10, 2011 and ending at the latest 15:00 March 10,2011.

Viewing your reservation
------------------------
You can find your reservation number by running:

    $ show_res | grep $USER

To view when your reservation will begin/end and which nodes you'll be assigned:

    $ show_res --res=<reservation id> --readable --start --end --node_list

Following our example from above:

    $ show_res | grep $USER
    1289412186 ... (user)
    $ show_res --res=1289412186 --readable --start --end --node_list
    ...
    ResID           StartTime               EndTime                 NodeList
    1289412186  Thu Mar 10 10:30:00 2011  Thu Mar 10 11:30:00 2011  trestles-1-32 trestles-1-4

The reservation will begin at 10:30 on March 10, 2011 and end at 11:30. The scheduler has selected nodes trestles-1-32 and trestles-1-4.

Submitting a job to a reservation
---------------------------------
If you want to run a job on the reservation, you'll need to modify your submit script. If you're running an interactive job, your command should be:

    $ qsub -I -q normal -l nodes=<#nodes>:ppn=<#ppn>,walltime=HH:MM:SS -v Catalina_res_bind=<reservation_id>,QOS=0

For the reservation we created above, the exact command would be

    $ qsub -I -q normal -l nodes=2:ppn=32,walltime=00:45:00 -v Catalina_res_bind=1289412186,QOS=0

As a submission script, the job would look like this:

    #!/bin/bash
    #PBS -q normal
    #PBS -l nodes=2:ppn=32
    #PBS -l walltime=00:45:00
    #PBS -A TG-XXXXXXX
    #PBS -v Catalina_res_bind=1289412186
    cd $PBS_O_WORKDIR
    mpirun_rsh -np 64 -hostfile $PBS_NODEFILE ./my_application

If Catalina_res_bind= is not specified, the job can run in or out of the reservation. The scheduler will try to use the reserved nodes, but may use others if the reservation can't fully contain the job. Also, it is important to note that the scheduler requires a 5 minute padding for job reservations. In the example, the longest job we could request to run in the reservation would be for 55 minutes.

Cancel Reservations
-------------------
Because the nodes you allocate during a reservation a locked to your account, you are charged for the reservation whether you run jobs on it or not. In order to cancel a reservation:

    $ user_cancel_res <reservation_id>

