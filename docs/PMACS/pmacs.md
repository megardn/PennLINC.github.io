---
layout: default
title: PMACS
has_children: false
nav_order: 6
permalink: docs/pmacs
has_toc: true
---
# Using Penn Medicine Academic Computing Services (PMACS)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

This section gives an overview of how to get started on PMACS Limited Performance Computing (LPC) cluster. For additional information, check out the [LPC wiki](https://wiki.pmacs.upenn.edu/pub/LPC).

## Obtaining PMACS LPC Access
Send your PI your Pennkey, full name, and Penn email, and have them forward this information to pmacs-sys-sci@lists.upenn.edu and request access for you. When access has been granted, you will receive an email with a temporary password through SecureShare. Log in via [this link](https://reset.pmacs.upenn.edu/) with your temporary password and set a new PMACS password. Your Pennkey and new PMACS password will be used to ssh into the LPC and to access the PMACS help desk/ticketing system.

## Logging in to PMACS LPC
Once you've set up your login credentials for the PMACS LPC, you can SSH into the LPC from anywhere (even without Penn VPN). You can SSH into PMACS as follows:

```bash
$ ssh [username]@scisub.pmacs.upenn.edu
```
Enter PMACS password when prompted. 
You can submit jobs on scisub.

## Submitting tickets
Log in to the [PMACS Help Desk](https://helpdesk.pmacs.upenn.edu/) with PMACS credentials. Under “Quick Actions” click “Need help? Report it." Submit ticket under appropriate category.

## Making a project directory
New project directories must be created for you by PMACS LPC admin. To have a new project directory created, you need to submit a ticket. Log into the [PMACS Help Desk](https://helpdesk.pmacs.upenn.edu/) with PMACS credentials, and submit a "Systems" ticket specifying the desired project folder name (e.g. `grmpy_diffusion`) and who should have read access and read/write access to the folder (e.g. `bbllpc`). Once made, project directories can be found in `project/` (e.g. `/project/grmpy_diffusion`).

## Loading modules and accessing module software 
Environment modules are used to load and unload available software (e.g. ANTs, mrtrix, R, ITK, dcm2niix, fsl, freesurfer, etc.). Hence, in order to use most software/applications that are available on PMACS, you must first load the appropriate module. To check which modules are available on PMACS, type `module avail`. To load a desired module, use `module load [modulename]`. Finally, to see what modules you have loaded, use `module list`.

For example, if I want to load python:
```bash
$ module load python # automatically loads latest version
$ python
$ exit()
```

Modules can be loaded and unloaded both in the log in node and in an interactive shell. You can access interactive shells via `ibash` (standard) or `xbash` (used for graphics, X11 forwarding enabled). In order to use some module software, however, you must be in an interactive shell!

For example:
```bash
$ module load mrtrix3/current
$ mrstats 
```
will return -bash: mrstats: command not found

However,
```bash
$ ibash
$ mrstats
```
will now reveal help documentation for mrstats.

In what circumstances do you use `ibash` you ask? From the [LPC wiki](https://wiki.pmacs.upenn.edu/pub/LPC):

"Use the interactive shell to:
- Prepare you submit scripts
- Find apps, browse appl
- Work on anything you don’t have access to on the submit host
- Browse /project/group directory
- Prototype code and test apps"

## Requesting software installations
Open source free apps and most imaging software needs to be installed by PMACS admin. This means you can’t install R, Stata, or Python packages on your own. You can submit a “Systems” ticket at [PMACS Help Desk](https://helpdesk.pmacs.upenn.edu/) requesting that new software be installed.

## Installing Flywheel on your PMACS account
You can install the Flywheel command line interface (CLI) locally, which is helpful as the CLI version updates frequently. Follow these steps to install the Flywheel CLI and gain access to `fw` commands. Complete these steps on the log in node, not in an interactive shell.

1. Install miniconda in your home directory (`/home/[pmacs username]`):
```bash
$ wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
$ bash ./Miniconda3-latest-Linux-x86_64.sh -b -p $HOME/software/pkg/miniconda3
```
2. Load miniconda
```bash
$ source $HOME/software/pkg/miniconda3/bin/activate # should be whatever folder miniconda3/bin/activate is in
```
3. Create flywheel python environment and activate it
```bash
$ conda create -n flywheel anaconda python=3
$ conda activate flywheel
```
4. Install flywheel-sdk
```bash
$ pip install flywheel-sdk
```
5. Install flywheel-cli
```bash
$ wget https://storage.googleapis.com/flywheel-dist/cli/12.1.1/fw-linux_amd64.zip
$ unzip fw-linux_amd64.zip
```
6. The fw executable should be in the linux_amd64 folder. Add this directory to your path so that the `fw` command is accessible from all locations
```bash
$ echo "export PATH=\$PATH:~/linux_amd64" >> ~/.bashrc
```
7. Log in to flywheel using your API key (obtained from your Flywheel User Profile)
```bash
$ fw login upenn.flywheel.io:$APIkey
$ fw status
$ You are currently logged in as $username to upenn.flywheel.io
```

> Note: Sometimes `fw status` gets you this error message: `'Connection to upenn.flywheel.io timed out. (connect timeout=10)')': /api/auth/status`. In this case, logging out of PMACS and logging in again should fix the issue.

## Mounting a PMACS project directory on your local machine
1. If you are using a Mac, first install [OSXFuse and SSHFS](https://osxfuse.github.io/).

2. Make an empty mount point folder on your local machine. Make sure that only the user (not group or others) have access to this mount directory!
```bash
$ mkdir /Users/$username/ImageData/PMACS_remote
$ chmod 700 /Users/$username/ImageData/PMACS_remote
```
3. Mount the desired PMACS directory to your newly created, local mount directory using sshfs and sciget 
```bash
$ sshfs [username]@sciget.pmacs.upenn.edu:<my-folder-on-PMACS> <my-local-mount-folder> -o defer_permissions,volname=project
```
4. Unmount when done! You should run this unmount command from outside of the mount point.
```bash
$ diskutil umount force /Users/$username/ImageData/PMACS_remote
```

## Enabling X11 forwarding
To use graphics on PMACS, make sure that you:
- Have [XQuartz](https://www.xquartz.org/) installed on your local Mac
- log in to scisub via ssh -Y
- use xbash for your interactive shell 


## Transferring files to and from the LPC
- You can transfer files to and from MPACS with username@transfer.pmacs.upenn.edu. This can be used with SFTP, rsync, and scp. (You cannot use transfer for ssh).
- Sciget can be used with scp
