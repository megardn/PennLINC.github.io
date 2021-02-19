---
layout: default
title: Curating BIDS Datasets on CUBIC
parent: The Way
nav_order: 3
has_toc: true
---

# Curating BIDS Datasets on CUBIC
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

## General princples & motivation
1. We wanted to create a generalizable workflow for curating BIDS datasets on CUBIC.
2. This workflow includes running a BIDS dataset through CuBIDS (validation and grouping), and using Datalad to run preprocesssing pipelines.

## Curration with CuBIDS
* Familiarize yourself with the CuBIDS and Datalad documentation
* First you are going to need to take your dataset through the CuBIDS workflow.
* Obtain a full copy of your BIDS dataset on CUBIC
* Create a conda environment for CuBIDS/Datalad (you should have miniconda installed and git set up on your project user)
    * **conda create cubids**
        * Datalad is a dependency of CuBIDS, so it will be installed when you install CuBIDS
* Activate your new conda environment
    * **conda activate cubids**
* Install CuBIDS in your conda environment
    * **git clone [git@github.com:PennLINC/BOnD.git](git@github.com:PennLINC/BOnD.git) ./cubids**
    * **cd cubids**
    * **pip install -e .**
        * This command will install all dependencies of CuBIDS in the conda envrionment, including Datalad
* Now that you have CuBIDS installed, you're ready to go through the CuBIDS workflow.
    * **cubids-add-nifti-info dataset_path**
        * Run this if you want information from the nifti files to be considered in creating param/acquisition groups.
    * **cubids-validate dataset_path output_path**
        * This command wraps the bids-validator and will output a csv with all your validation errors
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
