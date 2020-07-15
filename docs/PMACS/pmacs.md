---
layout: default
title: PMACS
has_children: false
nav_order: 5
has_toc: true
---
# Using Penn Medicine Academic Computing Services (PMACS) 
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

This section gives an overview of how to get started on PMACS Limited Performance Computing (LPC) cluster. The [LPC wiki](https://wiki.pmacs.upenn.edu/pub/LPC) is super helpful for other questions and issues related to PMACS.

## Obtaining access to PMACS
Send Ted your Pennkey, full name, and email, and he will contact pmacs-sys-sci@lists.upenn.edu. You will receive an email with a temporary password through SecureShare. Log in via [this link](https://reset.pmacs.upenn.edu/) with your temporary password and choose a new PMACS password  


## Logging into PMACS
Once you set up your login credentials for PMACS, you can connect to it anywhere (even without Penn VPN) using SSH. The login looks like this:

```bash
$ ssh [username]@scisub.pmacs.upenn.edu 
```
Most people login to submit jobs to scisub. Enter PMACS password when prompted. Sadly, you won't be greeted with a fancy message like in CUBIC, but at least you're in.

## Making a project directory 
To make a project directory, you need to submit a ticket. Log into the [PMACS Help Desk](https://helpdesk.pmacs.upenn.edu/) with PMACS credentials, and submit “Systems” ticket with project folder name (i.e. grmpy_diffusion) and who it’s accessible to (i.e. bbllpc). Once made, project directories are all under `project/` 
(i.e. `/project/grmpy_diffusion`). 

## Loading modules 
To check modules that are available, use `module avail`. To load a module, use `module load [modulename]`. Finally, to see what modules you have loaded, use `module list`. 

For example, if I want to load python:
```bash
$ module load python # automatically loads latest version
$ python 
$ exit()
```

To start an interactive module, type `ibash`. You should see something like:
```bash
$ Job <5522235> is submitted to queue <bbl_interactive>.
$ <<Waiting for dispatch ...>>
$ <<Starting on galton>>
```
In what circumstances do you use `ibash` you ask? From the [LPC wiki](https://wiki.pmacs.upenn.edu/pub/LPC):

"Use the interactive shell to: 
- Prepare you submit scripts 
- Find apps, browse appl 
- Work on anything you don’t have access to on the submit host 
- Browse /project/group directory 
- Prototype code and test apps"

## Install software
Any open source free apps needs to be installed by PMACS admin. Note that you can’t install R, Stata, or Python packages on your own. You can submit a “Systems” ticket at [PMACS Help Desk](https://helpdesk.pmacs.upenn.edu/) with PMACS credentials.

## Install Flywheel locally on PMACS
You can install Flywheel locally, which is helpful since it updates so frequently.

1. Make sure to use the login node (meaning don’t use interactive node like `ibash`, like I did at first…oops. You’re automatically in login node once you log in  --  hence ‘login node.’)
2. Install miniconda in your home directory (`/home/[pmacs username]`):
```bash
$ wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
$ bash ./Miniconda3-latest-Linux-x86_64.sh -b -p $HOME/software/pkg/miniconda3
```
3. Load miniconda
```bash
$ source $HOME/software/pkg/miniconda3/bin/activate # should be whatever folder miniconda3/bin/activate is in
```

4. Create python environment and activate it
```bash
$ conda create -n flywheel anaconda python=3
$ conda activate flywheel
```
5. Install flywheel-sdk
```bash
$ pip install flywheel-sdk
```
6. Install flywheel-cli
```bash
$ wget https://storage.googleapis.com/flywheel-dist/cli/12.1.1/fw-linux_amd64.zip
$ unzip fw-linux_amd64.zip
```
7. The fw executable should be in the linux_amd64 folder. `fw` command should now work. 
8. Log into flywheel
```bash
$ fw login upenn.flywheel.io: #######
$ fw status
$ You are currently logged in as Audrey Luo to upenn.flywheel.io
```
> Note: To use Flywheel CLI on PMACS, you need to load miniconda and activate your conda environment every time. 
```bash
$ source $HOME/software/pkg/miniconda3/bin/activate
$ conda activate flywheel
```
> Another note: Sometimes `fw status` gets you this error message: `'Connection to upenn.flywheel.io timed out. (connect timeout=10)')': /api/auth/status`. In this case, logging out of PMACS and logging in again should fix the issue. 


## Mounting PMACS project directory on your local machine 
1. If you're using a Mac, install [OSXFuse and SSHFS](https://osxfuse.github.io/).
2. Make a mount point folder in your home directory:
```bash 
$ mkdir /Users/audreyluo/remote
```
3. **Not sure if I actually needed to create a shared key but this was one of the steps I did:
Create a shared key: 
```bash
$ ssh-keygen -t rsa
Enter file in which to save the key (/Users/audreyluo/.ssh/id_rsa): /Users/audreyluo/.ssh/pmacs_id_rsa # type in path here
```
4. Copy key and install `ssh-copy-id` script: 
```bash
$ brew install ssh-copy-id
$ ssh-copy-id -i ~/.ssh/pmacs_id_rsa.pub [username]@sciget.pmacs.upenn.edu
```
5. Check to see if the key successfully connects to server:
```bash
$ ssh -i ~/.ssh/pmacs_id_rsa [username]@sciget.pmacs.upenn.edu
```
> Note: You need to use 'sciget’ to connect to transfer or obtain your data.

6. Mount using sshfs
```bash
$ sshfs [username]@sciget.pmacs.upenn.edu:<my-folder-on-PMACS> <my-local-folder>
```
For example:
```bash
 
$ sshfs [username]@sciget.pmacs.upenn.edu:/project/grmpy_diffusion /Users/audreyluo/project -o defer_permissions,volname=project
```

## Enable X11 forwarding 
If you're using a Mac, download and open new terminal in [XQuartz](https://www.xquartz.org/). Log into PMACS with X11 forwarding enabled (-Y on Macs): 
```bash
$ ssh -Y [username]@scisub.pmacs.upenn.edu
$ xbash # interactive job on Galton, but with X11 forwarding enabled
```
> Note: X11 doesn’t work with some commands like `mrview` and `shview` from MRtrix. But you can use X11 forwarding with SAS or other instances when you need graphics.

## Use `scp` to download files from PMACS
```bash
$ scp [username]@transfer.pmacs.upenn.edu:/project/project_dir/file /your/local/directory/file
```
> Note: make sure to use transfer.pmacs.upenn.edu
 
 
## Submitting tickets
Log into [PMACS Help Desk](https://helpdesk.pmacs.upenn.edu/) with PMACS credentials. Under “Quick Actions” click “Need help? Report it." Submit ticket under appropriate category. 

