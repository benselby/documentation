## Who this is for

- Lab members who help curate the `archive` data
- Lab members interested in knowing more about what scripts are run on the data in `/archive`
- Lab members who help manage datman (If you don't know what datman is, you don't need this page yet!)

## Intro

In `/archive/code/bin` there are two scripts, `run.sh` and `runall_pipelines_scc.sh`, that are run nightly as a cron job on tigrsrv. These scripts launch the management and analysis scripts, respectively, for all studies. Everything is run as user clevis.

## Manually running stages

If a subject needs to be sped through the pipelines a management or analysis script can be manually run step by step. Ideally, you should switch to clevis if you do this to prevent permissions problems. Certain stages of the pipeline are optional or can be run out of order but some stages require certain user intervention (e.g. signing off on QC, fixing session name errors) or prerequisite stages, so consult the relevant sections under [Management](#management-pipeline) and [Analysis](#analysis-pipeline) for any stages you wish to run.

### Module prerequisites

Some modules must be loaded for any datman scripts to be run. Analysis stages may require one of two datman versions on the SCC due to the fact that some of our pipelines scripts are legacy code still in the process of being updated. Individual steps in the pipeline may also have their own additional module dependencies or variables that need to be set and this should be copy pasted from the management/analysis script itself.

#### Management

Management pipeline scripts require the version of datman stored in `/archive/code/datman`, which can be loaded with packages.module as shown below. The packages module also sets up access to the dashboard for any scripts that require it as well as several other dependencies.

```
module load /archive/code/config/packages.module
source activate
```

#### Analysis

To load any Kimel specific modules on the SCC you must first run:

```
module load /KIMEL/quarantine/modules/quarantine
```

**Up to date pipelines** scripts (currently only `dm_proc_freesurfer.py` and `dm_proc_fs2hcp.py`) use the copy of datman that is stored at `/archive/code/datman`. However, paths on the SCC differ so a different module file must be loaded:

```
module load datman/packages
source activate
```

**Older pipelines** scripts require a copy of datman that is 2+ years old and only exists on the SCC. Currently, these 'old' pipelines are every script found in our analysis shell scripts **except** `dm_proc_freesurfer.py` and `dm_proc_fs2hcp.py`. To load this old copy use:

```
module load datman/latest
```

The 'latest' version for this module is a legacy thing that we're unfortunately a bit stuck with at the moment, and this copy is in fact the oldest copy of datman we have lying around. This version of datman does **NOT** use the `<study>_settings.yml` files stored at `/archive/code/config` and as a result the scripts that use this version often require more environment variables to be set. Make sure to copy the giant chunk of 'export' statements at the beginning of the analysis script for the study if you must run one of these pipeline stages.

## Management pipeline

This section documents any special things to note if you must run a data management pipeline stage manually on a study that already has a complete `<study>_settings.yml` file (for documentation on setting up a config file see [here](https://github.com/TIGRLab/datman/wiki/Datman-Config)). Modules listed are in addition to the datman module loaded according to [module prerequisites](#module-prerequisites). Study specific stages are not documented here, and all scripts should have their own more detailed documentation viewable with `<scriptname> --help` if more information is needed.

**dm_blacklist_rm.py**
* Prerequisite stages: None
* Modules: None
* Environment Variables: None
* Human Intervention:  
    * Will only remove files for series that have been added to the blacklist.csv in the study's metadata folder.
* Other: None

**dm_sftp.py**
* Prerequisite stages: None
* Modules: None
* Environment Variables: None
* Human Intervention: None
* Other:
    * The user on the machine that this runs from must have the sftp server in its known hosts file. Clevis on tigrsrv is already correctly configured.
    * The user must have permission to read the mrftppass.txt file in the study's metadata folder. Clevis is already configured.

**dm_link.py**
* Prerequisite stages: None
* Modules: None
* Environment Variables: None
* Human Intervention:  
    * If a zip file does not have the correct datman ID in the PatientName field of its DICOMs then an entry must be added to the scans.csv file in the study's metadata folder. 
* Other: None

**dm_xnat_upload.py**
* Prerequisite stages: 
    * dm_link.py (Or a <study>/data/dicom folder full of properly named zip files produced by other means)
* Modules: None
* Environment Variables: None
* Human Intervention:
    * Rarely, zip files may contain DICOMs with more than one experiment UID. These zips tend to get caught in the pre-archive requiring them to be manually moved. The script should complain if this happens.
* Other: None

**dm_xnat_extract.py**
* Prerequisite stages:
    * dm_xnat_upload.py (Or access to any xnat project with datman named subjects / experiments already present)
* Modules:
    * slicer/4.4.0 or newer
    * minc-toolkit/1.0.01 or newer
    * mricron (included in `/archive/code/packages.module`)
* Environment Variables: None
* Human Intervention: None
* Other: None

**dm_link_shared_ids.py**
* Prerequisite stages: None
* Modules: None
* Environment Variables: None
* Human Intervention:  None 
* Other: 
    * The user running this script must have permission to read the redcap-token file in the study's metadata folder. Clevis should already have permission.

**dm_proc_split_pdt2.py**
* Prerequisite stages: None
* Modules: 
    * FSL
* Environment Variables: None
* Human Intervention:  None 
* Other: None

**dm_qc_report.py**
* Prerequisite stages: 
    * dm_xnat_extract.py
* Modules: 
    * matlab/R2014a 
    * AFNI/2014.12.16 
    * FSL/5.0.10
* Environment Variables: None
* Human Intervention:  None 
* Other: 
    * If a qc folder already exists for the participant it will need to be removed or the --rewrite flag needs to be used.

**dm_proc_fmri.py, dm_proc_rest.py, dm_proc_ea.py, dm_proc_imob.py**
* **NOTE**: These scripts technically are part of the analysis pipeline but are run under management because they're not configured to run on the SCC
* Prerequisite stages: 
    * dm_proc_fs2hcp.py
* Modules: 
    * matlab/R2014a
    * AFNI/2014.12.16
    * FSL/5.0.10
    * Other requirements may exist, but if so they are included in `/archive/code/packages.module`
* Environment Variables: None
* Human Intervention:
* Other: None

**dm_proc_dtiprep.py**
* **NOTE**: This script is technically part of the analysis pipeline, but is not setup to run on the SCC
* Prerequisite stages: None
* Modules:
    * slicer/4.5.0-20160714
* Environment Variables: None
* Human Intervention:  None 
* Other:
    * The machine that runs this script must have Singularity installed. All machines in our cluster should be configured already.

**dm_proc_tractmap.py**
* **NOTE**: This script is technically part of the analysis pipeline, but is not setup to run on the SCC
* Prerequisite stages: 
    * dm_proc_dtiprep.py
* Modules:
    * slicer/4.5.0-20160714
* Environment Variables: None
* Human Intervention:  None 
* Other:
    * The machine that runs this script must have Singularity installed. All machines in our cluster should be configured already.

## Analysis pipeline
This section notes any special considerations for running an analysis pipeline stage manually.

**dm-proc-dtifit.py**
* Prerequisite stages: None
* Modules: 
    * FSL/5.0.9 or greater
* Environment Variables: None
* Human Intervention:  None 
* Other: None

**dtifit-qc.py**
* Prerequisite stages: 
    * dm-proc-dtifit.py
* Modules: 
    * FSL/5.0.9 or greater
* Environment Variables: None
* Human Intervention:  None 
* Other: None

**dm-proc-enigmadti.py**
* Prerequisite stages:
    * dm-proc-dtifit.py
* Modules: 
    * FSL/5.0.9 
    * R/3.2.5 
    * ENIGMA-DTI/2015.01
* Environment Variables: None
* Human Intervention:  None 
* Other: None

**dm_proc_freesurfer.py**
* Prerequisite stages: None
* Modules: 
    * freesurfer
* Environment Variables: 
    * None. Note that SUBJECTS_DIR does **NOT** need to be set, and will not be checked, because the script uses recon-all's `-sd` option to set the output directory to the 'pipelines/freesurfer' folder. 
* Human Intervention:  
    * This will not run on a subject until the subject has been signed off on during quality control (i.e. they must have an entry in the study's checklist.csv file in the metadata folder)
* Other: None

**dm_proc_fs2hcp.py**
* Prerequisite stages: 
    * dm_proc_freesurfer.py
* Modules: 
    * freesurfer
    * FSL
    * connectome-workbench/1.2.3 or greater
    * hcp-pipelines/3.15.1 or greater
* Environment Variables: None
* Human Intervention:  None 
* Other: None
