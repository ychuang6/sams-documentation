Cluster Computing Workflow
==========================

The VTLab uses resources from the `Center of High Throughput Computing <http://chtc.cs.wisc.edu>`_ (CHTC) at the University of Wisconsin-Madison to execute the automatic segmentation and compositing step of the pipeline. Due to the multiple template-based CT image registration design of this portion of the pipeline, a parallel computing environment provides more computing resources to complete multiple registration within the same time frame. Automatic processing, however, is not limited to the CHTC environment described here. 

This pipeline is integrated to work with HTCondor for higher performance. HTCondor is a tool for HTC on large collections of distributive computing resources, consisting of machines (typically a physical computer) that may have multiple cores and slots (unit of a machine, often corresponds to one core). It is a specialized workload management system for jobs that are computationally intensive or projects that require parallel computing power. It provides a job queuing mechanism, scheduling policy, priority scheme, resource monitoring and management. Users submit their parallel jobs or serials and HTCondor places them into a queue, chooses when and where to run the jobs based on the specification designed by the user, monitors the progress, and ultimately returns the result of the job to the user. 

For more information regarding HTCondor, refer to the `HTCondor Manual <https://research.cs.wisc.edu/htcondor/manual>`_.  

Workflow Map
------------
The registration and compositing commands described in the Basic Workflow section are put into a workflow designed to run on the HTCondor scheduler. DAGMan (Directed Acyclic Graph Manager), a meta-scheduler for HTCondor, is utilized for the SAMS pipeline to connect the registration and compositing steps together as dependencies.  Refer to the `DAGMan documentation <https://research.cs.wisc.edu/htcondor/manual/latest/2_10DAGMan_Applications.html>`_ in the HTCondor manual for detailed information. 

.. figure:: images/SAMSCHTC-Flowchart.png

	Flowchart demonstrating steps, scripts and components of running Automatic Segmentation and Compositing step using CHTC resources.



Terminology
-----------
The list of terminology provided below contains excerpts from the HTCondor Manual. The descriptions provided here are brief snippets of their actual and potential functions. Refer to the `HTCondor Manual <https://research.cs.wisc.edu/htcondor/manual>`_ for detailed explanations. 

**JOB** : 
The *JOB* keyword specifies a job to be managed by Condor. ::

   JOB JobName SubmitFileName 


**PARENT ... CHILD** : 
The *PARENT* and *CHILD* keywords specify the dependencies within the DAG. A parent node must be completed succssfully before any of the children node may be started. We use this to ensure the compositing job starts only after all specified registration jobs are completed. ::

   PARENT ParentJobName... CHILD ChildJobName... 


**DAG** : 
Directed Acyclic Graph (DAG) is used to represent a set of computations where the input, output or execution of one or more computations is dependent on one or more other computations. A DAG input file describes the DAG, and further submit description files are used by DAGMan when submitting programs to run under Condor. DAGMan is itself executed as a scheduler universe job within Condor. See HTCondor Manual for more information. 
A DAG file will be executed by running *condor_submit_dag* <dagfilename>.dag

A very simple DAG input file is ::

   JOB A A.sub
   JOB B B.sub
   JOB C C.sub
   JOB D D.sub
   PARENT A CHILD B C
   PARENT B C CHILD D


**SPLICE** : 
A splice is an instance of a subgraph which is specified in a separate DAG file. This creates a named instance of a DAG as specified in another file as an entity which may have PARENT-CHILD dependencies. ::

   SPLICE SpliceName DAGFileName [DIR directory]

A simple DAG spliced input file is ::

   SPLICE file1 dagFile1.dag
   SPLICE file2 dagFile2.dag
   ...
   SPLICE file5 dagFile5.dag


**Gluster** : 
Gluster is a file share maintained by CHTC, and used for files or software that are too large for HTCondor file transfer. See `<http://chtc.cs.wisc.edu/file-avail-gluster.shtml>`_ for explanation.


*In progress*



