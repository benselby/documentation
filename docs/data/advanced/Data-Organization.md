## Who this is for
   - Anyone who has to work with the data stored in `/archive`
   - Anyone interested in how we manage the data in `/archive`

## Overview
The data on the TIGRlab system lives in `/archive/data` (this is identical to `/archive/data/data-2.0`). 

Briefly, MRI data is uploaded to our [XNAT database](http://da55.pet.utoronto.ca:5004), downloaded into `/archive/data` by [`datman`](https://github.com/TIGRLab/datman), and then further inspected and pre-processed by various pipelines managed by `datman`. All important metadata is also stored here regarding data quality and organization.

The folder structure for a typical project will look like this:
```
/archive/data/SPINS
    bin/
    data/
    docs/
    logs/
    metadata/
    qc/
    website/
    README.md
```

**bin/**

Contains scripts run on a particular project. Every project has a `run.sh` file, with is run ever night. This nightly run **manages everything found in data/**. If you have a question about what is / is not being done to our data, start here.

**data/**

The organization of the `data/` folder is roughly:

```
filetype/
    subject1/
        file1.nii.gz
        file2.nii.gz
    subject2/
        file1.nii.gz
        file2.nii.gz
```

The `filetype/` name tells you a little about what kind of data you should expect to find within, organized by each subject. Here are the most common ones for raw data:

+ `dcm/`: sample DICOMs taken from XNAT to facilitate DICOM header comparisons.
+ `zips/`: in studies collected at our scanner (`CMH`), the raw `.zip` files that come off of the MR server are stored here.
+ `dicom/`: softlinked files to the zip files in `zips/`, renamed to match our naming convention. Some of these renames are defined manually by the `metadata/scans.csv` file, described later.
+ `mnc/`: DICOM-to-MINC converted data.
+ `nii/`: DICOM-to-NIFTI converted data.
+ `nrrd/`: DICOM-to-nrrd converted data.
+ `bids/`: NIFTI-to-BIDS converted data.
+ `RESOURCES`: all 'other' (non dicom) data extracted from the XNAT server for a given subject. This could be things like the raw behavioural data from a subject, or the tech notes from the MR unit.

Accompanying these files are outputs from some of our analytic pipelines:

+ `dtifit/`: the outputs of [FSL's DTIFIT](http://fsl.fmrib.ox.ac.uk/fsl/fslwiki/FDT/UserGuide).
+ `enigmaDTI/`: the outputs of the [ENIGMA DTI](http://enigma.ini.usc.edu/ongoing/dti-working-group/) pipeline.
+ `freesurfer/`: the outputs of [Freesurfer](http://surfer.nmr.mgh.harvard.edu/fswiki/FreeSurferAnalysisPipelineOverview).
+ `hcp/`: the outputs of the HCP-conversion of the anatomical data (via [epitome](https://github.com/josephdviviano/epitome)).
+ `imob/`: the pre-processed data (via [epitome](https://github.com/josephdviviano/epitome)) and 1st level GLM analysis files from the imitate-observe experiment.
+ `ea/`: the pre-processed data (via [epitome](https://github.com/josephdviviano/epitome)) and 1st level GLM analysis files from the empathic accuracy experiment.
+ `rest/`: the pre-processed data (via [epitome](https://github.com/josephdviviano/epitome)) of the resting state experiment.

And some other, less organized folders might be there too:

+ `clinical/`: contains *timestamped* spreadsheets of cognitive variables pulled from the Slaight Centre.
+ `bkup/`: contains any zip files obtained via FTP, email, etc, that are too valuable to throw away, but should be available already somewhere else in the directories.

**docs/**

There is no specific organization of this folder, but it is important. Any study designs, MRI protocols, relevant grant documentation, forms, guides, and miscellaneous notes can be found here.

**logs/**

When `run.sh` runs, the logs form the pipelines are placed here. This is a good place to look for clues on why a pipeline might not have produced the correct outputs.

**metadata/**

This folder contains a lot of interested information used by `datman` to manage our data. It maps out subject names between old and new schemes, keeps track of bad scans, keeps track of expected data, and more:

+ `blacklist.csv`: Contains a list of bad files (at the scan level) that should **not** be exported from XNAT into the raw file formats. These files will not be available for analysis in any form due to data artifacts or incidental findings. A short note will accompany the scan identifying the reason for exclusion.
+ `greylist.csv`: A list of suspicious files. Not sure if they are OK to analyze or not (this is up to you), but still flagged for some reason. It's a good idea to look at this list before analyzing your data.
+ `checklist.csv`: Keeps track of the qc outputs that have been looked at, and those that are still outstanding.
+ `exportinfo.csv`: Uses [regular expressions, or regex](http://www.aivosto.com/vbtips/regex.html) to map between the 'DICOM discription field' of the header to the appropriate 'tag' (e.g., DTI-60, RST; see [Data Naming Convention](/data/introduction/Data-Naming) for more details). The tag is used by our pipelines to figure out which kind of analysis to do for a given dataset, and can be used by you to find all available data:

```
jdv@davinci:/archive/data-2.0/SPINS/data/nii$ find . -name '*RST*' > ~/list-of-SPINS-restingstate-nii.txt
jdv@davinci:/archive/data-2.0/SPINS/data/nii$ head ~/list-of-SPINS-restingstate-nii.txt 
./SPN01_MRC_PHA_FBN0054/SPN01_MRC_PHA_FBN0054_RST_04_EPI-3x3x4xTR2-Resting-Eye-closed.nii.gz
./SPN01_MRC_PHA_FBN0054/SPN01_MRC_PHA_FBN0054_RST_05_Resting-State-test-TR2.4sec.nii.gz
./SPN01_MRC_PHA_FBN0055/SPN01_MRC_PHA_FBN0055_RST_05_EPI-3x3x4xTR2-Resting-Eye-closed.nii.gz
./SPN01_MRC_PHA_FBN0055/SPN01_MRC_PHA_FBN0055_RST_06_Resting-State-test-TR2.4sec.nii.gz
./SPN01_ZHH_PHA_FBN0002/SPN01_ZHH_PHA_FBN0002_RST_03_Resting-State-212.nii.gz
./SPN01_CMH_0024_01/SPN01_CMH_0024_01_01_RST_08_AxEPI-RestingState.nii.gz
./SPN01_CMH_0001_01/SPN01_CMH_0001_01_01_RST_07_Ax-RestingState.nii.gz
./SPN01_CMH_0033_01/SPN01_CMH_0033_01_01_RST_08_AxEPI-RestingState.nii.gz
./SPN01_MRC_PHA_FBN0044/SPN01_MRC_PHA_FBN0044_RST_04_EPI-3x3x4xTR2-Resting-Eye-closed.nii.gz
./SPN01_CMH_PHA_FBN0052/SPN01_CMH_PHA_FBN0052_RST_03_Ax-EPI-RestingState.nii.gz
```

+ `gold_standards/`: a folder structure containing known good `.dcm` files. This is used by a header-comparison tool to ensure that vital settings (e.g., TE, TR, flip angle) are not changing between scans during a scan.
+ `protocols/`: a list of MRI protocols from CAMH and any other external sites. These should include the expected scans and most of the important MRI settings.
+ `design/`: These are files associated with the experimental design (e.g., stimulus files for fMRI tasks). Meant mostly as a backup of the code.

**qc/**

Contains, for each subject, the full QC output as an `html` page. Also contains `subject-qc.db`, which contains QC metrics as a sqlite database.

**website/**

Some studies have websites for tracking the QC of the phantom data over time. This is where we store a github pages - formatted (via jekyll) website that we push to github to be rendered as a subdomain of our website: http://imaging-genetics.camh.ca/. For a list of the currently active websites, see here: http://imaging-genetics.camh.ca/database/qc/
 
