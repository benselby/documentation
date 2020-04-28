Office
======

In the case that you want to use things like microsoft office on your linux machine, you simply need to log into the citrix portal.

Log in with your CAMH credentials (same as the ones you use to log into webmail etc).

Under 'non-clinical', is a folder called microsoft office 2010, which contains all of your beloved productivity softwares.

NB: When you click on any of these programs, a small 'launch.ica' program will be downloaded and launched. If this fails to open it is likely because we need to install 'citrix receiver' on your machine.

http://docs.citrix.com/en-us/receiver/linux/13-1/linux-install.html

Civet
=====
A freesurfer alternative.

+ [Atlas Labels](http://www.bic.mni.mcgill.ca/ServicesSoftware/VisualGuides)

ENIGMA DTI
==========

**Improper Labels**

via http://enigma.ini.usc.edu/protocols/dti-protocols/:

> **An important note **in the analysis is that the ROI labeled “IFO” is actually different that the same ROI under the most current FSL JHU Atlas label. This was different in the older JHU atlas used to create the template and make the protocols. Please note this will not play a role in ENIGMA-DTI GWAS as we will not be using this ROI for GWAS (it is very small) but it may be considered for disorder studies.

> If groups choose to use these measures for their own analysis, please be advised that this should be uncinate according to the current atlas. To avoid confusion, we will NOT switch the labels back, but we will keep this warning so you can carefully examine your data.

This shows up in the file ENIGMA_look_up_table.txt as:

45      IFO-R           Inferior fronto-occipital fasciculus right
46      IFO-L           Inferior fronto-occipital fasciculus left
47      UNC-R           Uncinate fasciculus right
48      UNC-L           Uncinate fasciculus left

whereas in the FSL JHU-labels.xml we have:

<label index="45" x="55" y="127" z="54">Uncinate fasciculus R</label>
<label index="46" x="123" y="125" z="58">Uncinate fasciculus L</label>
<label index="47" x="60" y="78" z="86">Tapetum R</label>
<label index="48" x="117" y="76" z="87">Tapetum L</label>

This means our enigma values as already computed are mislabeled. :-D

White Matter Analysis
=====================

This page documents how we're using Lauren O'Donnell's [whitematteranalysis](http://github.com/ljod/whitematteranalysis) tract clustering tool on our data. 

You should probably read the documents on her wiki to get started: 
http://github.com/ljod/whitematteranalysis/wiki

**Converting PickTracts group files to Model Hierarchies in a MRML file**

That's a mouthful, but roughly the situation is this: 

1. The [PickTracts](https://github.com/ljod/whitematteranalysis/wiki/Installing-Pick-Tracts) Slicer module allows you to interactively select clusters from a MRML scene file and group them. 
2. Unfortunately, to run any statistics on these groups (e.g. find mean FA per tract) we need to have the groups of clusters represented using the Models plugin. 
3. The Models plugin *does not* let you group clusters interactively by pointing and clicking on the image. So that sucks. 

Solution: @pipitone has written a helpful script that takes a PickTracts group file, and MRML scene file, and spits out a MRML scene file with the groups represented as Model Hierarchies. 

The script is here online: 
https://github.com/pipitone/whitematteranalysis/blob/picktracts-converter/bin/picktracts_converter.py

To use it on the TIGR lab machines, you run: 
```bash
$ module load whitematteranalysis/latest
$ export PATH=$PATH:/projects/jp/whitematteranalysis/bin/
```

Run `picktracts_converter.py --help` for usage info.

Neural Net Libraries
====================

This guide describes how to set up your system and GPU for neural network development using the Blocks/Theano and Caffe libraries.

**Useful Resources**

[NVIDIA CUDA Documentation](http://docs.nvidia.com/cuda/index.html)  
[Blocks](http://blocks.readthedocs.org/en/latest/index.html)  
[Theano](http://deeplearning.net/software/theano/index.html)  
[Caffe](http://caffe.berkeleyvision.org/)  
[Blocks Forums](https://groups.google.com/forum/#!forum/blocks-users)  
[Theano Forums](https://groups.google.com/forum/#!forum/theano-users)  
[Caffe Forums](https://groups.google.com/forum/#!forum/caffe-users)  

**GPU Drivers**

Training neural networks is hard work: you definitely want to let your GPU do the heavy lifting. This section describes how to upgrade your GPU drivers to the latest stable release which works with both of the neural network libraries described below.

**Do you have a CUDA compatible GPU?**

Check whether your system has an NVIDIA GPU from the command line:  

`$ lspci | grep VGA`

This will display which GPU is installed. Make sure it is CUDA compatible by verifying that it's listed somewhere here: [CUDA compatible NVIDIA GPUs](https://developer.nvidia.com/cuda-gpus)

**Upgrading Drivers**

Refer to this link for more specific info: [CUDA Getting Started](http://docs.nvidia.com/cuda/cuda-getting-started-guide-for-linux/index.html)

First, check which version of the drivers you currently have. The 311.* series of drivers may be installed by default, in which case you will need to upgrade to the current set of drivers.  
`$ nvidia-smi`

We upgrade the CUDA and GPU drivers using apt-get. First, download the 
[Ubuntu CUDA Network Installer](https://developer.nvidia.com/cuda-downloads) to disk, and install the cuda repository: `$ sudo dpkg -i cuda-repo-<distro>_<version>_<architecture>.deb`

Update your repository cache:  
`$ sudo apt-get update`

Upgrade your cuda drivers:  
`$ sudo apt-get install cuda-drivers`

Install the cuda toolkit:  
`$ sudo apt-get install cuda`

Update your $HOME/.bashrc file to contain the following environment variables:  
`export PATH=/usr/local/cuda-7.0/bin:$PATH`  
`export LD_LIBRARY_PATH=/usr/local/cuda-7.0/lib64:$LD_LIBRARY_PATH`

Verify that the Driver Version listed in the nvidia-smi output has changed to the current driver. You may need to reboot the system.

**Miniconda**

This section describes how to install Blocks/Theano into an anaconda virtual environment environment. Download the miniconda bash install script from [here](http://conda.pydata.org/miniconda.html), and run it. Once finished with the install, create a new environment for blocks/theano:  
`$ conda create -n myenv python=2.7`

Activate your new environment and install dependencies:  
`$ source activate myenv`  
`$ conda install hdf5 scipy h5py`  

You may want to add this variable to your $HOME/.bashrc file:  
`export HDF5_DIR=/path/to/your/new/conda/env/include/`

**Theano**

From your conda shell, install the bleeding-edge Theano:  
`$ pip install --no-deps git+git://github.com/Theano/Theano.git`

**Blocks**

Fork and locally clone Blocks as described in their [documentation](http://blocks.readthedocs.org/en/latest/setup.html)

From the same terminal where you activated your conda environment, install Blocks locally:  
`$ pip install -e git+git@github.com:USER/blocks.git#egg=blocks[test,docs] --src=$HOME \
  -r https://raw.githubusercontent.com/mila-udem/blocks/master/requirements.txt`

Set up an env variable for FUEL (Block's data library):  
`export FUEL_DATA_PATH=/path/to/your/data/folder/`

For real time plotting, you may want to install bokeh:  
`conda install bokeh`

**Caffe**

Caffe uses several dependencies that are not available for the anaconda virtual env, so the following section describes how to set up Caffe on your system and assumes you have sudo access. The instructions are based on the [Ubuntu Installation](http://caffe.berkeleyvision.org/install_apt.html) documentation.

All of the dependencies can be installed using `apt-get`:  
`$ sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libboost-all-dev libhdf5-serial-dev libatlas-base-dev python-dev libgflags-dev libgoogle-glog-dev liblmdb-dev protobuf-compiler`

Now locally clone Caffe directly from the github site: `$ git clone https://github.com/BVLC/caffe.git`

Create a copy of the Makefile.config.example file. Unless you have a specific virtual env set up for your python dependencies, or want to compile Caffe to use cuDNN, you should not need to edit it. See the [installation docs](http://caffe.berkeleyvision.org/installation.html) for details.

Make Caffe using, say, 8 threads for faster compilation:  
`make all -j8`  

Ensure all the tests pass:
`make test`  
`make runtest`

Add the built caffe binaries to your $PATH for ease of access:  
`export PATH=/path/to/caffe/build/tools:$PATH`
