Gordon User Guide: Dedicated I/O nodes (Gordon ION)
===================================================
A limited number of Gordon’s 64 flash-based I/O nodes are available as dedicated resources. The PI and designated users of a dedicated I/O node have exclusive use of the resource at any time for the duration of the award without having to go through the batch scheduler. Any data stored on the nodes is persistent (but not backed up) and can only be removed by the users of the project. These nodes are referred to as the “Gordon ION” and must be requested separately from the regular compute cluster.

Each I/O node contains two hex-core 2.66 GHz Intel Westmere processors, 48 GB of DDR3-1333 memory and sixteen 300 GB Intel 710 series solid-state drives. The drives will typically be configured as a single RAID 0 device with 4.4 TB of usable space. However, this default configuration can be changed based on the needs of the project and in collaboration with SDSC staff.  Each of the I/O nodes can mount both the NFS home directories and Data Oasis, SDSC’s 4 PB Lustre-based parallel file system, via two 10 GbE connections. The aggregate sequential bandwidth for the 16 SSDs within an I/O node was measured to be 4.3 GB/s and 3.4 GB/s for reads and writes, respectively. The corresponding random performance is 600 KIOPS for reads and 37 KIOPS for writes.

Recommended Use
---------------
Gordon ION awards are intended for projects that can specifically benefit from persistent, dedicated access to the flash storage. Applications that only need temporary access to the flash storage (e.g. fast scratch space, staging of data sets that will be accessed multiple times within a batch job) can do so via a regular Gordon compute cluster award.

Gordon ION is particularly well suited to database and data-mining applications where high levels of I/O concurrency exist, or where the I/O is dominated by random access data patterns. The resource should be of interest to those who, for example, want to provide a high-performance query engine for scientific or other community databases. Consequently, Gordon ION allocations are for long-term, exclusive, and dedicated use of the awardee.

SDSC staff can help you configure the I/O node based on your requirements. This includes the installation of relational databases (MySQL, PostgreSQL, DB2) and Apache web services.

Allocations
-----------
Dedicated Gordon I/O nodes are only available through the XSEDE startup allocations process. Normally a single I/O node will be awarded, but two I/O nodes can be awarded for exceptionally well-justified requests. Projects that also have accompanying heavy computational requirements can ask for up to 16 compute nodes for each I/O node.

Successful allocation requests must describe how you will make use of the I/O nodes. This should include relevant benchmarks on spinning disks, with projections of how the applications will scale when using flash drives. Additionally, the request should include a strong justification for why these should be provided as a dedicated resource—for example, providing long-term access to data for a large community via a Science Gateway

Gordon’s flash-based I/O nodes are a unique resource in XSEDE and we encourage you to contact SDSC staff to discuss the use of this resource before you submit your allocation request.  SDSC applications staff will be able to help you understand if this resource is a good match for your project, and subsequently, provide assistance in getting started with your project.
