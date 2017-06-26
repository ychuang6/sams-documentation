Overview
========

This tutorial outlines the method developed by the `Vocal Tract Development Lab <http://www.waisman.wisc.edu/vocal>`_ (VTLab) to segment 3D mandible/jaw objects from head and neck multi-detector Computed Tomography (MDCT) images. This pipeline is designed to reduce the effort spent on manually segmenting 3D mandibles from raw CT images. Due to morphological variation of the mandibule across age and individuals, manual segmentation is a tedious and time consuming process, especially in research setting requiring large input of 3D mandible models, eg: developmental study on mandibular morphology. Therefore the VTLab developed this semi-automatic mandible segmentation (SAMS) pipeline to speed up the segmentation process, using a relatively easy setup. Other research groups facing similar issues may therefore adopt and adapt this pipeline for their use.  

Background
----------
This semi-automated mandible segmentation (SAMS) pipeline uses image registration to compare an unknown case (referred to as a *test* *scan* in this documentation, see next figure for flowchart), with a set of previously-segmented CT images and their respective 3D mandible models (this dataset is referred to as *reference templates*). This process is then used to infer the location of the mandibular region on the *test scan*. 

This pipeline was developed with using mainly open-source software with the exception of `Analyze 12.0 <http://analyzedirect.com>`_ (AnalyzeDirect, Overland Park, KS). The VTLab uses Analyze 12.0 (AnalyzeDirect, Overland Park, KS) for image preparation, editing and threshold determination. It can however be replaced with alternative software listed under the Installation section of this documentation. 

`FSL (FMRIB Software Library) <http://fsl.fmrib.ox.ac.uk/fsl/fslwiki>`_ and `Advanced Normalization Tools (ANTs) <http://sourceforge.net/projects/advants>`_ are used for thresholding, trimming, and image registration. ITK-SNAP's `Convert3D (C3D) <http://sourceforge.net/projects/c3d>`_ tool is used to convert output into binary form. `AFNI (Analysis of Functional NeuroImages) <http://afni.nimh.nih.gov/download>`_ is used for post-processing padding and file conversion as needed. `MATLAB <http://www.mathworks.com>`_ is used for inspection of the segmented mandibles. See Installation page for links, alternatives and additional information.

This pipeline consists of three major steps: 1) Pre-processing, 2) Automatic Segmentation and Compositing, and 3) Post-processing. This pipeline is semi-automated since step 1 and step 3 require manual intervention.

In summary, an input CT series stored in Digital Imaging and Communications in Medicine (DICOM) format is first converted into Analyze-compatible 3D format for the user to determine a threshold in Hounsfield Unit (HU) that will remove all non-osseous tissues from the CT image (*test scan*). Then the user will determine the minimal enclosing box that contains the entire mandible in the *test scan*. The input *test scan* is then processed through the pipeline to be thresholded and cropped based on input user values. The pre-processed *test scan* is then sent to cluster computing to be registered against multiple *reference templates*. Once the mathematical transformation between the *test scan* and each references is established, the inverse transformation is applied to the known reference's 3D mandible models (*template model*) to yield predictive mandible model for the *test scan*. These resulting "segmented 3D mandibles" are then compiled and averaged to produce a final model. This final model is then rendered in 3D for visual inspection. Mandibles with regions requiring correction/enhancement are converted back into Analyze format for manual editing in Analyze 12.0 (see Installation page for software options).

Dataset
-------
The dataset and the *reference templates* used to design this pipeline are from our VTLab imaging database which was collected retrospectively following `University of Wisconsin-Madison Institutional Review Board (IRB) <http://www.irb.wisc.edu>`_ approval (IRB number: 2011-0036 and 2016-1052).

Flowchart
---------

.. figure:: images/samsflowchartmain.jpg
	
	Flowchart of the SAMS Pipeline
