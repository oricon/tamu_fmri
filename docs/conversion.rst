-------------------
Converting Raw Data
-------------------

DICOM Conversion
================

Data is saved from the scanner in a raw format called DICOM. On the TIPS scanner the files have the ending .IMA, but these are a form of DICOM. As DICOM images cannot be read by most software packages, we need to convert them to a standard format for neuroimaging software. The current standard is NIFTI (.nii). The most common way to perform a DICOM-to-NIFTI conversion is a package developed bu Chris Rorden called `dcm2nii`. The current version of `dcm2nii` is called `dcm2niix` <https://www.nitrc.org/plugins/mwiki/index.php/dcm2nii:MainPage>`_.

To convert your DICOM image you can simply call ``dcm2niix`` as follows:
``dcm2niix <dicomdir>`` where ``<dicomdir>`` is the directory containing the DICOM images for a given scan.

BIDS Format
===========
I have a more thorough description of the BIDS format elsewhere, but briefly, BIDS (Brain Imaging Data Structure) is an attempt at creating a standardized organization structure for neuroimaging experiments. One benefit of BIDS is that every scan has a .JSON file accompanying the NIFTI files which contains descriptive information about the scan, such as scanning parameters. Most of this information come straight from the DICOM file headers, but this information is usually lost when converting to NIFTI format.

There are several tools for converting your DICOM images to BIDS format. I am most familiar with the ``bidskit`` python utility, but the ``heudiconv`` tool is also a popular tool for DICOM to BIDS conversion.

``bidskit`` is current installed on brazos, but ``heudiconv`` is not.



BIDSKIT
-------
``bidskit`` is run in a two step process. The first step converts the DICOM to NIFTI format and the second step organizes the converted files in the BIDS stucture. 




Procedures
==========

For setting up a new study at TIPS:
* X
* Y
* Z

Piloting
========