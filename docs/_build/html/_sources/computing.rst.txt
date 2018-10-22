********************************
Neuroimaging Computing Resources
********************************
This page gives an overview of the resources available for neuroimaging computing and date storage. 

===================
Data Storage Server
===================
Once a scan is completed, it is transferred to the Department's storage server (see your PI or Hugh McCann for access). This server had to be mounted on your local computer. Once the server is mounted on your computer you can transfer the files to your local workstations or to a computing cluster. Both brazos and terra are accessible through globus. You must install globus on your local computer and set it up as an endpoint. As part of the set-up, you must add access to mounted drives (e.g., the storage share or GoogleDrive), as shown `in these instructions <https://docs.globus.org/how-to/globus-connect-personal-mac/>`_.

`Brazos Globus Instructions <http://www.brazos.tamu.edu/docs/globusonline.html/>`_

`Terra Globus Instructions <https://hprc.tamu.edu/wiki/SW:GlobusConnect>`_

=========================
Using a computing cluster
=========================
https://campuscluster.illinois.edu/user_info/doc/beginner.html

========================
Brazos Computing Cluster
========================
Brazos is a high-throughput computing cluster maintained by the College of Science. The cluster is primarily available to stakeholders. PBSI has a stake in brazos which includes priority access to 128 computing cores. The remainder of the free computing cores are available via the background queue. 

Connecting to Brazos
--------------------
Brazos is accessible to approved users via ``ssh``. See also `login information <http://www.brazos.tamu.edu/docs/login.html/>`_.

On a windows machine you can connect with `putty <https://www.putty.org/>`_ or `MobaXterm <https://mobaxterm.mobatek.net/>`_. On a mac, simply use the built-in terminal (I recommend installing the iTerm2 terminal instead of using the built-in terminal).  

If you only need occasional graphics, you should be fine by enabling X-forwarding with the -Y flag in your ssh command::

	ssh -Y <userid>@login.brazos.tamu.edu

If you are viewing images or using a graphics intensive GUI (e.g., conn), you will need to connect using Virtual GL (see below) or use `X2Go Remote Desktop <http://www.brazos.tamu.edu/docs/graphical_login.html>`_. I have found that VirtualGL is faster, but novices to linux environments may be more comfortable using the X2Go Remote Desktop.

Virtual GL & TurboVNC Graphical Connection
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
For a Virtual GL with TurboVNC graphical connection, follow the following steps:

\1. Download and install `VirtualGL <https://sourceforge.net/projects/virtualgl/files/>`_ as well as `TurboVNC <https://sourceforge.net/projects/turbovnc/files/>`_.

2. Open terminal on local computer and ssh to gfx.brazos.tamu.edu. You can check if you already have a running vncserver with the command ``ps axu | grep vnc | grep \($USER)\``. If you don't already have a running server, start a VNC server session using the command::

	vncserver

You will be prompted to set (and confirm) a password. If it was successful, you will obtain something like this::

	You need to make a note of the display number, e.g., login:1, as this is your individual display. You will next need to ssh while opening a tunnel to the VNC display port, which is determined by added the display number to `5900`, e.g., `1 + 5900 = 5901`. You should now exit to close your ssh connection.

3. In your local computer terminal, you need to create an ssh tunnel for encrypting the VNC stream by typing::	

	ssh -L <vncport>:localhost:<vncport> <netid>@gfx.brazos.tamu.edu

where <vncport> is your VNC display port determined in the previous step, e.g., 5901. 

4. You can now open TurboVNC Viewer on your local machine and enter the vncserver as::

	localhost:<vncport>

5. This will open a remote desktop on brazos. You can then start your graphics program with the `vglrun command`, e.g.,::

	vglrun matlab -nosoftwareopengl

Loading software resources
--------------------------
Software on brazos is managed with the ``modules`` system. To see what software is available on brazos, use ``module avail``. Psychology software is available after first loading the `psyc` module
:: module load psyc
You can then use ``module avail`` to see which software packages, and which versions, are available. If you don't specify a version, he most recent version is loaded by default.

To load a specific version you need to give the version number, e.g.,
:: module load fsl/5.0.10

By loading a module you are configuring paths and loading any dependent software. Once you load a software module, the executables should be available, e.g.,
:: 	

	$ which fsl
		/usr/bin/which: no fsl in (/home/joseph.orr/bin:/usr/lib64/qt-3.3/bin:/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin/brazos)
	$ module load psyc
	$ module load fsl
	$ which fsl
		/apps/psyc/fsl/5.0.11/bin/fsl

Submitting jobs
---------------
When you login to the cluster, you are on on the login node, which is only meant for simple processes like editing text files or copying a small number of files. More complicated jobs should be submitted to the compute nodes with the job manager.

Brazos (as well as terra) use ``slurm`` for managing jobs, an open source package which is used on most academic computing clusters. Brazos has a good guide to `slurm <http://www.brazos.tamu.edu/docs/slurm.html>`_. A job is submitted with ``sbatch`` and monitored with ``squeue``. If you need to actively monitor or interface with a job, you can start an interactive job with the wrapper ``sintr``. This will queue the resources needed on a compute node.

FSL has a built in program for submitting jobs to slurm called ``fsl_sub`` that is used instead of ``sbatch``, and actually uses ``sbatch`` to submit your job to slurm. However, many packages will self-submit so you won't need to use ``fsl_sub``. A list of this programs is found `here <https://fsl.fmrib.ox.ac.uk/fsl/fslwiki/SGE%20submission%20FAQ>`_, and includes ``feat``.

Running containerized jobs  
~~~~~~~~~~~~~~~~~~~~~~~~~~
There is a growing trend in software to use *containers*, which are "lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings." Containers are useful for running software that require lots of library dependencies that might lead to compatability issues with software already on a system. **Docker** is a leader in the field of container packaging, and there are already several docker container images available for neuroimaging software, e.g., ``fmriprep`` and ``mriqc``. However, docker containers have issues with security on HPC systems, but there is a workaround called ``singularity``. ``singularity`` is available on ``brazos`` for running container images. Currently, the following containers are available on ``brazos``:

- fmriprep (v. 1.1.4)
- bidskit (v. 1.1.2)
- mriqc (v. 0.14.2)

Contact joseph.orr@tamu.edu for adding new container images to brazos. Your container image will be placed in ``/apps/psyc/containers``

submitting container jobs to slurm
----------------------------------
To submit a container job to slurm, you must create a submission script as discucced above (Brazos>Submitting jobs). The submission script will can singularity as follows::	

	singularity run <container>.simg <container commands>


===================
Terra Supercomputer
===================
The `terra` machine is the newest cluster available to members of TAMU through the High Performance Research Computing (HPRC) Center. Unlike `brazos`, `terra` is considered a `high-performance` cluster as opposed to a `high-throughput` cluster, meaning that it is optimized for highly parallel operations. The compute nodes generally have much less RAM that the compute nodes on brazos nodes. However, terra has many more cores than blanca, so if you are running large-scale analyses, terra may be more useful than brazos. Terra also has a good number of GPU nodes for running CUDA-capable jobs, e.g., diffusion preprocessing. 

`terra user guide <https://hprc.tamu.edu/wiki/Terra>`_. The only fMRI software currently installed on terra is FSL v. 5.0.10, but you can request the installation of other software by `emailing the HPRC Help <mailto:help@hprc.tamu.edu>`_. 

Remote Visualization
--------------------
The HPRC Wiki has a great page on `remote visualization <https://hprc.tamu.edu/wiki/Terra:Remote-Viz>`_.