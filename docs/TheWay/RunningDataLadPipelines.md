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

First, you want to clone the containers dataset. You should build containers in /cbica/projects/RBC/dropbox.
rbc user, for some reason, cannot do this, so do this as your user.

for example, for xcp-abcd-0.0.1:
```bash
singularity build xcp-abcd-0.0.1.sif docker://pennlinc/xcp_abcd:0.0.1
```

Hop back on rbc, then copy to /cbica/projects/RBC/your_container_name
for example:
```bash 
datalad create -D "xcp-abcd container".
```
do that actual copy:
```bash 
datalad containers-add --url ~/dropbox/xcp-abcd-0.0.1.sif xcp-abcd-0-0-1
```

delete original:
```bash
#rm /cbica/projects/RBC/dropbox/dropbox/xcp-abcd-0.0.1.sif
```

## Preparing the analysis dataset

We assume your BIDS data is curated and available either on your local file system
or a remote datalad source.

You can [download](https://github.com/PennLINC/TheWay/tree/main/scripts/cubic) and run one or more of our "bootstrap" scripts that set up an
working directory for the running of your pipeline. Set the `BIDSAPP` variable
to the lowercase version of the pipeline you'd like to run.

The only argument for the boostrap script is the location of the input
data. This can be any datalad-accepted clone source (e.g. ria+ssh://,
ria+file://, https://) or a path to a local directory containing BIDS data.
If a local path, it should be the directory that containse the `sub-`
directories and **must** be a datalad dataset. here we bootstrap an fmriprep analysis:

```bash
$ BIDSAPP=fmriprep
$ BIDSINPUT=/path/to/my/BIDS
$ wget https://raw.githubusercontent.com/PennLINC/TheWay/main/scripts/cubic/bootstrap-${BIDSAPP}.sh
$ bash boostrap-${BIDSAPP}.sh ${BIDSINPUT}
```


This will create a `${BIDSAPP}` directory that contains numerous other
directories needed to run the app at scale. 

---
### ⚠️ ⚠️ WARNING ⚠️ ⚠️ 
Once you have run the command to create your `${BIDSAPP}` directory, 
DO NOT rename or change the path to any of the directories that were inputs to your 
bootstrap script!!

---

Inside the `${BIDSAPP}` directory, the subdirectory most relevant for you is the
`${BIDSAPP}/analysis` directory. This is a regular datalad dataset that
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
`${BIDSAPP}/analysis/code` that will be used to run your job. You will want to
edit these to tailor the call to your prep in case there are any specific
options for this particular run. In `${BIDSAPP}/analysis/participant_job` edit
below the area labeled `# EDIT HERE #` to change arguments for the prep.

Once satisfied with the parameters for your pipeline run, launch the jobs with

```bash
$ bash ${BIDSAPP}/analysis/code/qsub_calls.sh
```

This will create one job per subject, which will generate a lot of text printed
in the terminal. Note: if you have other jobs running and want to cancel all
the jobs you launched, you might unintentionally kill non-pipeline jobs if
you delete all your jobs.

# After the pipeline runs

At the end of your pipeline runs, each subject/job will exist as a branch in
one of the special remotes set up by the bootstrap script. These will need to
be merged into a single branch to create a complete results dataset.

A script to do this was created during the bootstrapping, to run the merge:

```bash
$ bash ${BIDSAPP}/analysis/code/merge_outputs.sh
```

To view all the results files together (not recommended) you can

```bash
$ cd ${BIDSAPP}/analysis
$ datalad get -r .
```

## Saving results

Consider sending your outputs to an RIA store on pmacs so they will be
safe and backed up.
