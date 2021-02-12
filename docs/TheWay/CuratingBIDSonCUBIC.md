---
layout: default
title: Curating BIDS Datasets on CUBIC 
parent: The Way
nav_order: 2
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
* NOW you're ready to run preprocessing pipelines on one subject from each acquisition group (see cubids-group acq_groups.csv for list)

## Preprocessing Pipelines with Datalad 
Second
