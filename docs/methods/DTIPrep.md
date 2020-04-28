# Setting up a new project for DTIPrep processing

DTIPrep expects a project specific protocol file `protocol.xml`. This ensures the scan collection parameters are the same across scans.
The protocol.xml file must be manually created.

1. Start the DTIPrep singularity container in `shell` mode, with /input directory pointing to a suitable example scan. The /output directory needs to point to a folder with write permission.

    `$ singularity shell -B <path_to_input_nrrd_folder>:/input -B <path_to_output>:/output <container>`

2. Start DTIPrep in interactive mode.

    `Singularity> $ DTIPrep`

3. Open NRRD file
[image]

4. Navigate to /input and open the example file.

5. On the protocol tab select `Default` to generate the default protocol.
[image]

6. Modify the protocol as required, *N.B.* capitalisation is important.

* Brainmasking

    If brain masking is required set:


| Parameter | Value | Notes |
| ------- | ---- | ----- | 
| BRAINMASK_bCheck | Yes | 
| BRAINMASK_method | 1 | Slicer masking |

* Dominant direction check

    The dominant direction check looks for artifacts thought to be caused by scanner bed vibration, these are particulary prevalent in older scanners and with smaller subjects (children / animals) [ref](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3906573/). 


| Parameter | Value | Notes |
| ------- | ---- | ----- | 
|DOMINANTDIRECTION_bCheck| Yes

* Denoising

    If required, it is recomended that only one of DENOISING_bCheck or JOINDENOISING_bCheck is enabled [ref](https://www.nitrc.org/docman/view.php/283/1083/quality_control_tutorial_for_DTI_draft_Nov29.pdf). 

7. Select *Save as* and navigate to `/output`. Save the protocol as `dtiprep_protocol.xml`.

8. Exit the DTIPrep GUI.

9. Exit the singularity container:

    `singularity> $ exit`

10. Move the new protocol file to the project metadata folder.
    `$ mv dtiprep_protocol.xml <path_to_project_metadata>