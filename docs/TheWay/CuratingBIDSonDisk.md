---
layout: default
title: Curating BIDS Datasets
parent: The Way
nav_order: 2
has_toc: true
---

# Curating BIDS Datasets
{: .no_toc }

BIDS curation can be a frustrating process. This guide describes best practices for
the curation process using a local filesystem. We divide this process into multiple
stages.



* TOC
{:toc}

## Preparing your environment

If curating data on a Penn cluster, you will need to set up a conda
environment for your project. For information on how to set up conda
on each clustess [instructions for CUBIC]() and
[instructions for PMACS](). You will need to install datalad and CuBIDS
for the rest of the steps in this workflow. To do so, create a conda
environment:

```shell
$ conda create cubids
$ conda activate cubids
```

Git annex is required for Datalad, and is not installed by default.

```shell
$ conda install -c conda-forge git-annex datalad
```

Finally, [download and install CuBIDS](linktocubids.html). Note that
this environment must be activated for the rest of the steps.


## Stage 0: Organization

The process begins when data arrives on one of our servers. While it may not
be possible to document everything that has happened to the data prior to its
arrival to our server, the goal of the organization step is to document as
much as we can about the process of receiving the data and any initial
changes we might make to it. The person in charge of organizing a data set
should document any changes they make in a *Data Narrative* along with any
relevant scripts used to a git-tracked repository.

### Documenting data provenance

The *Data Narrative* should begin with a section describing where the data
came from.

```markdown
# Transfer Process

The data were acquired at [SITE] as part of [STUDY] study. Access was given
to PennLINC as part of the [COLLABORATION/GRANT] project. The data was
transferred from [SITE] to [PennLINC CLUSTER] using [TOOLS & SOFTWARE +
VERSIONS]. The approximate date of transfer was [DATE RANGE]. In sum,
PennLINC received [APPROX VOLUME OF DATA] comprising of [N] subjects with a
range of [N] sessions. The imaging data consisted of [MODALITIES].
Additionally, [ANY ADDITIONAL CLINICAL OR COHORT DATA]. The imaging data was
organised [IN BIDS/NOT IN BIDS] and [ANONYMIZED/NOT ANONYMIZED] at the time
of transfer, and the raw data was stored in [DIRECTORY ON THE CLUSTER] and
tracked on [GITHUB].
```

Where the fields in brackets are replaced by your specific information.

### Documenting initial steps taken

Files with clearly non-BIDS-compliant names, unnecessary data and any sensitive
information should be removed as soon as possible. Each of these steps should be
documented in the *Data Narrative* like so:

```markdown
# Initial Steps

The initial data contained localizer scans that will not be used. These files
were removed using [scripts/delete_localizers.sh](link_to_script_on_github).
```

Any other scripts applied at this stage should be noted here, including both
their path in the git repository and a description of the action they
implement.

### Removing sensitive metadata

> ⚠️ NOTE: This step must occur **BEFORE** data is checked in to datalad.

Sometimes the DICOM-to-NIfTI conversion process results in unwanted information
in the JSON sidecars. You can use `cubids-remove-metadata-fields` to purge these
from all .json files in your data. All unique metadata fields can be listed
using `cubids-print-metadata-fields`.Be sure to note the fields you removed in
your *Data Narrative*.

```markdown
Sensitive fields, included in: AccessionNumber, PatientBirthDate, PatientID,
PatientName, PatientSex, AcquisitionDateTime, SeriesInstanceUID,
DeviceSerialNumber, InstitutionAddress, AcquisitionTime, StationName,
ReferringPhysicianName, InstitutionName, InstitutionalDepartmentName,
AccessionNumber were removed from all metadata files using
`cubids-remove-metadata-fields`.
```

You can check your data into datalad any time after you've removed all
sensitive metadata fields.


## Stage 1: BIDS Validation

At the end of Step 0 you should have a BIDS-like data set containing NIfTI
and JSON files. The goal of this stage is to get your data passing the
BIDS Validator without any errors. This is an iterative process - fixing
one error may introduce new errors. Expect this step to take a number of
iterations and be sure to describe each step in your *Data Narrative*.

