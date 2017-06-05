Cluster Computing Workflow
==========================
The VTLab uses resources from the Center of High Throughput Computing (CHTC) here in University of Wisconsin-Madison to execute the automatic segmentation and compositing step of the pipeline. Due to the multiple template-based CT image registration design of this portion of the pipeline, a parallel computing environment provides more computing resources to complete multiple registration in the same time frame. This is, however, not limited to the environment described here. 

The pipeline is integrated to work with HTCondor scheduler on remote clusters with higher performance. HTCondor is a tool for HTC on large collections of distributive computing resources. It is a specialized workload management system for compute-intensive jobs or projects requiring parallel computing power. It provides job queuing mechanism, scheduling policy, priority scheme, resource monitoring and resource management. Users submit their parallel jobs or serials and HTCondor place them into a queue, choosing when and where to run the jobs based on the specification designed by the user, monitor the progress, and ultimately return the result of the job to the user. 

For more information regarding HTCondor, refer to the `HTCondor Manual <http://research.cs.wisc.edu/htcondor/manual>`_.  

Workflow Map
~~~~~~~~~~~~
The registration and compositing commands described in the Basic Workflow section are put into a workflow designed to run on the HTCondor scheduler. DAGMan (Directed Acyclic Graph Manager), a meta-scheduler for HTConder, is utilized for the SAMS Pipeline to connect the registration and compositing steps together as a dependencies.  Please refer to the `DAGMan Documentation <http://research.cs.wisc.edu/htcondor/manual/latest/2_10DAGMan_Applications.html>`_ in the HTCondor manual for detailed information. 

<insert workflow Map>

Important Components
~~~~~~~~~~~~~~~~~~~~
SPLICE
DAG
Submit File
Scripts


Submit File
-----------
HTCondor scheduler relies on a *Submit File* that communicates everything about our job(s) to the scheduler. A submit file will specify the executing job/script, arguments, variables, etc. (Refer to HTCondor Manual).
The SAMS Pipeline will consists of two main submit file:
1) SAMS.sub - A submit file for *Registration* Job
2) Average.sub - A submit file for *Compositing* Job

SAMS.sub::

	universe=vanilla
	getenv=True
	environment="ITK_GLOBAL_DEFAULT_NUMBER_OF_THREADS=1"
	executable=/home/<user>/sams/mandible.wrapper.chtc.sh
	should_transfer_files=YES
	when_to_transfer_output=ON_EXIT
	request_cpus=1
	request_memory=$(memory)
	request_disk=6G

	requirements = (HasGluster == true)
	periodic_release=((JobStatus==5)&&((CurrentTime - EnteredCurrentStatus)>360))

	transfer_input_files=$(studyDir)/sams/mandible.register.chtc.sh,$(studyDir)/bin/c3d,$(test_dicom),$(test_model),$(refImg),$(refMod),<location>/fsl-5.0.8-chtc-built.tgz,<location>/ants-chtc-built.tgz

	arguments="$(now) $(test_name) $(refName) $(Cluster) $(useModel)"
	log=$(id)_T-$(test_name)_R-$(refName)_$(Cluster)_$(now).log
	output=$(id)_T-$(test_name)_R-$(refName)_$(Cluster)_$(now).out
	error=$(id)_T-$(test_name)_R-$(refName)_$(Cluster)_$(now).err
	notification=Error
	notify_user=<user@email.com>
	stream_output=True

	queue 

Average.sub::

	universe=vanilla
	getenv=True
	environment="ITK_GLOBAL_DEFAULT_NUMBER_OF_THREADS=1"
	executable=/home/<user>/sams/average.wrapall.chtc.sh
	should_transfer_files=YES
	when_to_transfer_output=ON_EXIT
	request_cpus=1
	request_memory=10000
	request_disk=8G
	requirements = (HasGluster == true)

	transfer_input_files=$(studyDir)/sams/average.weighted-averaging.chtc.sh,$(studyDir)/sams/mandible.unpack-output.sh,$(studyDir)/bin/c3d,<location>/$(test_name)/$(test_model),<location>/fsl-5.0.8-chtc-built.tgz

	arguments="$(now) $(test_name) $(dir_name) $(Cluster) $(comp)"
	log=$(id)_T-$(test_name)_O-$(dir_name)_$(Cluster).log
	output=$(id)_T-$(test_name)_O-$(dir_name)_$(Cluster).out
	error=$(id)_T-$(test_name)_O-$(dir_name)_$(Cluster).err
	notification=Error
	notify_user=<user@email.com>
	stream_output=True

	queue


<InProgress>

 
