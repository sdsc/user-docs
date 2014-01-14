On-Demand Jobs
==============
Trestles has reserved a set of 5 nodes that allow critical tasks to be run on-demand as needed. These on-demand jobs allow applications that need urgent access in response to unpredictable natural or man-made events with societal impact (e.g., earthquakes, oil spills, tsunamis, severe storms, infectious disease outbreaks) to start computing without having to wait in the queue. These jobs will kill any previous jobs running on the set of specific nodes reserved for on-demand jobs.

Run at risk
-----------
All users are allowed to run their jobs "at risk" by adding the line:

    #PBS -v Catalina_run_at_risk=1

to their submit script. Run at risk jobs will be charged at a rate of 75% of normal SU usage, e.g., if the job runs for 100 CPU hours it will be charged 75 SUs. However, should a on-demand job come up, the user's job may be killed if the set of nodes are required. In this case, the user's SUs will not be refunded.

Jobs that do not specify Catalina_run_at_risk will not run on the at risk nodes, and will not be killed should a on-demand job be submitted.

The run at risk feature and discount will be enabled once on-demand usage is present on Trestles.

Running on-demand jobs
----------------------
In order to be added to the list of accounts allowed to run on-demand jobs, please send an email to help@xsede.org stating your request and justification, including why user-settable reservations are not adequate. If you do not currently have an allocation on Trestles, you should request a start-up allocation through the XSEDE User Portal for quick access, then submit a standard XSEDE allocation proposal. Once access is granted to run on-demand jobs, you may submit to the queue by adding:

    #PBS -v QOS=4

to your submit script. On-demand jobs will be charged at 3x the rate of normal SU usage (e.g., if the job runs for 100 CPU hours it will be charged 300 SUs). This is to enforce that users do not take advantage of the on-demand nodes.

While only a limited number of nodes are currently available for on-demand jobs, requests for expanding the number of nodes will be considered. For sustained, large-scale computing needs in response to societal emergencies where the on-demand queue may not be adequate, please call to discuss your needs.
