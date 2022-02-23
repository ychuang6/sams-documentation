Cluster Computing: Overview
===========================

The VTLab uses resources from the `Center of High Throughput Computing <http://chtc.cs.wisc.edu>`_ (CHTC) at the University of Wisconsin-Madison to execute the automatic segmentation and compositing step of the pipeline. Due to the multiple template-based CT image registration design of this portion of the pipeline, a parallel computing environment provides more computing resources to complete multiple registration within the same time frame. Automatic processing, however, is not limited to the CHTC environment described here. 

This pipeline is integrated to work with HTCondor for higher throughput. HTCondor is a tool for HTC on large collections of distributed computing resources, consisting of machines (typically a physical computer) that may have multiple cores and slots (portion of a machine, often corresponds to one core). It is a specialized workload management system for jobs that are computationally intensive or projects that require parallel computing power. It provides a job queuing mechanism, scheduling policy, priority scheme, resource monitoring and management. Users submit their jobs and HTCondor places them into a queue, chooses when and where to run the jobs based on the specification designed by the user, monitors the progress, and ultimately returns the result of the job to the user. 

For more information regarding HTCondor, refer to the `HTCondor Manual <https://research.cs.wisc.edu/htcondor/manual>`_.  

Workflow Map
------------
The registration and compositing commands described in the Basic Workflow section are put into a workflow designed to run on the HTCondor scheduler. DAGMan (Directed Acyclic Graph Manager), a meta-scheduler for HTCondor, is utilized for the SAMS pipeline to connect the registration and compositing steps together as dependencies.  Refer to the `DAGMan documentation <https://research.cs.wisc.edu/htcondor/manual/latest/2_10DAGMan_Applications.html>`_ in the HTCondor manual for detailed information. 

.. figure:: images/SAMSCHTC-Flowchart.png

	Flowchart demonstrating steps, scripts and components of running Automatic Segmentation and Compositing step using CHTC resources.

