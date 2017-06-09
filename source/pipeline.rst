Basic Workflow
==============

After setting up the required dependencies and *reference templates*, you are ready to segment a 3D mandible from an input raw CT image (*test scan*). 

Input Preparation
-----------------
Before starting the SAMS pipeline, the desire input files should be converted from its DICOM series into Analyze75 file format [.hdr/.img] or equivalent.

In Analyze 12.0, user will inspect the scan and collect the threshold needed to remove all non-osseous tissues from the 3D renderings. 

If an alternative software is used for inspection and threshold collection, the input file can also be saved as NIfTI file format [.nii or .nii.gz]. 

The following pre-processing step accepts both Analyze75 and NIfTI file format as the input *test scan*.

Pre-processing
--------------
In pre-processing, the input *test scan* will be inspected by user to:

1. Find a threshold value in Hounsfield Unit (HU) that provides best quality of the mandible.
2. Find the minimal enclosing box that contains the mandible. 


In Analyze 12.0, the threshold value is determined by adjusting for the minimum threshold that will render a 3D mandible free of all non-osseous tissue as shown below:

.. image:: images/ThresholdSample.png

Typical threshold range between 80-300. 


Next, user will record the smallest and largest slice number of the image in all X, Y and Z direction.
These dimensions information will be used as input values to crop the image into its minimal enclosing box::

	$ fslroi <trim_image> <input_image> <xlower> <xupper> <ylower> <yupper> <zlower> <zupper>
	$ SVAdjustVoxelspace -in <trim_image> -origin 0 0 0 

The image will be then be thresholded according to the threshold value the user collected.::

	$ fslmaths <trim_image> -thr <threshold_value> -uthr 3000 <trim_image>


Effect of applying threshold to a cropped *test scan* in all anatomical orientation. Top row are raw, cropped scan, bottom row are thresholded scan. 

.. image:: images/RawThreshold.jpg


Shown below are illustrations of cropping input *test scan* down to its minimal enclosing box: 

.. image:: images/Cropped3D.jpg
.. image:: images/ThresholdedCropped.png


Bias correction is then applied to decrease scan intensity inhomogeneity::

	$ N4BiasFieldCorrection -d 3 -i <trim_image> -o <trim_image>

The resulting trimmed *test scan* is now ready for the automatic portion of the pipeline.


Automatic Segmentation and Compositing
--------------------------------------
This step is an entirely automatic process completed on cluster computing (if available to user). The preprocessed input-*test scan*, will be registering against 54 *template scans*, followed by the application of the resulting inverse transformation on the *template models* to generate 54 separate segmented mandibles of the input. 
The 54 segmented mandibles are then composited and normalized to generate the final mandible. 

Major commands used are as follow:

Automatic Segmentation
~~~~~~~~~~~~~~~~~~~~~~
ANTS registration (shown are the command using ANTS instead of antsRegistration) ::
 
	$ ANTS 3 -m MSQ[<referenceScan>, <testScan>, 1, 0] -o <output>.nii.gz -i 10x20x5 -r Gauss[4,0] -t SyN[4] --affine-metric-type MSQ --number-of-affine-iterations 2000x2000x2000 <output>.log

The ANTS parameter listed here are as follow: 
	i. Similarity Metric = MSQ[<referenceScan], <testScan>, 1, 0]
	ii. Deformable iteration = 5 x 50 x 10
	iii. Regularizer = Gauss[4,0]
	iv. Transformation = SyN[0.4]
	v. Affine metric type = MSQ
	vi. Number of affine iterations = 2000 x 2000 x 2000

	These values were obtained after in-house parameter sweeping. Users can alter the values according to their targeted reference templates age range and demographics. 

Followed by ::

	$ WarpImageMultiTransform 3 <referenceModel> <affineInverseWarp>.nii.gz -i <ANTSAffine>.txt <ANTSInverseWarp>.nii.gz --use-NN -R <testScan>

Note: affineInverseWarp.nii.gz, ANTSAffine.txt and ANTSInverseWarp.nii.gz are output from the first step.

Binarization is performed to ensure that segmented mandibles are in binary form ::

	$ c3d <affineInverseWarp>.nii.gz -binarize -o <segmented_mandible>.nii.gz

	
Compositing
~~~~~~~~~~~
All segmented mandibles from Automatic Segmentation steps will be compiled into one single composite::
	
	$ fslmerge -t <allModels>.nii.gz "all <segmented_mandibles>.nii.gz separated by space"

Normalization of the composite mandible::

	$ fslmaths <allModels>.nii.gz -thr <normalization_value> -uthr <total_number_of_mandibles_in_composite> -bin <allModels>.nii.gz


Post-processing
---------------
Once all compositing and averaging are completed, generating one final mandible, this mandible is viewed in MATLAB.
In our case, the output from step 2 is in NIfTI file format::
 
	 nii = load_nii('<allModels>.nii.gz')
	 mandible = isosurface(nii.img,0.5)

Now you can view the 3D mandible::
 
	 p = patch(mandible)
	 set(p,'FaceColor','red','EdgeColor','none')
	 camlight

Rotate the mandible to inspect any regions requiring further enhancement 

Manual Editing
~~~~~~~~~~~~~~
If the mandible needs to be edited manually, the NIfTI file will be padded into its original dimension using AFNI.
Once padded, the mandible will be imported back into Analyze 12.0 for manual editing. 

The following are used only if the user is using Analyze 12.0 as the editing software. 

Depending on the set up of pre-processing the values for dimensions varies::
	
	$ 3dZeropad -I <zlower> -S <zupper> -P <ylower> -A <yupper> -L <xlower> -R <xupper> -prefix <outputName> <allModels>.nii.gz
	$ 3dAFNItoANALYZE <outputName> <outputName>+orig

When reloading the scan into the Analyze, user should flip the scan in X direction.



