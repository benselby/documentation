## MR Database Information
Neuroimaging data collected during the study will be stored in an **online MRI Database (XNAT) server** hosted by the Centre for Addiction and Mental Health (CAMH)’s **Krembil Centre for Neuroinformatics (KCNI)**. The following information can be used to access the database:

**Database:** https://xnat.camh.ca

**Documentation:** https://kcniconfluence.camh.ca/display/NPP/

**Contact:**  tigrlab@camh.ca

## Accessing the MRI Database
Project members requiring access to the KCNI XNAT instance will need to complete an [online training course](https://kcniconfluence.camh.ca/display/NPP/XNAT+Workshop) and then [request an XNAT user account](https://kcniconfluence.camh.ca/pages/viewpage.action?pageId=6455465#XNATIntroduction-NewUserRequest).


**Details on User Request Form:**
1. For the first question click **XNAT**.
2. Set **Aristotle Voineskos** as the primary investigator, the associated e-mail is aristotle.voineskos@camh.ca.
3. The Neuroinformatics platform code is the XNAT Project ID for your associated site. Refer to the naming conventions section below for more details.

**NOTE:**
The only browsers that are currently supported are FireFox, Microsoft Edge, and Safari. Internet explorer users will run into issues.

## XNAT Naming Convention
In order to facilitate automatic data management, imaging data uploaded to XNAT must adhere to a common naming convention as implemented by the Krembil Neuroinformatics Institute (KCNI) Platform at CAMH. Further information can be found in the following links:
- [XNAT Naming Convention](https://kcniconfluence.camh.ca/display/NPP/XNAT+Naming+Convention)

Data stored in XNAT adheres to the following hierarchy:
- Project
  - Subject A
    - Experiment A
      - Scan 1
      - Scan 2
      - Scan 3
    - Experiment B
      - Scan 1
      - Scan 2
      - Scan 3
  - Subject B
    - Experiment A
      - Scan 1
      - Scan 2
      - ...

## XNAT Data Upload Procedure

XNAT data upload must occur **as soon as possible** after the MR scanning session.


### Uploading DICOM Data

As much as is possible, data should be submitted to XNAT as a single .zip file containing all DICOM data from a single ‘session’ in the scanner. To upload DICOM data, use [this guide](https://kcniconfluence.camh.ca/display/NPP/MR+Upload+Instructions).

If using the Compressed Image Upload tool, please be patient. Transfers can take ~30 minutes or more for a complete dataset. We do not need demographic information (e.g. age, sex) for which XNAT displays data entry fields. You may enter this (non-identifying) information if you like, but it is not recommended nor analyzed.


### Uploading non-DICOM Data

**IMPORTANT:** NON-DICOM DATA CANNOT BE RECEIVED IN THE SINGLE .ZIP FILE. BEHAVIOURAL MEASURES, MRI TECH NOTES IN PDF FORM, AND SOME SITES’ PHYSIOLOGICAL SIGNAL FILES NEED TO BE UPLOADED SEPARATELY. 

Please create a separate file for these types of data in the participant’s ‘resources’ folder, in accordance with [these general instructions](https://kcniconfluence.camh.ca/display/NPP/Non-DICOM+Upload+Instructions). 

Any task data output (both .txt and .edat2 files) should be uploaded to a folder under ‘resources’ called ‘behav’. In the unusual situation that any NIfTI files are to be uploaded, they should be uploaded to a folder under ‘resources’ called ‘NII’.
