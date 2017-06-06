Components
==========
Submit File
~~~~~~~~~~~
HTCondor scheduler relies on a *Submit File* that communicates everything about our job(s) to the scheduler. A submit file will specify the executing job/script, arguments, variables, etc. (Refer to HTCondor Manual).
The SAMS Pipeline will consists of two main submit file:
1) SAMS.sub - A submit file for *Registration* Job
2) Average.sub - A submit file for *Compositing* Job

SAMS.sub ::

        universe=vanilla
        getenv=True
        environment="ITK_GLOBAL_DEFAULT_NUMBER_OF_THREADS=1"
        executable=<location>/mandible.wrapper.chtc.sh
        should_transfer_files=YES
        when_to_transfer_output=ON_EXIT
        request_cpus=1
        request_memory=$(memory)
        request_disk=6G

        requirements = (HasGluster == true)
        periodic_release=((JobStatus==5)&&((CurrentTime - EnteredCurrentStatus)>360))

        transfer_input_files=<location>/mandible.register.chtc.sh,<location>/bin/c3d,$(test_dicom),$(test_model),$(refImg),$(refMod),<location>/fsl-5.0.8-chtc-built.tgz,<location>/ants-chtc-built.tgz

        arguments="$(now) $(test_name) $(refName) $(Cluster) $(useModel)"
        log=$(id)_T-$(test_name)_R-$(refName)_$(Cluster)_$(now).log
        output=$(id)_T-$(test_name)_R-$(refName)_$(Cluster)_$(now).out
        error=$(id)_T-$(test_name)_R-$(refName)_$(Cluster)_$(now).err
        notification=Error
        notify_user=<user@email.com>
        stream_output=True

        queue


Average.sub ::

        universe=vanilla
        getenv=True
        environment="ITK_GLOBAL_DEFAULT_NUMBER_OF_THREADS=1"
        executable=<location>/average.wrapper.chtc.sh
        should_transfer_files=YES
        when_to_transfer_output=ON_EXIT
        request_cpus=1
        request_memory=10000
        request_disk=8G
        requirements = (HasGluster == true)

        transfer_input_files=<location>/average.weighted-averaging.chtc.sh,<location>/mandible.unpack-output.sh,<location>/bin/c3d,<location>/$(test_model),<location>/fsl-5.0.8-chtc-built.tgz

        arguments="$(now) $(test_name) $(dir_name) $(Cluster) $(comp)"
        log=$(id)_T-$(test_name)_O-$(dir_name)_$(Cluster).log
        output=$(id)_T-$(test_name)_O-$(dir_name)_$(Cluster).out
        error=$(id)_T-$(test_name)_O-$(dir_name)_$(Cluster).err
        notification=Error
        notify_user=<user@email.com>
        stream_output=True

        queue