Suppose you ran `cubids-validate` on your BIDS data. This will create
a file containing all the errors present in your data. Add this file
to your git repository and describe it in the *Data Narrative*:

```markdown
# BIDS Validation

Upon first run of the validator, we found [N] errors:

[ERROR CODE1 + SHORTHAND DESCRIPTION1]: [NUMBER OF SUBJECTS WITH ERROR1]
[ERROR CODE2 + SHORTHAND DESCRIPTION2]: [NUMBER OF SUBJECTS WITH ERROR2]
[ERROR CODEn + SHORTHAND DESCRIPTIONn]: [NUMBER OF SUBJECTS WITH ERRORn]
[GITHUB URL TO VALIDATOR OUTPUT AT DATE OF COMMIT]
```

For example, this might look like:

```markdown
Code 39 (Inconsistent Parameters): 15 subjects
Code 86 (Suspiciously Short Event Design): 136 subjects
Commit: https://github.com/PennLINC/RBC/blob/3dd4e931cc96f7f74512c57b86ede30735ef9252/PennLINC/Validation/Merging.ipynb

We attempted to iteratively remove each of these errors in [GITHUB URL TO NOTEBOOK/SCRIPT]. Specifically, [SCRIPT URL] was used to solve code [ERROR CODEn].

```

After re-running the validator you may find new errors. In this case, write more scripts
that fix the errors and describe them in the *Data Narrative*

```markdown
After re-running the validator on [APPROX DATE], we found a further [N] errors:

[ERROR CODEn + SHORTHAND DESCRIPTIONn]: [NUMBER OF SUBJECTS WITH ERRORn]
[GITHUB URL TO VALIDATOR OUTPUT AT DATE OF COMMIT]

We solved this error with  [GITHUB URL TO NOTEBOOK/SCRIPT].

```

Eventually you will have fixed all the problems and achieved BIDS compliance.
Note this victory in your *Data Narrative*:

```markdown
We were satisfied with curation in/on [APPROX LAST DATE OF CURATION]. The
last BIDS validator output is available [GITHUB URL TO VALIDATOR OUTPUT AT
DATE OF COMMIT], after which data was checked into datalad and backed up to
[CLUSTER/DIRECTORY]. The datalad commit was [SHASUM of datalad commit].

```

## Stage 2: BIDS Optimization

### Add NIfTI information to the sidecars

Image files (NIfTI) are large binary files that contain information about
the spatial coverage of MRI images. We want to be able to detect variability
in this, but don't necessarily want to always be reading it from NIfTI files.
For example, the nifti files may be checked in to git annex or stored on
a remote server. These are somewhat common use cases, so we recommend
adding the information you would normally get from NIfTI files directly
to the JSON files. CuBIDS comes with a utility for this, which can be run
with

```bash
$ cubids-add-nifti-info dataset_path
```

### Find unique acquisition groups




## General princples & motivation
1. We wanted to create a generalizable workflow for curating BIDS datasets on CUBIC.
2. This workflow includes running a BIDS dataset through CuBIDS (validation and grouping), and using Datalad to run preprocesssing pipelines.

## Curration with CuBIDS
* Create a conda environment for CuBIDS/Datalad (you should have miniconda installed and git set up on your project user)

* Install CuBIDS in your conda environment
    * **git clone [git@github.com:PennLINC/BOnD.git](git@github.com:PennLINC/BOnD.git) ./cubids**
    * **cd cubids**
    * **pip install -e .**

* Now that you have CuBIDS installed, you're ready to go through the CuBIDS workflow.

    * **cubids-group dataset_path output_path**
        * This command outputs your four CuBIDS files delineating key/param groups and acquisition groups
        * Relevant parties will then edit the summary csv requesting new names for parameter groups as well as merging or deleting them where appropriate.
    * **cubids-apply dataset_path old_summary_csv old_files_csv new_output_path**
        * This command applies changes (renames, merges, and deletions of parameter groups) requrested in the summary csv CuBIDs output and produces the four new CuBIDS files at the output path.
