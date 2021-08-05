---
layout: default
title: Running Pipelines with DataLad
parent: The Way
nav_order: 3
has_toc: true
---

# Running Bids App pipelines
{: .no_toc }

In general, we will need to apply an image processing workflow to the raw
data we've
[curated in BIDS](/docs/TheWay/CuratingBIDSonDisk#curating-bids-datasets).
This workflow is essentially the same for any of the preps, including
fmriprep, qsiprep, CPAC and aslprep. In this section we show how to create an
*analysis dataset* that contains the prep containers, the provenance of the
prep runs, and the prep outputs.


* TOC
{:toc}

## Preparing your containers

---
Note:
On CUBIC, the RBC user can't build singularity images. You should build the
container as your personal user and copy it to /cbica/projects/RBC/dropbox.

---


Here, for example, we build a qsiprep image:
```bash
singularity build qsiprep-0.14.2.sif docker://pennbbl/qsiprep:0.14.2
```

This image needs to be added to a datalad "containers dataset". Be sure you've installed
the datalad containers plugin via `pip install datalad_containers`. Now create the
dataset:

```bash
$ datalad create -D "Note about the container" qsiprep-container
$ cd qsiprep-container
$ datalad containers-add --url /full/path/to/qsiprep-0.14.2.sif qsiprep-0-14-2
```

Note the last argument is the *image ID* in the datalad container dataset. This string
can only have characters and dashes in it. Remember what you assign as the *image ID* because
you will need to add it to your scripts later.

Lastly, you've copied the sif file into `qsiprep-container/` so you can delete the original
sif file:

```bash
$ rm /full/path/to/qsiprep-0.14.2.sif
```

## Preparing the analysis dataset

We assume your BIDS data is curated and available either on your local file system
or a remote datalad source.

You can [download](https://github.com/PennLINC/TheWay/tree/main/scripts/cubic) and run one or more of our "bootstrap" scripts that sets up a
working directory for the running of your pipeline. Set the `BIDSAPP` variable
to the lowercase version of the pipeline you'd like to run.

Different bootstrap scripts require different argmuents. For BIDSApps, the arguments are

  1. The datalad-interpretable path to your BIDS dataset. Examples may be ria+ssh://, ria+file://,
     https:// or a path to a local datalad-enabled directory containing BIDS data. If a local path, it should be the directory that contains the `sub-`directories and **must** be a datalad dataset.
  2. The path to the container dataset you created in the previous step.

---
**NOTE:** Both arguments must be *absolute paths* and *cannot contain a trailing /*.

---

Here we "bash the bootstrap" to create a `qsiprep` directory:

```bash
$ BIDSAPP=qsiprep
$ wget https://raw.githubusercontent.com/PennLINC/TheWay/main/scripts/cubic/bootstrap-${BIDSAPP}.sh
$ bash boostrap-${BIDSAPP}.sh /full/path/to/BIDS /full/path/to/qsiprep-container
```

This will create a `qsiprep` directory that contains numerous other
directories needed to run the app at scale.

---
### ⚠️ ⚠️ WARNING ⚠️ ⚠️
Once you have run the command to create your `${BIDSAPP}` directory,
DO NOT rename or change the path to any of the directories that were inputs to your
bootstrap script!!

---

Inside the `${BIDSAPP}` directory, the subdirectory most relevant for you is the
`analysis` directory. This is a regular datalad dataset that
contains the code, input data, and remote configuration needed to run the
jobs on the cluster.

```bash
$ cd ${BIDSAPP}
$ ls
analysis	input_ria	output_ria
$ cd analysis
$ datalad siblings
.: here(+) [git]
.: input(-) [/Users/mcieslak/projects/tmp/BIDSAPP/input_ria/9d1/e46ef-27a2-400c-84da-7ea466afd3e7 (git)]
.: output-storage(+) [ora]
.: output(-) [/Users/mcieslak/projects/tmp/BIDSAPP/output_ria/9d1/e46ef-27a2-400c-84da-7ea466afd3e7 (git)]
```

We can see that in addition to the `analysis` dataset there are two remotes
configured for pushing and pulling provenance and data from. These are
required for performance on large datasets, but it's not required for you to
know exactly how this all works as an end user.

## Editing the executable code

The bootstrapping script will also create some scripts in
`analysis/code` that will be used to run your job. You will want to
edit these to tailor the call to your prep in case there are any specific
options for this particular run. The files `analysis/code/participant_job.sh`
and `analysis/code/*zip.sh` are the key to running your job. You can edit these
however you like, but be sure you save your changes and push them to the
input and output ria stores. For example, if I just edited one of the scripts
and saved the file:

```bash
$ datalad save -m "edited participant_job.sh" code/participant_job.sh
$ datalad push --to input
$ datalad push --to output
```

### Keeping files from an example run

If you want to have your job persist somewhere accessible, you should edit
`participant_job.sh` and change the line

```bash
cd ${CBICA_TMPDIR}
```

to point somewhere on a network file system. On CUBIC you could use:

```bash
cd /cbica/comp_space/$(basename $HOME)
```

This will create a directory in your project user's `/cbica/comp_space` directory
that you can visit and investigate. This is particularly useful for debugging your
`*_zip.sh` script.

---
**NOTE:** Be sure to change the line back to `cd ${CBICA_TMPDIR}` *before* you
submit jobs for all your subjects. Otherwise they will all run in your
`comp_space` directory, which may fill up.

---


### Making sure you're using the correct singularity image

The bootstrap scripts are supposed to be templates for running a containerized
pipeline on datalad-tracked data. The environment they set up will be correct,
but it is likely that the content of the scripts in `analysis/code` will not
meet your needs.

The most likely change you'll need to make is in the name of the image in the
container dataset. Remember from above that you assign an *image ID* to the
sif file in your container dataset? You will need to make sure that container
ID is referenced in the `datalad run` command in `participant_job.sh` and
in `*zip.sh`. Be sure to make the zip file names match the version of
the image.



## Running a test subject

After editing the scripts in `analysis/code`, saving them and pushing them
to input and output ria stores, it's time to test.
You never know if your scripts will work until you do a test run. To
submit a single subject, you can execute the last line of `code/qsub_calls.sh`
and see how it goes. From the `analysis` directory run

```bash
$ $(tail -n 1 code/qsub_calls.sh)
```

This will submit the last subject. If this run succeeds you should delete the
last line of `code/qsub_calls.sh` so the test subject isn't run again.

Once your test subject finishes, launch the rest of the jobs with

```bash
$ bash code/qsub_calls.sh
```

This will submit one job per subject, which will generate a lot of text printed
in the terminal. Note: if you have other jobs running and want to cancel all
the jobs you launched, you might unintentionally kill non-pipeline jobs if
you delete all your jobs.

# After the pipeline runs

At the end of your pipeline runs, each subject/job will exist as a branch in
the `output` special remote set up by the bootstrap script. These branches will
need to be merged into a single branch to create a complete results dataset.

A script to do this was created during the bootstrapping, to run the merge:

```bash
$ bash code/merge_outputs.sh
```

---
### ⚠️ ⚠️ WARNING ⚠️ ⚠️
NEVER do production work in the `merge_ds` directory. It is meant only
as a place to merge branches, NOT to gather data for further analysis.
The appropriate method for accessing data created by a bootstrap run
is described in the next section.

---


## Accessing files created by a bootstrap script

Once the `code/merge_outputs.sh` script finishes, you will have all
the results from your bootstrap script stored in the `output_ria`.
As an RIA store, it's not intended for a human to work with its contents.
Instead, you should clone from the RIA store to create a normal datalad
dataset, where all your files will be accessible. For example

```bash
$ datalad clone ria+file:///path/to/qsiprep/output_ria#~data qsiprep_outputs
$ cd qsiprep_outputs
$ # Unlock one of the results zip files
$ datalad get sub-1_qsiprep-0.14.2.zip
$ datalad unlock sub-1_qsiprep-0.14.2.zip
$ unzip sub-1_qsiprep-0.14.2.zip
```

this will unzip sub-1's qsiprep results. You'll notice that it is inefficient
and cumbersome to `get` and `unlock` the files you created.

# Running a bootstrap on the outputs of another bootstrap

Suppose you want to extract a specific file from each subject's output
zips. One great way to get all this data is to create a new bootstrap
script that does this. You can find an example
[here](https://github.com/PennLINC/TheWay/blob/main/scripts/cubic/bootstrap-fmriprep-bugcheck.sh).

Other bootstrap scripts work on the outputs of other bootstraps by
design, such as [XCP](https://github.com/PennLINC/TheWay/blob/main/scripts/cubic/bootstrap-xcp.sh),
[QSIRecon](https://github.com/PennLINC/TheWay/blob/main/scripts/cubic/bootstrap-qsirecon.sh),
and the various Audit scripts.