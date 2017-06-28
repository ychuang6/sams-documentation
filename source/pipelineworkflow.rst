Basic Workflow
==============

After setting up the required dependencies and *reference templates*, users may begin segmenting 3D mandibles from an input raw CT image (*test scan*). 

What follows is a list of all commands used in the SAMS pipeline. Variable names/values/files needed to be modified by users are enclosed between **< ... >**. 

...

.. note:: Refer to related documentations of functions and commands described in this documentation for detailed explanation. 
 

Input Preparation
-----------------
Before starting the SAMS pipeline, the desired input files should be converted from DICOM series into Analyze75 file format [.hdr/.img] or equivalent format.

In Analyze 12.0, user needs to inspect the scan and collect the threshold needed to remove all non-osseous tissues from the 3D renderings. 

If an alternative software is used for inspection and threshold collection, the input file can also be saved as NIfTI file format [.nii or .nii.gz]. 

The following pre-processing step accepts both Analyze75 and NIfTI file format as the input *test scan*.


Pre-processing
--------------
In pre-processing, the input *test scan* will be inspected by user to:

1. Find a threshold value in Hounsfield Unit (HU) that provides best quality of the mandible.
2. Find the minimal enclosing box that contains the mandible. 


In Analyze 12.0, the threshold value is determined by adjusting for the minimum threshold that will render a 3D mandible free of all non-osseous tissue. Typical threshold range between 80-350 HU.

Next, user will record the smallest and largest slice number of the image in all X, Y and Z direction.
These dimensions information will be used as input values to crop the image into its minimal enclosing box::

<insert FSL>


The image will be then be thresholded according to the threshold value the user collected::

<insert FSL>

<insert IMAGE>

Automatic Segmentation and Compositing
--------------------------------------
This step is an entirely automatic process completed on cluster computing (if available to user). The preprocessed input-*test scan*, will be registering against 54 *template scans*, followed by the application of the resulting inverse transformation on the *template models* to generate 54 separate segmented mandibles of the input. 
The 54 segmented mandibles are then composited and normalized to generate the final mandible. 

<insert FSL>


Automatic Segmentation
~~~~~~~~~~~~~~~~~~~~~~
ANTS registration's command are used to perform all automatic segmentation steps. :: 

<insert parameters>
	
<insert ANTScommands>

<insert image>


Compositing
~~~~~~~~~~~
All segmented mandibles from Automatic Segmentation steps will be compiled into one single composite and normalized::

<insert weighted averaging command>

Post-processing
---------------
Once all compositing and averaging are completed, a single final composite mandible is generated. This composite mandible is viewed in MATLAB to determine if further manual touch-up is needed.

 
	 nii = load_nii('<allModels>.nii.gz')
	 mandible = isosurface(nii.img,0.5)


Now you can view the 3D mandible::
 
	 p = patch(mandible)
	 set(p,'FaceColor','<ColorValues>','EdgeColor','none')
	 camlight

Rotate the mandible around to inspect any regions requiring further enhancement 


Manual Editing
~~~~~~~~~~~~~~

If manual touch-up on the model are needed, the mandible composite will be converted into analyze75 [.hdr/.img] format in order to allow manual editing in Analyze 12.0. To do this, we use the following commands from AFNI:: 

<insert AFNI command> 


<insert image>



