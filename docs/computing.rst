********************************
Neuroimaging Computing Resources
********************************
This page gives an overview of the resources available for neuroimaging computing and date storage. 

===================
Data Storage Server
===================
Once a scan is completed, it is transferred to the Department's storage server (see your PI or Hugh McCann for access). This server had to be mounted on your local computer. Once the server is mounted on your computer you can transfer the files to your local workstations or to a computing cluster. Both brazos and terra are accessible through globus. You must install globus on your local computer and set it up as an endpoint. As part of the set-up, you must add access to mounted drives (e.g., the storage share or GoogleDrive), as shown `in these instructions <https://docs.globus.org/how-to/globus-connect-personal-mac/>`_.

`Terra Globus Instructions <https://hprc.tamu.edu/wiki/SW:GlobusConnect>`_

=========================
Using a computing cluster
=========================
https://campuscluster.illinois.edu/user_info/doc/beginner.html

===================
ViDaL Cluster
===================
The Vidal cluster supports high memory jobs, graphics intensive processes (P100 GPUs), and data requring added security or legal protection (e.g., HIPAA, FERPA). Information on accessing the cluster is available `here <https://vidal.tamu.edu/>`_.

===================
Terra Supercomputer
===================
The terra cluster is available to members of TAMU through the High Performance Research Computing (HPRC) Center. HPRC offers frequent `training classes and workshops <https://hprc.tamu.edu/user_services/#training>`_ on a variety of computing topics for beginners and experienced users.

The `terra user guide <https://hprc.tamu.edu/wiki/Terra>`_ provides a lot of information of using terra. The only fMRI software currently installed as a module on terra is FSL, but you can request the installation of other software by `emailing the HPRC Help <mailto:help@hprc.tamu.edu>`_. 

Remote Visualization
--------------------
The HPRC Wiki has a great page on `remote visualization <https://hprc.tamu.edu/wiki/Terra:Remote-Viz>`_.

Loading software resources
--------------------------
Software on terra is managed with the ``modules`` system. To see what software is available on brazos, use ``module avail``. If you have an idea of what software you want to load, you can use ``module spider <name>`` to see what versions are available and how to load them. If you don't specify a version, the most recent version is loaded by default.

To load a specific version you need to give the version number, e.g.,
:: module load fsl/5.0.10

By loading a module you are configuring paths and loading any dependent software. Once you load a software module, the executables should be available, e.g.,
:: 	

	$ which fsl
		/usr/bin/which: no fsl in (/home/joseph.orr/bin:/usr/lib64/qt-3.3/bin:/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin/brazos)
	$ module load fsl
	$ which fsl
		/apps/psyc/fsl/5.0.11/bin/fsl

Container packages
------------------
There is a growing trend in software to use *containers*, which are "lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings." Containers are useful for running software that require lots of library dependencies that might lead to compatability issues with software already on a system. **Docker** is a leader in the field of container packaging, and there are already several docker container images available for neuroimaging software, e.g., ``fmriprep`` and ``mriqc``. However, docker containers have issues with security on HPC systems, but there is a workaround called ``singularity``. ``singularity`` is available on ``terra`` for running container images. 

A lot of MRI software is available as a container through `neurodocker <https://github.com/kaczmarj/neurodocker>`_.

You should download the container images to your user or group scratch directory.

Submitting jobs
~~~~~~~~~~~~~~~
When you login to the cluster, you are on on the login node, which is only meant for simple processes like editing text files or copying a small number of files. More complicated jobs should be submitted to the compute nodes with the job manager.

Terra uses ``slurm`` for managing jobs, an open source package which is used on most academic computing clusters. The terra user guide has a lot of documentation on submitting a variety of different types of jobs. A job is submitted with ``sbatch`` and monitored with ``squeue``. If you need to actively monitor or interface with a job, you can start an interactive job with the wrapper ``srun``. This will queue the resources needed on a compute node.

FSL has a built in program for submitting jobs to slurm called ``fsl_sub`` that actually uses ``sbatch`` to submit your job to slurm. However, many packages will self-submit so you won't need to use ``fsl_sub``. A list of the self-submitting programs is found `here <https://fsl.fmrib.ox.ac.uk/fsl/fslwiki/SGE%20submission%20FAQ>`_, and includes ``feat``. Because many of these self-submitting jobs do some organizational procedures on the login node before submitting, you may need to call them from a interactive node.

submitting container jobs to slurm
----------------------------------
To submit a container job to slurm, you must create a submission script as discucced above. The submission script will call singularity as follows::	

	singularity run <container>.simg <container commands>

AFNI (and possibly other containers) can be run interactively, meaning you can pull up the typical GUI and work with that (use singularity shell). To do this however, you will need to run the container as an interactive job so that it isn't running on the login node. You will need to make a slurm submission script, but instead of calling it with ``sbatch`` you can use ``srun`` instead. In your submission script you will need to specify the required walltime, RAM, nCPUS, etc.