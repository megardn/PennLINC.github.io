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

Create a project root directory. Initially we suggest keeping one copy of your
initial data that will remain untouched as a backup during early stages of
the workflow. This is not necessary if your data is archived elsewhere online.
In the project root consider something like

```
project
├── RAW
│   ├── sub-1
│   ├── sub-2
│   ├── ...
│   └── sub-N
└── working
    ├── curation_code
    │   ├── DataNarrative.md
    │   ├── Fix1.sh
    │   └── Fix2.sh
    └── BIDS
        ├── dataset_description.json
        ├── README.txt
        ├── sub-1
        ├── sub-2
        ├── ...
        └── sub-N
```

The `RAW` directory should be an unchanged copy of the original data. If this
is impossible, you can [remove sensitive
metadata](#removing-sensitive-metadata) before initializing your `working/`
directory as a datalad dataset. Datalad will then provide the ability to
undo mistakes made during this stage.


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
were removed using [curation_code/delete_localizers.sh](link_to_script_on_github).
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
Commit:
https://github.com/PennLINC/RBC/blob/3dd4e931cc96f7f74512c57b86ede30735ef9252/PennLINC/Validation/Merging.ipynb

We attempted to iteratively remove each of these errors in [GITHUB URL TO
NOTEBOOK/SCRIPT]. Specifically, [SCRIPT URL] was used to solve code [ERROR
CODEn].

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

> BIDS is a file naming and metadata specification - what's there to optimize?

We found that it's difficult to come up with BIDS file names that accurately
represent both what a file contains and how its acquisition is different
from similar images.

Consider the case where someone created a BIDS data set using heudiconv.
It's common for a heudiconv heuristic to rely on a simple field like
`ProtocolName`, which is defined by the scanner technician. The technician
can use the same protocol name (eg "task1BOLD") because a scan was a
BOLD acquisition during which the subject performed "task1". Suppose
a scanner upgrade occurred or a fancy new sequence became available
that changed the `MultibandAccelerationFactor` from 1 to 4. The
`ProtocolName` may still be "task1BOLD" since the image will still
contain BOLD collected as the participant performed task1, but the
signal will be very different. Without changes to the heuristic,
both acquisitions would result in `sub-X_task-1_bold` as the
BIDS name.

CuBIDS provides a utility that finds variations in important imaging
parameters within each BIDS *name group*. In our example there might
be two unique *parameter groups* that map to the same *name group* of
`sub-X_task-1_bold`: one with `MultibandAccelerationFactor` 1 and
the other 4. A complete description of name and parameter groups
can be found on the [CuBIDS documentation](https://bids-bond.readthedocs.io/en/latest/usage.html#definitions).

### Add NIfTI information to the sidecars

Image files (NIfTI) are large binary files that contain information about
the spatial coverage of MRI images. We want to be able to detect variability
in this, but don't necessarily want to always be reading it from NIfTI files.
For example, the nifti files may be checked in to git annex or stored on
a remote server. These are somewhat common use cases, so we recommend
adding the information you would normally get from NIfTI files directly
to the JSON files. CuBIDS comes with a NIfTI metadata extractor, which
can be run with

```bash
$ cubids-add-nifti-info dataset_path
```

Once run, you will find volume information in the JSON sidecars.

### Find unique acquisition groups

CuBIDS reads and writes CSV files during the grouping process. You should create an
`iterations/` directory in `working/code` to store these CSVs and so they can be
tracked in git. Your project should look like

```
project
├── RAW
└── working
    ├── curation_code
    │   ├── DataNarrative.md
    │   ├── iterations
    │   ├── Fix1.sh
    │   └── Fix2.sh
    └── BIDS
```

To detect acquisition groups in your data set, change into `working/` and run

```shell
$ cubids-group BIDS code/iterations/iter0
```

This command will write four CuBIDS files delineating name/param groups and
acquisition groups.

### Rename, merge or delete parameter groups

Relevant parties will then edit the summary csv
requesting new names for parameter groups as well as merging or deleting them
where appropriate. A description of how this process works can be found on
the [CuBIDS documentation](https://bids-bond.readthedocs.io/en/latest/usage.html#modifying-key-and-parameter-group-assignments)
[//]: # (Sydney, could you add the actual csv names in a tree printout?)

Once the edited CSV is ready, the merges, renamings and deletions can be
applied to your BIDS data. Again from `working/`, if you're not using
datalad at this point, run

```shell
$ cubids-apply \
    BIDS \
    code/iterations/iter0_summary.csv \
    code/iterations/iter0_files.csv \
    code/iterations/iter1
```

If using datalad, be sure to include `--use-datalad` as the first argument to
`cubids-apply`. If not using Datalad, be sure to add to your *Data Narrative*
so you know that CuBIDS was used to change your data. Describe any major
renaming, deleting or merging of parameter groups.

New parameter groups will be described in `code/iterations/iter1*.csv` and you
should make sure your data was changed as intended.

Once you are satisfied with your name/parameter groups, be sure to re-run
`cubids-validate` to make sure you haven't introduced any BIDS-incompatible
names.


## Stage 3: Preprocessing Pipelines with Datalad

This stage includes two distinct parts. The first is a final check that your
BIDS curation is sufficient for correctly running BIDS Apps on your data. The
second stage is running those BIDS Apps for real. The exemplar subject test
and the full cohort run follow the exact same steps, just using different
data as inputs.

### Testing pipelines on example subjects

Each subject belongs to one Acquisition group. Since all subjects in an
acquisition group will be processed the same way in the \*preps, you only
need to test one subject per acquisition group. We will need to add some
directories to our project for the outputs of each test.

To add a testing directory and copy example subjects into its BIDS
subdataset, use the CuBIDS program (again from `working/`)

```shell
$ cubids-copy-exemplars BIDS testing
```
[//]: # (this CLI program needs to be written, but will be super useful)

You should then see the following changes in your project:

```
project/
├── RAW
└── working
    ├── BIDS
    ├── code
    │   ├── DataNarrative.md
    │   ├── Fix1.sh
    │   └── Fix2.sh
    └── testing             # (This is a superdataset)
        ├── bidsdatasets    # subdataset
        │   ├── sub-2
        │   ├── ...
        │   └── sub-N
        ├── code
        ├── fmriprep        # subdataset
        ├── freesurfer      # subdataset
        └── qsiprep         # subdataset
```

In the `working/testing/code` directory, download the example scripts for
each pipeline to be run on whichever cluster you're using from
[TheWay](https://github.com/PennLINC/TheWay/tree/main/scripts).
Then submit each of the test subjects to the queuing system using

```shell
$ bash submit_[pipeline]_jobs.sh
```

where `[pipeline]` is replaced by `fmriprep` or `qsiprep`.

### Checking outputs from your exemplar subjects

The `fmriprep` or `qsiprep` directory in your `working/testing` directory
will have branches for each of your test subjects. To see if pipelines worked
as you anticipated, run a completeness check from `working/`:

```shell
$ cubids-completeness-check testing code/iterations
```

If you found that some Acquisition groups need to have their BIDS data
changed to work properly in the pipelines, return to [Stage 2](#stage-2-bids-optimization)

### Running pipelines on ALL your subjects

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