* Once there are no more validation errors from cubids-validate, you can move on to cubids-group/cubids-apply. Once all parties are satisfied with the parameter groups in the summary csv and have no more changes to request, congratulations, you have finished the CuBIDS workflow!



## Preprocessing Pipelines with Datalad
* NOW you're ready to run preprocessing pipelines on one subject from each acquisition group (see cubids-group acq_groups.csv for list)
* Activate a conda environment that contains CuBIDS
    * **conda activate cubids**
* Create the empty superdataset
    * **datalad create -c text2git /cbica/projects/RBC/testing/superds**
        * **-c text2git** Ensures scripts are checked into git not git annex
        * We suggest calling your superds the name of your BIDs dataset (e.g. PNC)
* cd into the superdataset and run the following commands:
    * **datalad create -c text2git -d . bidsdatasets**
    * **mkdir code**
* copy the entirety of your BIDS dataset into the **superds/bidsdatasets** subdataset
    * The root of your BIDS dataset should now be **superds/bidsdatasets**
* run the following from the root of the superdataset
    * **datalad status**
        * you should see all the data you copied into **superds/bidsdatasets** as untracked
    * **datalad save -m "created superds, added BIDS data**
        * you should always include a DETAILD commit message when saving!
* We have created three scripts that cover setting up the superds for pipeline runs, submittnig jobs to cubic, and running fmriprep. **cp** these to your **superds/code** directory. They are located at the following paths:
    * **/cbica/projects/RBC/testing/CCNP/code/setup_datalad_pre_pipelines.sh**
    * **/cbica/projects/RBC/testing/CCNP/code/datalad-fmriprep-run.sh**
    * **/cbica/projects/RBC/testing/CCNP/code/submit-fmriprep-jobs.sh**
* Make sure to change the dataset and csv paths in these scripts to reflect your superds!
* Run the following from the superds:
    * **bash code/setup_datalad_pre_pipelines.sh**
        * Running the setup script will create subdatasets fmriprep/, freesurfer/, and code/pipelines/ and will add license.txt to code/
    * **datalad status**
        * Should show you the new directories that have been added.
    * **datalad containers-list**
        * Should show the new fmriprep image you added
    * **datalad save -m "populated code subds and ran setup scripts"**
* To see fmriprep image added
    * **datalad save** from the root dir!
    * **datalad status**
* To make sure you have a clean environment, logout from rbc and log back in and cd into **SBIA_TMPDIR** and ls to see if there is space.
* Now you're ready to submit your fmriprep jobs! To do this, run the following from the superds root:
    * **bash submit_fmriprep_jobs.sh**
        * The submit script will call the run script for each subject in the acquisition group csv.
* This might take a few days to run, and make sure you run **qstat** periodically to check on the status of the jobs.
* Once your jobs have all finished, each subject's output will be in it's own git branch. To view an output, run the following commands:
    * **cd superds/fmriprep/**
        * This directory should look empty but isn't (each subject's output directory is just in it's own branch)
    * **git branch -v**
        * To see all branches
    * **git checkout sub-'subjectID'**
        * To switch to one subject's branch
    * **git checkout master**
        * To return to the main branch
* Now we want to merge all the subjects into the master branch. This is called an "Octopus Merge." To implement it, you are going to need to add two scripts to the **superds/code** directory:  A submit script that loops through your list of acquisition group subjects and calls the run script for each one. The run script should include the following Octopus Merge command:
    * **for b in $(git branch -l | grep 'sub-' | tr -d ' ');
     do ( git checkout -b m$b $b && git rm logs/CITATION.* && git commit --amend --no-edit ) ;
done**
* Once you are done creating your submit and run scripts, bash the submit script to submit each subject directory's merge as a job.
* Once your jobs finish, all subjects' fmriprep output directories should be on the main branch.
* Go through the same set of steps with the **superds/fressurfer/** fmriprep output directory.
* Run the audit script on your outputs to see if there are any issues in the data that need to be fixed.
* If you need to make changes to your BIDS dataset, make sure to re-run **cubids-group** and **cubids-apply** if necessary.
* Finally, repeat the entire fmriprep process, starting with setting up a superdataset but this time with the entire BIDS dataset.

