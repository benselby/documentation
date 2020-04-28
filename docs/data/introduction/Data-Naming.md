# Who is this for?
  - Anyone who will access the data stored in `/archive`
  - Anyone who will help curate the data stored in `/archive`

This document describes a naming protocol for our data files that explicitly indicates who, what, when, and where the data was acquired. The naming protocol is inspired by the  [OBI naming scheme](https://github.com/TIGRLab/spins/raw/master/docs/guides/spred-upload-tutorial-v1.5.pdf). 

# Data Naming Protocol 

## Standard Prefix

Generally, each data file should start with a string that follows this pattern: 

```
[study]_[site]_[subjectid]_[timepoint]_[session]
```

Where, 
- `[study]` is a short code indicating the overall study (eg. "`DTI`") and may be modified to indicate major study protocol differences (e.g. "`COGBDY`" vs. "COGBDO" to distinguish between young and older COGBD protocols).
- `[site]` indicates the site/scanner used (e.g. `CMH` indicates the CAMH 3T scanner)
- `[timepoint]` indicates which time point in the study design the data was collected for (e.g. 01 for baseline, 02 for 5yr follow up, etc..)
- `[session]` is a sequential numbering over scans at each time point in order that repeats/redos are obvious (i.e. the first scan at time point `01` is labeled `_01_01`, and a redo scan to collect a mis-acquired sequence would be labeled with as `_01_02`). 

For example, the first time a follow up scan for subject H240 is completed in the "DTI" study using the CAMH scanner, we might label it as `DTI_CMH_H240_02_01`. If the subject had to come in a second day to complete or repeat some acquisitions, we would label those files as `DTI_CMH_H240_02_02` (i.e. a session code of '02' because this is the second time they've come in for the '02' timepoint). 

The following table lists  our current studies by name, study and site codes to use in the naming, and a few example file names to illustrate this naming protocol in action: 

| Study Name       | Study Code | Sites                  | Examples                   | 
|------------------|------------|------------------------|----------------------------| 
| Anorexa study    | ANDT       | CMH                    | `ANDT_CMH_201_01_01`       |
| Autism study     | ASDD       | CMH                    | `ASDD_CMH_EF00_01_01`      |
| DTI study        | DTI        | TGH, CMH               | `DTI_TGH_H001_01_01`       | 
| SPINS            | SPN01      | CMH, ZHH, MRC          | `SPN01_CMH_0002_01_02`     | 
| COGBD older      | COGBDO     | CMH                    | `COGBDO_CMH_203211_01_01`  | 
| COGBD younger    | COGBDY     | CMH                    | `COGBDY_CMH_208260_01_01`  |  
| rTMS working mem | RTMSWM     | CMH                    | `RMTSWM_CMH_WM001_02_01`   | 
| STOPPDII         | STOPPD     | MAS, PMC, NKI, CMH     | `STOPPD_PMC_310010_02_01`  |
| PRELAPSE         | PRE01      | many :D                |                            |

## Sites

| Site                                   | Code |
|----------------------------------------|------|
| Toronto General Hospital               | TGH  |
| Centre for Addiction and Mental Health | CMH  |      
| University of Massachusetts            | MAS  |
| Maryland Psychiatric Research Centre   | MRC  | 
| Zucker Hillside Hospital               | ZHH  | 
| Pittsburg Medical Center               | PMC  |
| Nathan Kline Institute                 | NKI  |
| U Iowa College of Med                  | IWA  |
| U Minnesota                            | MIN  |
| Grady Hospital                         | GRD  |
| Cherry Street                          | CRY  |
| Borgess WMU School of Medicine         | BRG  |
| Creighton U.                           | CTN  |
| Peace Health                           | PCE  |
| Stanford University                    | SFD  |
| U Texas                                | TXS  |
| U Michigan                             | MCH  |


## Naming Scans Within Exams

The naming scheme above specifies naming an entire dataset acquired at a point in time, but the naming scheme can be extended to label different kinds of data within that acquisition. For instance, in an MR exam, each series could be labelled separately to indicate T1 vs. T2 vs. DTI data.

Generally, extending naming should follow this convention: 

```
<prefix>_[tag]_[series]_[description]
```

where 
- `<prefix>` is the full prefix as described above.
- `[tag]` is a friendly short code to indicate the type of data, e.g. `T1`, `T2`, `DTI33-1000`
- `[series]` is a number enumerating this acquisition within the exam. 
- `[description]` is longer and more specific string that describes the data. It should not contain 
underscores (_) or spaces.  For instance, for exam series, the description field should be mangled (see below) version of the MR scanner's series description field, e.g. `AXIAL-DIFFUSION-TENSOR-23`

For instance, for the exam `DTI_TGH_H001_02_01`, we would have the following extended file names: 

| Series Description      | Full Name |
| ----------------------- | ---------------- |
| 3-PLANE LOC             | `DTI_TGH_H001_02_01_LOC_01_3-PLANE-LOC` |
| Calibration             | `DTI_TGH_H001_02_01__CAL_04_Calibration` |
| AXIAL 3D SPGR IR PREP   | `DTI_TGH_H001_02_01_T1_04_AXIAL-3D-SPGR-IR-PREP` |
| AX FSE PD/T2 2.0 mm     | `DTI_TGH_H001_02_01_PD_09_AX-FSE-PD-T2-2.0-mm` |
| AX FLAIR                | `DTI_TGH_H001_02_01_FLAIR_12_AX-FLAIR` |
| AXIAL DIFFUSION TENSOR (23) | `DTI_TGH_H001_02_01_DTI23-1000_10_AXIAL-DIFFUSION-TENSOR-23` |
| AXIAL DIFFUSION TENSOR (23) | `DTI_TGH_H001_02_01_DTI23-1000_04_AXIAL-DIFFUSION-TENSOR-23` |
| AXIAL DIFFUSION TENSOR (23) | `DTI_TGH_H001_02_01_DTI23-1000_05_AXIAL-DIFFUSION-TENSOR-23` |

**Mangling** is simply this: characters that aren't numbers or letters are converted to a `-` character, and runs of `-`s are converted to a single `-`.

# Study Naming

The protocol above outlines how data files should be unambiguously named after participants and sites are given identifiers. *This* section describes how, for our specific studies, we will generate new identifiers. 

## DTI Study 
Subject identifiers follow the format: 

> diagnosis code (`H` or `S`) + four digit number

For example, `H003` or `S080`. H = healthy control, S = schizophrenia. Historically there a few variations on this theme when the identifiers are written down:

 1. Identifiers may start with `06`, e.g. `06H003` or `06S080`. 
 2. Identifiers may end with `_2` to indicate a follow up scan. E.g. `H003_2` or even `06H008_2`. 

Nevertheless, ongoing, when we refer to a DTI subject identifier, we mean the four character code version (e.g. 'H003', 'S080', ...). 

### Study protocol changes, and interaction with the SPINS study

The DTI study has undergone several changes in protocol (**TODO:** ref study protocol documents). However, essentially there have been two phases, pre-SPINS and post-SPINS. In the pre-SPINS phase, all participants were given IDs as described above, and scanned at TGH and CAMH sites. Once the SPINS study started, all participants under 51 are *also* participants of SPINS, and so are given *both* a DTI and a SPINS identifier (and, they are acquired at CAMH under the MR unit study code SPIN1MR). Participants 51 and older are *only* part of the DTI study (and, acquired using the CAMH `DTI3MR` study code).

Participants that have had an baseline scan pre-SPINS (and so only have a DTI identifier) but then return for a follow-up scan are treated as described above (i.e. given a baseline SPINS identifier if they are under 51 years old, but also given a DTI identifier). 

Here's a table with some examples illustrating the above: 

| Age  | Subj | Scan Type | SPINS ID               | DTI ID               | 
|------|---------|-----------|------------------------|----------------------|
|  46  |    H240 | Baseline  | `SPN01_CMH_H240_01_01` | `DTI_CMH_H240_01_01` |
|  51  |    H240 | Followup | n/a                    | `DTI_CMH_H240_02_01` |
|  40  |    H107 | Baseline (pre-SPINS)    | n/a         | `DTI_CMH_H107_01_01` |
|  45  |    H107 | Followup (SPINS) | `SPN01_CMH_H107_01_01` | `DTI_CMH_H107_02_01` |

