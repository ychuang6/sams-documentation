Components
==========
In this section we will go through the main components used in setting up SAMS Pipeline with CHTC machine pool.
Shown below is the workflow set up for SAMS Pipeline when working with CHTC resources.
.. image:: ../images/SAMSCHTC-Flowchart.png

DAG File
--------

Sample dag file ::

	JOB TestA-RefA Registration.submit
	VARS TestA-RefA id=“mandible.submit.chtc”
	VARS TestA-RefA test_name=“F001-00-01-002”
	VARS TestA-RefA test_model=“F001-00-01-002-M-trim.nii.gz”
	VARS TestA-RefA refName=“F155-10-00-002_trimmed.nii.gz”
	VARS TestA-RefA refModel=“F155-10-00-002-M_trimmed.nii.gz”
	VARS TestA-RefA Now=“<timenow>”
	VARS TestA-RefA useModel=“true”
	...
	JOB TestA-Compositing Compositing.submit
	VARS .... ... 
	...
	PARENT TestA-RefA TestA-RefB ... CHILD TestA-Compositing



Submit File
-----------
HTCondor scheduler relies on a *Submit File* that communicates everything about our job(s) to the scheduler. A submit file is a text file that specifies the executing job/script, arguments, variables, etc. (Refer to HTCondor Manual).
The SAMS Pipeline will consists of two submit files:
1) Registration.submit - A submit file for *Registration* Job
2) Compositing.submit - A submit file for *Compositing* Job

Registration.submit ::

        universe=vanilla
        getenv=True
        environment="ITK_GLOBAL_DEFAULT_NUMBER_OF_THREADS=1"
        executable=<location>/mandible.wrapper.sh
        should_transfer_files=YES
        when_to_transfer_output=ON_EXIT
        request_cpus=1
        request_memory=$(memory)
        request_disk=6G

        requirements = (HasGluster == true)
        periodic_release=((JobStatus==5)&&((CurrentTime - EnteredCurrentStatus)>360))

        transfer_input_files=<location>/mandible.register.sh,<location>/bin/c3d,$(test_dicom),$(test_model),$(refImg),$(refMod),<location>/fsl-5.0.8-chtc-built.tgz,<location>/ants-chtc-built.tgz

        arguments="$(now) $(test_name) $(refName) $(Cluster) $(useModel)"
        log=$(id)_T-$(test_name)_R-$(refName)_$(Cluster)_$(now).log
        output=$(id)_T-$(test_name)_R-$(refName)_$(Cluster)_$(now).out
        error=$(id)_T-$(test_name)_R-$(refName)_$(Cluster)_$(now).err
        notification=Error
        notify_user=<user@email.com>
        stream_output=True

        queue


Compositing.submit ::

        universe=vanilla
        getenv=True
        environment="ITK_GLOBAL_DEFAULT_NUMBER_OF_THREADS=1"
        executable=<location>/compositing.wrapper.sh
        should_transfer_files=YES
        when_to_transfer_output=ON_EXIT
        request_cpus=1
        request_memory=10000
        request_disk=8G
        requirements = (HasGluster == true)

        transfer_input_files=<location>/weighted-averaging.sh,<location>/mandible.unpack-output.sh,<location>/bin/c3d,<location>/$(test_model),<location>/fsl-5.0.8-chtc-built.tgz

        arguments="$(now) $(test_name) $(dir_name) $(Cluster) $(comp)"
        log=$(id)_T-$(test_name)_O-$(dir_name)_$(Cluster).log
        output=$(id)_T-$(test_name)_O-$(dir_name)_$(Cluster).out
        error=$(id)_T-$(test_name)_O-$(dir_name)_$(Cluster).err
        notification=Error
        notify_user=<user@email.com>
        stream_output=True

        queue


Executing Scripts
-----------------
As shown in the flowchart above, there are two executing scripts for the Registration step and Compositing step respectively. These two steps are linked through a PARENT-CHILD dependency listed in the submit DAG file. 
The two scripts consist of a "wrapper" script and an executing script.

.. topic:: Wrapper

	Initiate and make referral to executing environment
	Specify all variables and arguments need for executing script
	Unzip and install software prebuilt on machine
	Run executing script
	Compile output into tarball and export to gluster

.. topic:: Executing Script

	Run software with wrapper-specified arguments


Registration
~~~~~~~~~~~~

Scripts used are ::

	mandible.wrapper.sh
	mandible.registration.sh

The executing script here will consist of commands specified in Basic Workflow/Automatic Segmentation and Compositing/Automatic Segmentation section


Compositing
~~~~~~~~~~~

Scripts used are ::

	compositing.wrapper.sh
	weighted-averaging.sh
	mandible.unpack.sh

The executing script here will consist of commands specified in Basic Workflow/Automatic Segmentation and Compositing/Compositing section





