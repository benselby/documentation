## Who this is for:
  - Lab members who need to set up a new study in the archive
  - Those who want to learn more about how we set up our studies

## Setup process
Brace yourself, this will be a lot. We need to make this easier in the future... :( Some steps can be returned to later or done out of order, but for a fully set up study everything in this list should be checked off!

**NOTE:** make sure the study folder and everything in it is owned by clevis and the group kimel_data!

## 1. Add project folders and a README file
  - Add a folder in `/archive/data/` with the acronym/short form name of the new study
  - Inside this new folder create the following subfolders: `bin`, `data`, `docs`, `logs`, `metadata`, `pipelines`, and `qc`  
  - Add a `README.md` file directly inside the study folder describing the study, who the PI is and any other relevant / useful info. This README is the same one that is editable from the dashboard and can later be updated from there as well once your study has been added.

## 2. Fill in the 'docs' folder
  - Get study protocols for each site, make a `protocols` folder and add them there.
  - If you have contact info for other sites, make a `contacts` folder and add a file for each site following a `contacts-$SITE-$DATE` naming convention. The date should reflect the most recent update of the info.
  - If you have any REB documentation for any study sites make a folder that follows the naming convention `$SITE_REB` and add it there
  - If you have any files documenting standard operating procedure for different sites add them to a folder named `SOPs`

## 3. Set up XNAT
  If you haven't used XNAT before, find out how to do attend the training course, set up an account, and about our naming conventions [here](data/advanced/XNAT.md).
  - Add any new accounts for the PI and any research assistants/collaborators they're known to have (this can be skipped till later) 
  - Request a new project for your study following the instructions on the XNAT wiki.
  - After creating the study go to the 'access' tab and set the correct permissions
    - Default project permission should be 'private'
    - Add 'clevis', the PI, and any employees in the lab who will help manage the study as 'owners'
    - Add any RAs from other sites as 'Members' (they can update and add but can't delete data)

## 4. (CAMH only) Update REDCap's scan completed survey 
   - Go to https://edc.camhx.ca/redcap/
   - For the 'Scan Completed' survey add the new study to the options for study and add any research assistants to the list. If you don't have REDCap access to do this yet ask another staff member.

## 5. Add the expected files to the study's metadata folder
  - Add a file named `blacklist.csv` with two _space or tab separated_ columns named 'series' and 'reason'. Many pipeline scripts try to read this file, and the dashboard may try to write to it on behalf of users, so it needs to exist even if nothing has been blacklisted yet.
  - Add a 'scans.csv' file to hold name mappings for zip files that don't have the correct scan names in the 'PatientName' dicom field. This file should have the following _space or tab separated_ column headers (case sensitive!): source_name, target_name, PatientName, and StudyID.
  - If you have any study design related files (e.g. event timing files), add a folder called 'design' and save them there for later reference.
  - (only if REDCAP scan completed/ID sharing used) Add the redcap token in a file named 'redcap-token'. If the study has more than one redcap server to access (e.g. other sites have their own external server) each additional token can be added in its own file under whatever name you like, just be sure to provide the file name with the 'REDCAP_TOKEN' setting in each site's configuration ([see here](https://github.com/TIGRLab/datman/wiki/Study-Config#redcap-settings) for more info).
  - (Only if SFTP server used) Add the FTP server password in a text file. For CAMH scans this is expected in a file named 'mrftppass.txt'. For TONI or any other site you can set whatever name you like but you must provide your file name in the project settings file using the 'MRFTPPASS' setting ([see here](https://github.com/TIGRLab/datman/wiki/Study-Config#unique-site-sftp-server))

## 6. Create the project settings file  
  - To fill in this file you will need the following:  
    - A list of expected scan types for each site in the study
    - Knowledge of what will be in the actual SeriesDescription fields for the dicoms received. That is, you will probably need at least one scan to have been completed already. Pro-tip: You can easily get info from your dicom headers with 'dcmdump' (its installed on all our workstations). For example: 
      ```shell
           # Get the series description from a dicom in a series
           dcmdump --search SeriesDescription $PATH_TO_A_DICOM
      ```
      If you omit the search option it will show you the whole header. Note that the search option is case sensitive (i.e. 'SeriesDescription' works but 'seriesdescription' does not).
    - (CAMH/TONI only) an FTP username and password as well as the name of the folder(s) associated with the project on the MR FTP server.
  - In `/archive/code/config` add a file named `$YOURSTUDY_settings.yml`
  - For detailed info on filling this file [see here](https://github.com/TIGRLab/datman/wiki/Study-Config). See the example at the bottom of that page for a template (or copy one from another study) to make life easier.

## 7. Add your study's settings to the site config file  
  - Inside `/archive/code/config/tigrlab_config.yml` in the 'Projects' section add your study and its settings file name to the list.
  - If you created any new scan tags (i.e. a tag that has never been used by any study before) add an entry for each new tag to the 'ExportSettings' section in `tigrlab_config.yml`
  - Check that your file is being found and is free of syntax errors by loading it into datman:
```shell
# In your terminal
module load /archive/code/packages.module
source activate
ipython

# Inside python
import datman.config
config = datman.config.config(study="YOURSTUDYNAMEHERE")
```
If the last command ran without errors, you're set!

## 8. Add the study to the dashboard
  - **As clevis** run the dashboard's add_study_info.py script to add the new study's info to the dashboard (this reads the configuration files from the previous two steps)  

    ```shell
     module load /archive/code/packages.module
     source activate
     add_study_info.py
    ```
  - Confirm that it read the config files by visiting srv-dashboard.camhres.ca. If the new study is in the list on the main page your settings have likely been correctly read. To be 100% certain that _everything_ is set up correctly, though, check back after the nightly pipelines have run at least once and make sure the study's scans have been added correctly.
  - For everyone who will be QCing this study set up their study access:
    1. For those who don't have github accounts, get them to make one.
    2. Make sure the github accounts for every QCer have been added as members of TIGRLab on github (they need this to add issues)
    3. Have users attempt to log in to srv-dashboard.camhres.ca if they've never used the dashboard before. Every user has to try to access the dashboard at least once before they can be granted access to any studies.
    4. Find each person in the list on ['admin/users' page](http://srv-dashboard.camhres.ca/users) and update their permissions to let them access the new study. **NOTE:** QCers will **not** need admin access or PHI access.
    5. New QCers should be scheduled for QC training with our lab!

## 9. Set up the run script(s)
  - Add a script named `$YOURSTUDY_management.sh` to `/archive/code/config/` to turn on datman's core data management scripts ([see here](/data/advanced/Archive-Pipelines?id=management) for more info on each step). Copy another study's script to make your life easier :)
  - NOTE: If your study has data shared from another study (i.e. scan sessions shared across studies under multiple names) you'll need to make sure the 'dm_link_shared_ids.py' script is in your run script, and have set up redcap (step 4) for the study for links to the data to be made.
  - (Optional) Add a `$YOURSTUDY_analysis.sh` script to `/archive/code/config` to make use of the minimally preprocessed pipelines. [See here](/data/advanced/Archive-Pipelines?id=analysis) for more info on the scripts available.
  - Make sure the management and/or analysis scripts are executable!
  - Make a link in the study's bin folder named `run_data_kimel.sh` that points to the management script in `/archive/code/config/`. If you also have an 'analysis' script, make a _relative_ link (so the link doesnt break on the SCC) named `run_pipelines_scc.sh` pointing to your script.
  - Get your management script (and/or analysis script) to run nightly by updating `/archive/code/bin/run.sh` (or for analysis: `/archive/code/bin/runall_pipelines_scc.sh`). **NOTE:** make sure to add the new study both to the list of variables at the beginning of the script and also the list in 'all_projects' that appears right after.

## 10. Set up QC Gold Standards
After the nightly pipelines have run once and you have some data you can set up gold standards for each site in your study. These are used by dm_qc_report.py to detect changes in the scan parameters from the headers. Currently, our gold standards files are just copied from the `data/dcm` folder. We copy one dicom per series from the first good scan that has correct parameters from each site. These should be added to a `standards` folder inside the study's `metadata` folder.

After setting up a new study, be sure to check back a few days in a row to make sure there are no errors. All study run script logs will be found in the logs at `/archive/logs`. The management script logs are named like `YYYY-MM-DD-run.log` and analysis script logs are named like `YYYY-MM-DD-run_scc.log`

## Common Problems
### The PatientName field for a study's scans are not correctly set (i.e. not the datman ID)
If it's just the occasional wrongly set PatientName field you just have to manually add an entry to `metadata/scans.csv`. Otherwise:
  - Check if the site you receive these scans from has a complete mapping of scan zip files to subject IDs. If so, move the contents of this list into `metadata/scans.csv`
  - If there is some sort of pattern to the misnaming, or enough information for you to derive the correct name, use `datman/scan_list.py` to create a script that will automatically update `metadata/scans.csv`. This script would usually go in a bin folder inside the study and would be added to the study's management script right before the dm_link.py step (which reads scans.csv) runs. See `/archive/data/NEUR/bin/generate_scan_list.py` or `/archive/data/SPINS/bin/generate_scan_list.py` for examples.
  - If neither of the above applies, manually add all your scans to `metadata/scans.csv` as they come in and cry :(