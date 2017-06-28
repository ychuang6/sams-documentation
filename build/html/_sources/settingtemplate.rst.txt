Setting up Reference Templates
==============================
The SAMS pipeline requires a set of CT images and their respective segmented 3D mandible models as the **reference templates**. These templates are the CT image (*template scan*) that the *test scan* will be mapped on to during the image registration process. Their respective 3D mandible models are the deformable models used to create the automatically-segmented mandibles for each registration. The following sections showed steps the VTLab implemented to build the *reference templates* needed for this pipeline.


Template Selection
------------------
The 54 *reference templates* (27 Males, 27 Females) we selected consisted of typically-developing individuals from birth to age of 20 years old. Each of the *reference templates* have the following components prepared and pre-processed:

1. CT Image (Referred to as *Template Scan* in the rest of this documentation)
2. 3D Mandible Model (Referred to as *Template Model* in the rest of this documentation)

This set of *reference templates* can be altered according to user preferences and research goals. Parameters presented in this documentation were optimized based on this set of *reference templates*. The user should perform parameter optimization to allow for best-performance of the reference templates built. 


Template Preparation Process
----------------------------

The *template scans* are first processed through a cycle of registration pipeline with their 3D mandible models (that are segmented manually prior) as the deformable models, generating a set of automatically-segmented mandible models for each of the *template scan*. The segmented mandible models are then post-processed for enhancement and eventually used as the final *template models* in the SAMS pipeline. 


<insert image>