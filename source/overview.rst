Overview
========

This tutorial outlines the methods developed by the `Vocal Tract Development Lab <http://www.waisman.wisc.edu/vocal>`_ (VTLab) for segmenting 3D mandible/jaw objects from head and neck multi-detector Computed Tomography (MDCT) images. This pipeline is designed to reduce the effort spent on manually segmenting 3D mandibles from raw CT images. Due to morphological variation of the mandibular structure across ages and individuals, manual segmentation is a tedious and time consuming process, especially in research setting requiring large input of 3D mandible models, eg: developmental morphology study. Therefore the VTLab developed the semi-automatic mandible segmentation (SAMS) pipeline to speed up the segmentation process, presenting a relatively easy set-up for research groups that are facing similar issues.  

Background
~~~~~~~~~~
This semi-automated segmentation pipeline uses image registration to compare an unknown case (referred to as a *test* *scan* in this documentation, see Figure 1 for flowchart), with a set of previously-segmented CT images and their respective 3D mandible models (this dataset is referred to as *reference templates* for this documentation). This process is then used to infer the location of the mandibular region on the *test scan*. 

This pipeline is developed with mainly open-source software aside from Analyze 12.0 (AnalyzeDirect, Overland Park, KS). The VTLab uses Analyze 12.0 (AnalyzeDirect, Overland Park, KS) for image preparation, editing and threshold determination. It can however be replaced with alternative software listed under the Installation section of this documentation. 

FSL (FMRIB Software Library) and Advance Normalization Tools (ANTs) are used for thresholding, trimming, and image registration. ITK-SNAP's Convert3D(C3D) tool is used to convert output into binary form. AFNI (Analysis of Functional NeuroImages) is used for post-processing padding and file conversion as needed. MATLAB is used for inspection of the segmented mandibles. See Installation page for links, alternatives and other information.

This pipeline consists of three major steps: 1) Pre-processing, 2) Automatic Segmentation and Compositing, and 3) Post-processing. The pipeline is semi-automated where step 1 and step 3 are processed manually.

In summary, an input CT series stored in Digital Imaging and Communications in Medicine (DICOM) format is first converted into Analyze-compatible 3D format for user to determine a threshold in Hounsfield Unit (HU) that will remove all non-osseous tissues from the CT image (*test scan*). Then user will determine the minimal enclosing box that contains the whole mandible in the *test scan*. The input *test scan* is then processed through the pipeline to be thresholded and cropped based on input user values. The pre-processed *test scan* is then sent to cluster computing to be registered against multiple *reference templates*. Once the mathematical transformation between the *test scan* and each references is established, the inverse transformation is applied to the known reference's 3D mandible models (*template model*) to yield predictive mandible model for the *test scan*. These resulting "segmented 3D mandibles" are then compiled and averaged to produce a final model. This final model is then rendered in 3D for visual inspection. Mandibles with regions requiring correction/enhancement will be converted back into Analyze format for manual editing in Analyze 12.0 (see Installation page for software options).

Dataset
~~~~~~~
The dataset used to design this pipeline and used as the *reference templates* were collected retrospectively following University of Wisconsin-Madison Institutional Review Board (IRB) approval (IRB number: 2011-0036 and 2016-1052).

Figure 1: Flowchart of the SAMS Pipeline

.. image:: images/samsflowchartmain.jpg
	
