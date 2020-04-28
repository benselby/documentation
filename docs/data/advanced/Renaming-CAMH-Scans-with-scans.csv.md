## Who this is for
   - Anyone who helps curate the data in `/archive`

## Overview
You should first delete the misnamed files from XNAT and the file system. In the file system, delete the participant's files at the highest level, /archive/data/studyname. In XNAT, first delete the participant's MR data THEN delete the participant (make sure you delete their MR data first!). 

scans.csv is a file that can direct a participant's data to a new target name and folder. It has four columns that require filling in (the descriptions are included below):


| source_name      | target_name       | PatientName            | StudyID                    | 
|------------------|-------------------|------------------------|----------------------------| 
| zipfilename      | desired name      | from dicom preamble    | from dicom preamble        |


1. source_name: the name of the zip file associated with your participant's data. To fill this in, cd to the dicom directory and use 'ls -l'. It should list participantid.zip --> ../zipfilename.zip. Paste this zipfilename (no extension) under the source_name column. (N.B. zipfilename often does not match the participantid, which is why this step is necessary rather than just looking for your participant ID in /zips.) 

2. target_name: the desired new name for your participant, following all naming conventions.

Numbers 3 and 4 require values from the dicom preamble. Navigate to the zips folder. Type in:

`module load packages.module`

`archive-manifest.py zipfilename.zip`

You will see a csv formatted file. Grab the values from there. 

