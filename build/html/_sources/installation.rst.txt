Installation
============

The SAMS pipeline consists of shell scripts requiring several executable programs as dependencies.

Operating System Requirements
-----------------------------
The SAMS pipeline is set up to run in a Linux environment (Mac is possible). It has been tested on the Ubuntu 14.04 platform. Window users with access to Linux server can execute the pipeline using a terminal emulator, or set up a Linux environment using `VirtualBox <http://www.virtualbox.org/wiki/Downloads>`_.

Executable Software Dependencies
--------------------------------
The following software are needed to work with the SAMS pipeline:

	* AFNI_ - Analysis of Functional NeuroImages
	* Analyze_- Analyze 12.0 
	* ANTs_ - Advanced Normalization Tools
	* C3D_ - Convert3D
	* FSL_ - FMRIB Software Library
	* MATLAB_ - MATLAB is preferred by the VTLab to render resulting SAMS-processed mandible for visual inspection.

Alternatives
~~~~~~~~~~~~
The following are a list of open-source software that can be used as alternatives to Analyze 12.0 and/or MATLAB:

	* ImageJ_
	* ITKSNAP_
	* Slicer_



Cluster Computing Environment
-----------------------------
This pipeline is currently designed to work with cluster computing for best performance.
The Vocal Tract Development Lab employs High Throughput Computing (HTC) resources from the `Center for High Throughput Computing <http://chtc.cs.wisc.edu>`_ at the University of Wisconsin-Madison. It can be adapted to use on other cluster computing environment for users who may have access to similar resources too.



.. _AFNI: http://afni.nimh.nih.gov/download/
.. _ANTs: http://sourceforge.net/projects/advants/
.. _C3D: http://sourceforge.net/projects/c3d/
.. _FSL: http://fsl.fmrib.ox.ac.uk/fsl/fslwiki/
.. _Analyze: http://analyzedirect.com/
.. _MATLAB: http://www.mathworks.com/
.. _ImageJ: http://imagej.nih.gove/ij/
.. _ITKSNAP: http://www.itksnap.org/
.. _Slicer: http://download.slicer.org/


