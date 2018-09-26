-------------------
Converting Raw Data
-------------------

DICOM Conversion
================
Data is saved from the scanner in a raw format called DICOM. On the TIPS scanner the files have the ending .IMA, but these are a form of DICOM. As DICOM images cannot be read by most software packages, we need to convert them to a standard format for neuroimaging software. The current standard is NIFTI (.nii). The most common way to perform a DICOM-to-NIFTI conversion is a package developed bu Chris Rorden called `dcm2nii`. The current version of ``dcm2nii`` is called `dcm2niix <https://www.nitrc.org/plugins/mwiki/index.php/dcm2nii:MainPage>`_.

To convert your DICOM image you can simply call ``dcm2niix`` as follows: ``dcm2niix <dicomdir>``, where ``<dicomdir>`` is the directory containing the DICOM images for a given scan.

BIDS Format
===========
Brain Imaging Data Structure or `BIDS <http://bids.neuroimaging.io/>`_ is an attempt at creating a standardized organization structure for neuroimaging experiments. One benefit of BIDS is that every scan has a .JSON file accompanying the NIFTI files which contains descriptive information about the scan, such as scanning parameters. Most of this information come straight from the DICOM file headers, but this information is usually lost when converting to NIFTI format. BIDS also has specifications for behavioral data, esp. timing files used for fMRI. One of the main purposes of BIDS is to make sharing data as easy as possible. There are also data preprocessing and analysis tools now available that can run pipelines on your data with little need for user input, as the specification contains most of the critical information about settings. Examples are: ``fmriprep``and ``mriqc``. 

There are several tools for converting your DICOM images to BIDS format. I am most familiar with the ``bidskit`` utility, but the ``heudiconv`` tool is also a popular tool for DICOM to BIDS conversion.

``bidskit`` is current installed on brazos, but ``heudiconv`` is not.


BIDSKIT
-------
``bidskit`` is run in a two step process. The first step converts the DICOM to NIFTI format and the second step organizes the converted files in the BIDS stucture.

To run bidskit call::

	singularity run --clean-env /apps/psyc/containers/bidskit/<version> -i <dicomdir> -o <bidsdir>

Where ``<version>`` is the version of bidskit you want to use, ``dicomdir`` is the path to the directory with all of your subject's dicom data, and ``bidsdir`` is the path of the directory where you would like to put the bids formatted output.

The first pass performs the actual dicom to nifti conversion so it takes a long time. The first pass conversion will create new translator dictionary (Protocol_Translator.json) in the root DICOM folder. This has been prefilled with the protocol series names from the DICOM header of all unique series detected in the original DICOM files. The command will also create the new BIDS directory containing a single temporary conversion directory containing Nifti images and JSON sidecars for all series in the source DICOM folder. You will need to edit the translator dictionary so that each scan has the necessary information from the bids specifications. Here is an example of a study with structural, rest, functional, and diffusion images as well as distortion correction fieldmap-type images::

	{
	    "Localizer":[
	        "EXCLUDE_BIDS_Directory",
	        "EXCLUDE_BIDS_Name",
	        "UNASSIGNED"
	    ],
	    "rsBOLD_MB_1":[
	        "func",
	        "task-rest_acq-MB_run-01_bold",
	        "UNASSIGNED"
	    ],
	    "T1w":[
	        "anat",
	        "T1w",
	        "UNASSIGNED"
	    ],
	    "LearnRun1":[
	        "func",
	        "task-learn_acq-MB_run-01_bold",
	        "UNASSIGNED"
	    ],
	    "LearnRun2":[
	        "func",
	        "task-learn_acq-MB_run-02_bold",
	        "UNASSIGNED"
	    ],
	    "learn_distcorrAP":[
	        "fmap",
	        "dir-AP_epi.nii.gz",
	        ["task-learn_acq-MB_run-02_bold","dwi_96dir_PA"]
	    ],
	    "learn_distcorrPA":[
	        "fmap",
	        "dir-PA_epi.nii.gz",
	        ["task-learn_acq-MB_run-01_bold","dwi_96dir_AP"]
	    ],
	    "cmrr_mbep2d_diff_directions96_AtoP":[
	        "dwi",
	        "dwi_96dir_AP",
	        "UNASSIGNED"
	    ],
	    "cmrr_mbep2d_diff_directions96_PtoA":[
	        "dwi",
	        "dwi_96dir_PA",
	        "UNASSIGNED"
    	]
	}
