### Note:  We've largely moved QC over to our new QC [Dashboard](/data/intermediate/QC---dashboard).

### A brief introduction to QC for external groups

[Generating the QC list](#page_facing_up-generating-the-qc-list)  
[Opening a QC document](#file_folder-opening-a-qc-document)  
[Evaluating Scan Completeness](#thought_balloon-evaluating-scan-completeness)  
[Evaluating Scan Quality](#thought_balloon-evaluating-scan-quality)  
[Signing off](#pencil2-signing-off)  
[Communication](#email-communication)

### :page_facing_up:  Generating the QC list

➕ Log into a Kimel computer in the lab or via VPN using your lab (not CAMH) ID  
➕  Open a terminal. (We tend to use the _Terminator_ application, as it allows us to open multiple windows.)  
➕  Load the necessary module by typing `module load /archive/code/datman.module`  
➕  Run the necessary script by typing `dm-qc-todo.py`  

#### What am I looking at?

If everything worked, you'll see something that looks like [this](https://cloud.githubusercontent.com/assets/13733654/19321337/9e26f8a6-9082-11e6-8a06-a839d5d44ecf.png). 

The list generated as described above indicates all of the scans that have yet to be signed off on at a given point in time. Our system looks for new scans once per day and adds to this list. When a scan is signed off on, it will be removed from the list in real-time (you'll have to rerun `dm-qc-todo.py` to regenerate the list).

If a scan that you expect to see, on the basis of your knowledge of the MRI Scheduler or a 'scan completed' email, doesn't appear on the list the day _after_ the scan was completed, something has gone wrong, likely with the naming convention that our system relies on. Email your QC contact or post an issue on our [GitHub issues page](https://github.com/tigrlab/admin/issues).

Various staff and students have been assigned to QC incoming scans for a particular study. Look out for the study name that you've been assigned in the third component of the path, and a given subject's ID in the fifth and sixth components of the path `archive/data-2.0/STUDYNAME/qc/SUBJECTID/qc_SUBJECTID.html`.

### :file_folder:  Opening a QC document

➕  For ease of use, right click on the terminal screen and split the screen. This allows you to keep the QC list intact while working in a separate window  
➕  In the QC list window, copy the entire path (starting at `archive` and ending at `.html`) of a QC document. You'll need to use the mouse, not the keyboard, to copy  
➕  In the separate working window, type 'firefox' and paste the path. Again, use the mouse to paste

#### What am I looking at?

What you've done in the terminal should look something like [this](https://cloud.githubusercontent.com/assets/13733654/19322005/4511204a-9085-11e6-943c-c4a5b0daa333.png), and when you press enter, you should see a new window open. This is the QC document. It has 3 distinct components: (i) a header, which lists all expected acquisition types, (ii) a link to Tech Notes, and (iii) visualizations of all collected data. 

### :thought_balloon:  Evaluating Scan Completeness

An important aspect of QC is to make sure that we have all our data - and a lot can go wrong here. Basically, we need to track down any missing data that really exists, and have an explanation for any data that really is absent.

If something is missing, email your QC contact or post on our [GitHub issues page](https://github.com/tigrlab/admin/issues).

#### How do I know if data is missing?

**Header.** Review the header of the QC document. If you see "Missing" in the 'Notes' column, cross-reference with the Tech Notes (and RA notes, if applicable). If the Tech Notes indicate that that acquisition was not collected, that explains why we don't have it - you'll make a note on `checklist.csv`, and that's that (discussed further below). If the Tech Notes indicate that the acquisition _was_ collected, something has gone wrong somewhere along the way, and we will have to hunt the file down.

**Body.** Review the body of the QC document, i.e., the visualizations. If you see a file name that is not proceeded by associated images, it may be that something is wrong with the file itself or our image generation. Usually, where the images should be will be encased by a border or filled with blank space, but not always.  

Please note that, at present, we do not have visualizations for FMAPS and MRS scans, so it should be expected that those files names are not proceeded by any images.

### :thought_balloon:  Evaluating Scan Quality
In addition to making sure our data is complete, we need to have an idea of the data's quality. Poor quality might be the result of participant behaviour (motion, metal), MRI Tech error (FOV positioning, header differences), and/or scanner/computational problems (usually related to head coil and reconstruction). We have several automatic processes that help identify problematic data at a later stage, by some things are best - and most quickly - detected by eye. 

#### What should I be doing?

When we evaluate scan quality, we're essentially taking a quick look over the data to ensure that everything looks as it should, and notifying the appropriate parties if it doesn't. More specifically, we are looking out for (i) images that are not biologically plausible, (ii) geometric-looking distortions, and (iii) obvious and unexpected pathology. 

Common issues of this sort are nicely illustrated [here](https://github.com/TIGRLab/documentation/files/530007/qual-qc-manual.pdf). If you're unsure if what you're seeing is a problem, post an issue on our [GitHub issues page](https://github.com/tigrlab/admin/issues) with the `scan artefacts` label. 

#### Interpreting header differences. 
Header differences are printed on the QC document. Header differences flag any unexpected difference between the parameters of a given scan and an earlier, "gold standard" scan that is known to represent correct parameters. 
Some differences aren't consequential. However, some differences are integral, as diverging parameters of some kinds mean that data cannot be compared across subjects. Gradually, we are expanding our list of differences that can be safely ignored so that only important header difference flags are raised. If your QC documents indicate any header differences, please email your QC contact or post an issue on our [GitHub issues page](https://github.com/tigrlab/admin/issues) with the `header differences` label. 

In some unusual cases, flagged header differences that we know to be important can be ignored. For example, when a scan is repeated (due to scanner issues or participant behaviour), some scan parameters may be altered by the MRI Tech for want of time. Flags that can be ignored in this circumstance will related to number of excitations (NEX) value, and include `NumberOfAverages` and `PercentSampling`.

#### Discarding data

Sometimes, data should be discarded. The first, most common example of when this is the case is if the Tech Notes indicate that a particular scan was re-acquired (usually due to scanner malfunction or participant behaviour). If the second acquisition was successful, the Tech Notes might recommend that the scan's first instance should be "discarded". You'll see another indication of this as "Repeat" will be noted beside the scan's second instance in the 'Notes' column of the QC document header. 

The second situation in which you might want to delete data is if an acquisition's quality is so poor that it's unusable without question, even if the particular scan has not been re-acquired.

##### Deleting
➕ In the terminal, navigate to your study's data folder by typing `cd ~/../../archive/data/STUDYNAME/data`    
➕ Copy the file name of the acquisition, _without_ the `.nii.gz` extension, from the _header_ of the QC document    
➕ In the terminal, type `find . -name FILENAME*`  
➕ If everything worked, you'll see at least one file printed to the screen 
➕ Confirm that you have entered the right file name. When content, type the same command you did before, with `-exec rm -r {} + ` appended to the end (press the 'up' arrow key so that you don't have to type the first command twice)  
➕ Ensure that the data is deleted by rerunning the original find command `find . -name FILENAME*`  

If you've made a mistake, don't panic. Deleting data from our file system doesn't mean it's gone forever. But please let us know right away.

##### Blacklisting
If you followed the steps above, you've successfully deleted the data. Now, we need to ensure that the data doesn't come back (our system looks for discrepancies between our file system and our imaging database, and it brings in discrepant files that have not been blacklisted). In short, we ensure that deleted files stay deleted by adding those files to `blacklist.csv`.

➕ In the terminal, navigate to `cd ~/../../archive/data/STUDYNAME/metadata`  
➕ Open `blacklist.csv` by typing `nano blacklist.csv` (you can use a text editor other than nano, such as vi, if you'd prefer)      
➕ Scroll to the bottom of the list, and paste the file name of the acquisition, _without_ the `.nii.gz` extension, as you did when deleting the file  
➕ Indicate the reason that the scan has been deleted/blacklisted  
➕ When complete, type `Ctrl``X`, `Y`, and then `Enter` to save and exit blacklist.csv    

### :pencil2: Signing off 

Once you are certain that (i) the participant's scan in our system is as complete as it will ever be and (ii) you have clarified/resolved any issues, you can sign off on the scan.  Do not sign off on a scan until all issues have been resolved, as the act of signing off triggers various processing pipelines.

➕ Split the terminal screen  
➕ Navigate to the given study folder by typing `cd ~/../../archive/data/STUDYNAME`  
➕ Open the given study's checklist by typing `nano metadata/checklist.csv` (you can use a text editor other than nano, such as vi, if you'd prefer). What you've done should look something like [this](https://cloud.githubusercontent.com/assets/13733654/19323089/8e918f1c-9089-11e6-9504-3c654e171f6a.png)  
➕ When you press enter, your current window will be replaced with a list of all past and present QC documents that have been/need to be signed off on. Your window should look something like [this](https://cloud.githubusercontent.com/assets/13733654/19323346/84fb4ec4-908a-11e6-8ad3-af2b78c7f092.png)  
➕ Navigate to the given subject ID (displayed alphabetically, and generated automatically)  
➕ Type your initials    
➕ If applicable, include brief notes about (i) scan quality, (ii) missing or extra scans, (iv) 'blacklisted' scans, and (iv) participant behaviour as reported by MRI Tech or RA    
➕ When complete, type `Ctrl``X`, `Y`, and then `Enter` to save and exit checklist.csv  

### :email:  Communication 

It's essential to keep in mind that several of the issues you encounter while QCing may be of interest to others. For instance, if you notice that many participants exhibit extensive motion during an experimental task, you should inform the study RA that they might better emphasize the importance of staying still. If you notice MRI Tech or scanner-caused artifacts, you need to inform us and the MRI Centre, so that these artifacts are scrutinized elsewhere, and corrected. If you find that a protocol is often not completed for lack of time, you should ensure that the PI knows the frequency with which final series are being dropped. Or, if a particular participant's scan is virtually unusable due to poor quality (especially if the poor quality is _not_ a result of participant behaviour), the PI might want to be notified, so that the possibility of rescanning the participant can be considered.

These are but a few examples. The important point is that you communicate any findings that bear on others reliably and quickly.

##### Summary of links
[QC manual](https://github.com/TIGRLab/documentation/files/530007/qual-qc-manual.pdf)   
[TIGRlab QC staff contacts](https://docs.google.com/spreadsheets/d/1MTuU7Y7hqAHbiHMP2NsnrW1wZ9UZrmz4AQpJCQ1-_BQ/edit?usp=sharing)  
[TIGRlab GitHub 'issues' page](https://github.com/tigrlab/admin/issues)  
[TIGRlab 'scan completed' survey](http://tinyurl.com/scancompleted)   
[TIGRlab XNAT database](https://xnat.imaging-genetics.camh.ca)  
[CAMH REDCap account request form](https://edc.camhx.ca/redcap/surveys/?s=W8KRWYEJMX)  
Helpful [linux commands](http://linuxcommand.org/) & [linux tutorial](www.ee.surrey.ac.uk/Teaching/Unix/)