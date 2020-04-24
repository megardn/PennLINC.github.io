---
layout: default
title: BIDS Curation
parent: Flywheel
nav_order: 2
---

# BIDS Curation
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Introduction

[Brain Imaging Data Structure (or BIDS)](https://bids.neuroimaging.io) is an important recent development in the neuroimaging field that provides guidelines on how to structure, name, and share neuroimaging data. By adhering to the BIDS specifications, you can:

- Save time trying to understand and reuse data acquired by other scientists
- Jump right in to using a plethora of data analysis tools (BIDS apps) that understand BIDS data with little to no hassle
- Export and share neuroimaging datasets with ease
- Quickly validate and identify problems with your data before putting them through processing or analysis pipelines

Flywheel supports BIDS and BIDS apps primarily as its means of curation. They have an out-of-the-box utility gear for curation (`curate-bids`) that is available to all users; unfortunately, for some of our legacy data we found that this tool was somewhat inflexible to our needs. In lieu of this, we developed `fw-heudiconv` (pronounced /f wu di kɑ n(v)/) — a Python driven toolkit for BIDS curation on Flywheel. As the name suggests, it is in many ways a port of the common neuroimaging tool [`heudiconv`](https://heudiconv.readthedocs.io/en/latest/) developed by NiPy. Our tool allows us to build on their work and implement BIDS curation on Flywheel.

In this tutorial we will discuss more about BIDS, and go through how to use `fw-heudiconv` to curate your data. We've found that this is most useful in cases where one is curating legacy data, so we will start there.

# BIDS

BIDS stands for Brain Imaging Directory Structure, and is a standard format for organizing and storing brain imaging data. It's a philosophy of organisation that prioritizes having imaging scans located in a nested directory structure, where the top level is the subject label, followed by the imaging session label, followed by the modality of measurement. Each imaging file (typically `.nii` format) must have an associated `.json` dictionary, known as a sidecar, that represents the image's metadata. Lastly, the filenames must adhere to a `{key}-{value}.{extension}` naming convention, where key-value pairs are separated by underscores `_`. A dataset description file and a .bidsignore file are also necessary. An example BIDS directory looks like this:
```
bids_dataset/
    .bidsignore
    dataset_description.json
    sub-1/
        anat/
            sub-1_T1w.json
            sub-1_T1w.nii.gz
        dwi/
            sub-1_acq-HASC55_run-1_dwi.bval
            sub-1_acq-HASC55_run-1_dwi.json
            sub-1_acq-HASC55_run-2_dwi.json
            sub-1_acq-HASC55_run-2_dwi.nii.gz
            sub-1_acq-HASC92_dwi.json
            sub-1_acq-HASC92_dwi.nii.gz
            sub-1_acq-RAND57_dwi.bvec
            sub-1_acq-RAND57_dwi.json
        fmap/
            sub-1_acq-j_epi.json
            sub-1_acq-j_epi.nii.gz
    sub-C412/
        ses-baseline/
            anat/
                sub-C412_ses-baseline_T1w.json
                sub-C412_ses-baseline_T1w.nii.gz
                sub-C412_ses-baseline_T2w.json
                sub-C412_ses-baseline_T2w.nii.gz
            dwi/
                sub-C412_ses-baseline_acq-HASC55_run-01_dwi.bval
                sub-C412_ses-baseline_acq-HASC55_run-01_dwi.bvec
                sub-C412_ses-baseline_acq-HASC55_run-01_dwi.json
                sub-C412_ses-baseline_acq-HASC55_run-01_dwi.nii.gz
                sub-C412_ses-baseline_acq-HASC55_run-02_dwi.bval
                sub-C412_ses-baseline_acq-HASC55_run-02_dwi.bvec
                sub-C412_ses-baseline_acq-HASC55_run-02_dwi.json
                sub-C412_ses-baseline_acq-HASC55_run-02_dwi.nii.gz
                sub-C412_ses-baseline_acq-HASC92_dwi.bval
                sub-C412_ses-baseline_acq-HASC92_dwi.bvec
                sub-C412_ses-baseline_acq-HASC92_dwi.json
                sub-C412_ses-baseline_acq-HASC92_dwi.nii.gz
                sub-C412_ses-baseline_acq-RAND57_dwi.bval
                sub-C412_ses-baseline_acq-RAND57_dwi.bvec
                sub-C412_ses-baseline_acq-RAND57_dwi.json
                sub-C412_ses-baseline_acq-RAND57_dwi.nii.gz
            fmap/
                sub-C412_ses-baseline_dir-PA_epi.bval
                sub-C412_ses-baseline_dir-PA_epi.bvec
                sub-C412_ses-baseline_dir-PA_epi.json
                sub-C412_ses-baseline_dir-PA_epi.nii.gz
        ses-02/
            fmap/
                sub-C412_ses-02_dir-AP_run-01_epi.json
                sub-C412_ses-02_dir-AP_run-01_epi.nii.gz
            func/
                sub-C412_ses-02_task-flanker_run-01_bold.json
                sub-C412_ses-02_task-flanker_run-01_bold.nii.gz
                sub-C412_ses-02_task-flanker_run-01_events.tsv

```
`fw-heudiconv` allows users to curate datasets into BIDS on Flywheel. For more information on BIDS, as well as in-depth descriptions of the valid {key}-{value} pairs and how to use them, see their [readthedocs.io](https://bids-specification.readthedocs.io/en/stable/).

# What is a Heuristic?

As mentioned previously, a heuristic is a discrete set of rules specifying how to name different imaging files in a directory, based on properties in the image header. For `fw-heudiconv`, this set of rules is best specified in a python file. Here's an example:

```
#!/usr/bin/env python
"""Heuristic for mapping Brain RF1 scans into BIDS"""
import os


def create_key(template, outtype=('nii.gz',), annotation_classes=None):
    if template is None or not template:
        raise ValueError('Template must be a valid format string')
    return template, outtype, annotation_classes


# Baseline session
t1w = create_key(
    'sub-{subject}/{session}/anat/sub-{subject}_{session}_T1w')
t2w = create_key(
    'sub-{subject}/{session}/anat/sub-{subject}_{session}_T2w')

# ASL Scans
mean_perf = create_key(
    'sub-{subject}/{session}/asl/sub-{subject}_{session}_CBF')
raw_asl = create_key(
    'sub-{subject}/{session}/asl/sub-{subject}_{session}_asl')
m0 = create_key(
    'sub-{subject}/{session}/asl/sub-{subject}_{session}_MZeroScan')

# tms session
# conditions (run 1)
nback_HiConHiLoWMgated_run1 = create_key(
    'sub-{subject}/{session}/func/sub-{subject}_{session}_'
    'task-nback_acq-HiConHiLoWMgated_run-01_bold')
# conditions (run 2)
nback_HiConHiLoWMgated_run2 = create_key(
    'sub-{subject}/{session}/func/sub-{subject}_{session}_'
    'task-nback_acq-HiConHiLoWMgated_run-02_bold')

# field map
fmap_run1_ph = create_key(
    'sub-{subject}/{session}/fmap/sub-{subject}_{session}_run-01_phasediff')
fmap_run1_mag = create_key(
    'sub-{subject}/{session}/fmap/sub-{subject}_{session}_run-01_magnitude{item}')

# task session
# field maps (C412 ONLY)
fmap_pa_run1 = create_key(
    'sub-{subject}/{session}/fmap/sub-{subject}_{session}_dir-PA_run-01_epi')
fmap_ap_run1 = create_key(
    'sub-{subject}/{session}/fmap/sub-{subject}_{session}_dir-AP_run-01_epi')

# **********************************************************************************
def infotodict(seqinfo):
    """Heuristic evaluator for determining which runs belong where
    allowed template fields - follow python string module:
    item: index within category
    subject: participant id
    seqitem: run number during scanning
    subindex: sub index within group
    """

    last_run = len(seqinfo)

    info = {
        # baseline
        t1w: [], t2w: [], mean_perf: [], qsm_ph: [], qsm_mag: [],

        # TMS scans
        nback_HiConHiLoWMgated_run1: [],
        nback_HiConHiLoWMgated_run2: [],

        # field map
        fmap_run1_ph: [], fmap_run1_mag: [],

        # task
        fmap_pa_run1: [], fmap_ap_run1: [],
    }

    for s in seqinfo:
        protocol = s.protocol_name.lower()

        # Baseline Anatomicals
        if "anat_t1w" in protocol:
            info[t1w].append(s.series_id)
        elif "anat_t2w" in protocol:
            info[t2w].append(s.series_id)

        # TMS day
        elif "task-nback_acq-HiConHiLoWMgated_run-01" in s.protocol_name:
            info[nback_HiConHiLoWMgated_run1].append(s.series_id)
        elif "task-nback_acq-LoConHiLoWMgated_run-01" in s.protocol_name:
            info[nback_LoConHiLoWMgated_run1].append(s.series_id)

        # fmaps for subject C412
        elif "fmap_run-01" in protocol and "M" in s.image_type:
            info[fmap_run1_mag].append(s.series_id)
        elif "fmap_run-01" in protocol and "P" in s.image_type:
            info[fmap_run1_ph].append(s.series_id)

        elif "task-rest_acq-gated_bold" in s.protocol_name:
            info[rest_gated].append(s.series_id)

        elif "fmap_run-02" in protocol and "M" in s.image_type:
            info[fmap_run2_mag].append(s.series_id)
        elif "fmap_run-02" in protocol and "P" in s.image_type:
            info[fmap_run2_ph].append(s.series_id)

        elif "task-nback_acq-HiConHiLoWMgated_run-02" in s.protocol_name:
            info[nback_HiConHiLoWMgated_run2].append(s.series_id)
        elif "task-nback_acq-LoConHiLoWMgated_run-02" in s.protocol_name:
            info[nback_LoConHiLoWMgated_run2].append(s.series_id)

        # Task day
        # **** for subject C412 only ****
        elif "acq-fMRIdistmap_dir-PA_run-01" in s.protocol_name:
            info[fmap_pa_run1].append(s.series_id)
        elif "acq-fMRIdistmap_dir-AP_run-01" in s.protocol_name:
            info[fmap_ap_run1].append(s.series_id)

    return info


# Any extra metadata that might not be automatically added by dcm2niix.
MetadataExtras = {
    fmap_run1_ph: {
        "EchoTime1": 0.004,
        "EchoTime2": 0.006
    }
}


IntendedFor = {
    # baseline
    dwi_rpe: [
        'sub-{subject}/{session}/dwi/sub-{subject}_{session}_acq-HASC55_run-01_dwi.nii.gz',
        'sub-{subject}/{session}/dwi/sub-{subject}_{session}_acq-HASC55_run-02_dwi.nii.gz',
        'sub-{subject}/{session}/dwi/sub-{subject}_{session}_acq-HASC92_dwi.nii.gz',
        'sub-{subject}/{session}/dwi/sub-{subject}_{session}_acq-RAND57_dwi.nii.gz'],

    # task visit
    fmap_pa_run1: [ 'sub-{subject}/{session}/func/sub-{subject}_{session}_task-flanker_bold.nii.gz' ],
    fmap_ap_run1: [ 'sub-{subject}/{session}/func/sub-{subject}_{session}_task-flanker_bold.nii.gz' ]

}
```

There's a lot going on here, so let's break it down.

## Heuristic Breakdown: create_key()

This is the heavy lifting on the user's side. This function defines the template for a naming convention that the user wants to eventually to be filled in. For example, to create a BIDS compliant T1 scan name:

```
t1w = create_key('sub-{subject}/{session}/anat/sub-{subject}_{session}_T1w')
```

In this line, the characters within `{}` are the key-value pairs that will be filled in with case-specific values as `fw-heudiconv` loops over your data. You can use `create_key()` to create any number of keys for any number of different scan types, as long as you can fill in the fields for every key-value pair that will meet the corresponding criteria.

```
nback_HiConHiLoWMgated_run1 = create_key(
    'sub-{subject}/{session}/func/sub-{subject}_{session}_'
    'task-nback_acq-HiConHiLoWMgated_run-01_bold')
```

## Heuristic Breakdown: infotodict()

This function does the heavy lifting on the computational side. This function extracts sequence information from the image header, so that the user can specify to which template the image goes to. The object `info` is a dictionary, where`fw-heudiconv` stores lists of all of the scans that belong to a certain template. For example, the T1 we specified earlier:

```
if "anat_t1w" in protocol:
    info[t1w].append(s.series_id)
```

The T1 scan (which is an object called `s`) has a property named `protocol`. In our heuristic, this protocol field, for all T1 scans, *should* contain the text "anat_t1w". If this is the case, `s` will be added to the appropriate list in the dictionary.

If this is confusing, don't be discouraged — much of this process is abstracted very well by `heudiconv`; all that is required of the user is to know what properties in their scans' headers best discriminate different scans, and to express that in a series of `if-else` statements (see the full heuristic example). For example, if you know your protocol "*n*-back" has the string "back", in it, you will always be able to do:

```
if "back" in protocol:
```

And then further refine the match using other fields:

```
if "back" in protocol:
    if "M" in image_type:
        #assign to the correct template
```

## How Do I Know What Discriminates My Scans?

Understanding the different fields in your scans, and the values that they can take, will help you write a good heuristic. We cover this further in the step by step walkthrough.

## Metadata, Fieldmap Intentions, & Extended BIDS

One advantage of `fw-heudiconv` is that we can use it to specify BIDS metadata that normally wouldn't be added through `heudiconv`. This can be any field you want to specify, for example setting `EchoTime1` and `EchoTime2` for all the fieldmaps with phase diffs:
```
MetadataExtras = {
    fmap_run1_ph: {
        "EchoTime1": 0.004,
        "EchoTime2": 0.006
    }
}
```

As you can see, it's simply a dictionary with values you want to be added to certain templates (in this case, the template for `fmap_run1_ph` should always have `EchoTime1` and `EchoTime2`, which Flywheel wouldn't have inferred itself).

Additionally, with this method we can specify the list of files for which fieldmaps are intended:
```
fmap_ap_run1: [ 'sub-{subject}/{session}/func/sub-{subject}_{session}_task-flanker_bold.nii.gz' ]
```

If you have other templates (e.g. asl) that are not currently covered by the official BIDS specifications, you can add special metadata to these templates in this section too.


Lastly, use `ReplaceSubject()` or `ReplaceSession()` to define a function that will take each subject's/session's label and replace it as you see fit. E.g. to remove leading zeros:
```
def ReplaceSubject(subj_label):

    return str(int(subj_label))
```

This only manipulates the BIDS metadata, so your Flywheel objects remain unaffected.

---

# Step by Step BIDS Curation with `fw-heudiconv`

## Step 0: Installation

Follow instructions below to set up your system for using fw-heudiconv on your machine

Estimated time: 15 minutes

1. Install & start up Miniconda

First, get a package management system. Recommended is miniconda (conda): **Conda quickly installs, runs and updates packages and their dependencies.**

https://docs.conda.io/en/latest/miniconda.html.

You can check if you have this successfully by going to the terminal and doing:
```
$ which conda [macOS]
```
2. Start a virtual environment

Use miniconda to create a virtual environment, a restricted workspace where your programs and processes can operate without affecting everything on your computer. Create an environment called flywheel, in the terminal:
```
$ conda create -n flywheel anaconda python=3
```
At the prompt for which packages to install, type y and hit enter. It’s better to have them all, and they will not take up a lot of space on your machine:
```
:
:
:
$ wurlitzer          pkgs/main/osx-64::wurlitzer-1.0.2-py37_0
$ xlrd               pkgs/main/osx-64::xlrd-1.2.0-py37_0
$ xlsxwriter         pkgs/main/noarch::xlsxwriter-1.1.8-py_0
$ xlwings            pkgs/main/osx-64::xlwings-0.15.8-py37_0
$ xlwt               pkgs/main/osx-64::xlwt-1.3.0-py37_0
$ xz                 pkgs/main/osx-64::xz-5.2.4-h1de35cc_4
$ yaml               pkgs/main/osx-64::yaml-0.1.7-hc338f04_2
$ zeromq             pkgs/main/osx-64::zeromq-4.3.1-h0a44026_3
$ zict               pkgs/main/noarch::zict-1.0.0-py_0
$ zipp               pkgs/main/noarch::zipp-0.5.1-py_0
$ zlib               pkgs/main/osx-64::zlib-1.2.11-h1de35cc_3
$ zstd               pkgs/main/osx-64::zstd-1.3.7-h5bba6e5_0


$ Proceed ([y]/n)?
```
Activate your environment, so that any packages you install or use stay restricted to this project:
```
$ source activate flywheel
```
3. Download `fw-heudiconv` from pip

The fw-heudiconv code is hosted on pip: **pip is a standard package-management system used to install and manage software packages written in Python**

Pip should be installed with your new environment, but you can ensure you have it by doing:
```
$ which pip
```
Now, use pip to install fw-heudiconv
```
$ pip install fw-heudiconv
```
4. Download the Flywheel CLI & login

The flywheel CLI allows fw-heudiconv (or any other program you write) to communicate with Flywheel’s database. Follow their instructions here:

https://docs.flywheel.io/hc/en-us/articles/360008162214

Once installed and logged in, you should see your username when you do:
```
$ fw status
$ You are currently logged in as Tinashe Tapera to https://upenn.flywheel.io
```

5. Updating `fw-heudiconv`

If you already have `fw-heudiconv` and wish to update to the latest version, just do:

```
$ pip install --upgrade fw-heudiconv
```

6. Appendix — `fw-heudiconv-validate`

`fw-heudiconv-validate` is a convenience tool that wraps the official Bids Validator and pipes the output of `fw-heudiconv-export` to it. It's most useful for validating Flywheel data through a gear on the GUI.

To use `fw-heuduiconv-validate` on your local machine, you need to install [node.js](https://nodejs.org/en/). This is not necessary, however, and instead you are welcome to use `fw-heudiconv-validate` on your Flywheel site, or, use `fw-heudiconv-export` to export data first, and then use the official Bids Validator available [here](http://bids-standard.github.io/bids-validator/)

## Step 1: Understanding Your Dataset in the Context of BIDS

Before you can curate the dataset into BIDS, it's important to be able to predict how your dataset should look in BIDS. Use the tabulation tool to extract and summarise sequence info from your dataset on Flywheel. This step assumes you have Nifti files in your project, each derived from a DICOM imaging file, because the sequence information for each scan is stored in the DICOM header.

**At the command line**
```
fw-heudiconv-tabulate --project 'MyProject' --path .
```
Use the flags `--subject <subject list>` and/or `--session <sessions list>` to filter, and `--dry_run` to only print to console and not download. Otherwise, `fw-heudiconv` will download the output to the location specified in `--path`.

**In the GUI**
1. Select a session in your project.
2. Click "Run Gear" from the Acquisition Viewer.
3. Select "Analysis Gear" and select "Flywheel HeuDiConv".
4. An input file is necessary; use a dummy file for now, it's a benign feature.
5. In the "Configuration" tab, type "Tabulate" into the "Action" field, to tabulate your dataset.
6. In the "Configuration" tab, check/uncheck "Do whole project", based on your preference. If not checked, `fw-heudiconv` will operate only on the session the gear initiated from.
7. In the "Configuration" tab, check/uncheck "Dry run". Using dry run at this point is also benign, since no files are being manipulated. Dry run just prints all the info to the console, which is stored in the gear log. Otherwise, it will save the output file to the gear output.
8. Hit "Run Gear"

**The Output**

The output is a tab-separated file that summarises sequence information for the query. Only unique combinations of a handful of columns are shown, to reduce redundancy in case your project has a large number of scans. Here's an example (some columns have been removed for potential PHI):

| total_files_till_now | example_dcm_file                                             | series_id                | dcm_dir_name                        | dim1 | dim2 | dim3 | dim4 | TR   | TE      | protocol_name           | is_motion_corrected | is_derived | series_description      | sequence_name | image_type                                         | series_uid                                                  |
|----------------------|--------------------------------------------------------------|--------------------------|-------------------------------------|------|------|------|------|------|---------|-------------------------|---------------------|------------|-------------------------|---------------|----------------------------------------------------|-------------------------------------------------------------|
| 132                  | 1.3.12.2.1107.5.2.43.167024.2018032214133547261103733.MR.dcm | 5c1a86f09011bd00153695c4 | B0map_onesizefitsall_v4_4_e1.nii.gz | 64   | 64   | 88   | -1   | 1.01 | 0.00471 | B0map_onesizefitsall_v4 | False               | False      | B0map_onesizefitsall_v4 | _fm2d2        | ('ORIGINAL', 'PRIMARY', 'M', 'ND')                 | 1.3.12.2.1107.5.2.43.167024.2018032214115437323103464.0.0.0 |
| 44                   | 1.3.12.2.1107.5.2.43.167024.2018032214133585093003820.MR.dcm | 5c1a86f09011bd00113694c9 | B0map_onesizefitsall_v4_5_ph.nii.gz | 64   | 64   | 44   | -1   | 1.01 | 0.00717 | B0map_onesizefitsall_v4 | False               | False      | B0map_onesizefitsall_v4 | _fm2d2        | ('ORIGINAL', 'PRIMARY', 'P', 'ND')                 | 1.3.12.2.1107.5.2.43.167024.2018032214115437324503465.0.0.0 |
| 411                  | 1.3.12.2.1107.5.2.32.35069.2015101914203476145443522.MR.dcm  | 5c1a7fed9011bd0014368e4e | b0map_v4_6_e1.nii.gz                | 64   | 64   | 88   | -1   | 1.05 | 0.00406 | b0map_v4                | False               | False      | b0map_v4                | _fm2d2r       | ('ORIGINAL', 'PRIMARY', 'M', 'ND')                 | 1.3.12.2.1107.5.2.32.35069.2015101914184868390743166.0.0.0  |
| 411                  | 1.3.12.2.1107.5.2.32.35069.2015101914203476145443522.MR.dcm  | 5c1a7fed9011bd0014368e4e | b0map_v4_6_e2.nii.gz                | 64   | 64   | 88   | -1   | 1.05 | 0.00652 | b0map_v4                | False               | False      | b0map_v4                | _fm2d2r       | ('ORIGINAL', 'PRIMARY', 'M', 'ND')                 | 1.3.12.2.1107.5.2.32.35069.2015101914184868390743166.0.0.0  |
| 1677                 | 1.3.12.2.1107.5.2.32.35069.201510191422558417862742.MR.dcm   | 5c1a7fec9011bd0015368c08 | bbl1_restbold_mb6_742_8.nii.gz      | 448  | 448  | 742  | -1   | 0.5  | 0.03    | bbl1_restbold_mb6_742   | False               | False      | bbl1_restbold_mb6_742   | epfid2d1_64   | ('ORIGINAL', 'PRIMARY', 'M', 'MB', 'ND', 'MOSAIC') | 1.3.12.2.1107.5.2.32.35069.201510191420455048143619.0.0.0   |
| 935                  | 1.3.12.2.1107.5.2.32.35069.2015101914323462523363679.MR.dcm  | 5c1a7fed9011bd0014368e4c | bbl1_restbold1_124_9.nii.gz         | 448  | 448  | 124  | -1   | 3    | 0.032   | bbl1_restbold1_124      | False               | False      | bbl1_restbold1_124      | _epfid2d1_64  | ('ORIGINAL', 'PRIMARY', 'M', 'ND', 'MOSAIC')       | 1.3.12.2.1107.5.2.32.35069.2015101914293922341852056.0.0.0  |
| 537                  | 1.3.12.2.1107.5.2.43.167024.2018032214185313767709937.MR.dcm | 5c1a86f09011bd0013369952 | ep2d_effort1_236_6.nii.gz           | 448  | 448  | 236  | -1   | 3    | 0.03    | ep2d_effort1_236        | False               | False      | ep2d_effort1_236        | _epfid2d1_64  | ('ORIGINAL', 'PRIMARY', 'M', 'ND', 'MOSAIC')       | 1.3.12.2.1107.5.2.43.167024.2018032214135115699603908.0.0.0 |
| 771                  | 1.3.12.2.1107.5.2.43.167024.2018032214343969862950630.MR.dcm | 5c1a86f09011bd001436a0e5 | ep2d_effort2_236_7.nii.gz           | 448  | 448  | 234  | -1   | 3    | 0.03    | ep2d_effort2_236        | False               | False      | ep2d_effort2_236        | _epfid2d1_64  | ('ORIGINAL', 'PRIMARY', 'M', 'ND', 'MOSAIC')       | 1.3.12.2.1107.5.2.43.167024.2018032214283117672136244.0.0.0 |

Using this table, we can attempt to construct a heuristic.

## Step 2: Writing a Heuristic File

The next step is to craft your heuristic file. This is a Python script that you can write in any code or text editor. If you're new to writing code in Python, I recommend [this guide](https://www.w3schools.com/python/default.asp), however, it's not necessary to know *how to code* to complete this step; only *how the code is meant to work*. Here's what you must do:

1. Define the templates for your filenames
You do this by first defining the function that creates templates:
```
def create_key(template, outtype=('nii.gz',), annotation_classes=None):
    if template is None or not template:
        raise ValueError('Template must be a valid format string')
    return template, outtype, annotation_classes
```

This function will return a template that will be used to check and manipulate your filenames into BIDS. Once you've defined this function, you can create templates for your imaging files' names. For example, a template for T1 in BIDS looks like this:

```
t1w = create_key(
    'sub-{subject}/{session}/anat/sub-{subject}_{session}_T1w')
```

Based on your knowledge of your imaging sessions, and the data you get from `fw-heudiconv-tabulate`, you can craft keys that will accommodate the curation of BIDS for every scan session in your project.

Lastly, define the function that gathers and applied the conversion to each of your scans' names. This function, `infotodict`, is where you'll need to use your sequence info table to define the logic that best separates your scan types. In the function, `info` is a python dictionary of templates for each of your scan types, where the values for each key is going to be a list of the files that match that template. So, to assign a file to a template, use `if-else` statements (in programming, called "boolean" logic) to define the decision rules of what-goes-where.

```
def infotodict(seqinfo):
    """Heuristic evaluator for determining which runs belong where
    allowed template fields - follow python string module:
    item: index within category
    subject: participant id
    seqitem: run number during scanning
    subindex: sub index within group
    """

    last_run = len(seqinfo)

    info = {
        # baseline
        t1w: [], t2w: [],

        # field map
        fmap_run1_ph: [], fmap_run1_mag: [],

        # task
        fmap_pa_run1: [], fmap_ap_run1: [],
    }

    for s in seqinfo:
        protocol = s.protocol_name.lower()

        # Baseline Anatomicals
        if "anat_t1w" in protocol:
            info[t1w].append(s.series_id)
        elif "anat_t2w" in protocol:
            info[t2w].append(s.series_id)

        # fmaps
        elif "fmap_run-01" in protocol and "M" in s.image_type:
            info[fmap_run1_mag].append(s.series_id)
        elif "fmap_run-01" in protocol and "P" in s.image_type:
            info[fmap_run1_ph].append(s.series_id)

        elif "fmap_run-02" in protocol and "M" in s.image_type:
            info[fmap_run2_mag].append(s.series_id)
        elif "fmap_run-02" in protocol and "P" in s.image_type:
            info[fmap_run2_ph].append(s.series_id)

        # Task day
        elif "acq-fMRIdistmap_dir-PA_run-01" in s.protocol_name:
            info[fmap_pa_run1].append(s.series_id)
        elif "acq-fMRIdistmap_dir-AP_run-01" in s.protocol_name:
            info[fmap_ap_run1].append(s.series_id)

    return info

```
So for example, if we know we have a couple of different types of task scans, we can use the data in the protocol name to separate one run from another.

Optionally, you can add extra metadata at the end of your heuristic. These are key-value pairs that will be manually inserted into the BIDS sidecar. To add `EchoTime1` and `EchoTime2` to every fieldmap template:

```
MetadataExtras = {
    fmap_run1_ph: {
        "EchoTime1": 0.004,
        "EchoTime2": 0.006
    }
}
```
*For diffusion data, for the BIDS validator to work, you will need to add 2 other extra metatda fields: (1) "EffectiveEchoSpacing" and (2) "TotalReadoutTime". You should be able to get these values from your initial protocol. 

A last option is adding `IntendedFor` lists that specify which scan templates are dependent on specific fieldmap templates. In this case, the fieldmaps below are *intended for* fieldmap correction in any file that matches the template in the list:

```
IntendedFor = {
    # task visit
    fmap_pa_run1: [ 'sub-{subject}/{session}/func/sub-{subject}_{session}_task-flanker_bold.nii.gz' ],
    fmap_ap_run1: [ 'sub-{subject}/{session}/func/sub-{subject}_{session}_task-flanker_bold.nii.gz' ]

}
```

Finish writing your heuristic and save the file as a Python script (a file with the extension `.py`).

## Step 3: Running `fw-heudiconv-curate`

You will run the curation tool to apply your changes.

**At the command line**
```
fw-heudiconv-curate --project 'MyProject' --heuristic MyHeuristicFile.py
```
Use the flags `--subject <subject list>` and/or `--session <sessions list>` to filter, and `--dry_run` to test your heuristic and only print the changes to the console.

**In the GUI**

0. Upload your heuristic file to your project.
1. Select a session in your project.
2. Click "Run Gear" from the Acquisition Viewer.
3. Select "Analysis Gear" and select "Flywheel HeuDiConv".
4. An input file is necessary; this is your heuristic script.
5. In the "Configuration" tab, type "Curate" into the "Action" field, to curate your dataset into BIDS.
6. In the "Configuration" tab, check/uncheck "Do whole project", based on your preference. If not checked, `fw-heudiconv` will operate only on the session the gear initiated from.
7. In the "Configuration" tab, check/uncheck "Dry run". Dry run just prints all of the proposed changes to the console, which is stored in the gear log. Otherwise, it will apply the changes to the project.
8. Hit "Run Gear".

## Step 4: Running `fw-heudiconv-validate`

Lastly, you will run the validation tool for your BIDS dataset.

**At the command line**
```
fw-heudiconv-validate --project 'MyProject' --flywheel
```
