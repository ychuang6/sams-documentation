Cluster Computing Workflow
==========================

The VTLab uses resources from the Center of High Throughput Computing (CHTC) here in University of Wisconsin-Madison to execute the automatic segmentation and compositing step of the pipeline. Due to the multiple template-based CT image registration design of this portion of the pipeline, a parallel computing environment provides more computing resources to complete multiple registration in the same time frame. This is, however, not limited to the environment described here. 

The pipeline is integrated to work with HTCondor scheduler on remote clusters with higher performance. HTCondor is a tool for HTC on large collections of distributive computing resources. It is a specialized workload management system for compute-intensive jobs or projects requiring parallel computing power. It provides job queuing mechanism, scheduling policy, priority scheme, resource monitoring and resource management. Users submit their parallel jobs or serials and HTCondor place them into a queue, choosing when and where to run the jobs based on the specification designed by the user, monitor the progress, and ultimately return the result of the job to the user. 

For more information regarding HTCondor, refer to the `HTCondor Manual <https://research.cs.wisc.edu/htcondor/manual>`_.  

Workflow Map
------------
The registration and compositing commands described in the Basic Workflow section are put into a workflow designed to run on the HTCondor scheduler. DAGMan (Directed Acyclic Graph Manager), a meta-scheduler for HTConder, is utilized for the SAMS Pipeline to connect the registration and compositing steps together as a dependencies.  Please refer to the `DAGMan Documentation <https://research.cs.wisc.edu/htcondor/manual/latest/2_10DAGMan_Applications.html>`_ in the HTCondor manual for detailed information. 

<insert workflow Map>

Important Components
--------------------
Terminology: 

JOB : The *JOB* keyword specifies a job to be managed by Condor. ::
	JOB *JobName SubmitFileName* 

PARENT ... CHILD :  The *PARENT* and *CHILD* keywords specify the dependencies within the DAG. A parent node must be completed succssfully before any of the children node may be started. We use this to ensure that only when all specified registration jobs are completed, the compositing job can start.::
	PARENT ParentJobName... CHILD ChildJobName... 

DAG : Directed Acyclic Graph (DAG) is used to represent a set of computations where the input, output or execution of one or more computations is dependent on one or more other computations. A DAG input file describes the DAG, and further submit description files are used by DAGMan when submitting programs to run under Condor. DAGMan is itself executed as a scheduler universe job withint Condor. See HTCondor Manual for more information. 
A DAG file will be executed by running *condor_submit_dag* <dagfilename>.dag

A very simple DAG input file is ::
	JOB A A.sub
	JOB B B.sub
	JOB C C.sub
	JOB D D.sub
	PARENT A CHILD B C
	PARENT B C CHILD D


SPLICE : A splice is an instance of a subgraph which specified in a separate DAG file. This creates a named instance of a DAG as specified in another file as an entity which may have PARENT and CHILD dependencies. ::
	SPLICE *SpliceName* DAGFileName [DIR directory]

A simple DAG spliced input file is ::
	SPLICE file1 dagFile1.dag
	SPLICE file2 dagFile2.dag
	...
	SPLICE file5 dagFile5.dag



InProgress
----------
*This Section is still in progress*



