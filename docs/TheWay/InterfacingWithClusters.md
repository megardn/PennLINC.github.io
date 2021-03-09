---
layout: default
title: Interfacing with the clusters via Jupyter and MacFuse
parent: The Way
nav_order: 2
has_toc: true
---

# Interfacing with the clusters
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

## General Principles & Motivation

1. We want to code interactively in a shiny text editor or integrated development environment (IDE) with zero lag
2. We want the software and data we are using to be on the cluster, preventing version issues or having to download a test set to your local machine
3. We want to be able to view brains easily
4. We want it to be easy!

This means we are going to not use X11 at all. Why? Because running graphics on the cluster, and then having them sent to your local screen, is very laggy and not dependable.

## Connecting to the Clusters

This is covered in each cluster's specific documentation, but for completeness:

If you are using a Mac, first install [OSXFuse](https://github.com/osxfuse/osxfuse/releases/download/macfuse-4.0.5/macfuse-4.0.5.dmg) and [SSHFS](https://github.com/osxfuse/sshfs/releases/download/osxfuse-sshfs-2.5.0/sshfs-2.5.0.pkg).

PMACS

```bash
#login (my username is mb3152)
ssh -Y mb3152@sciget.pmacs.upenn.edu
```
CUBIC
```bash
#login (my username is bertolem)
ssh -Y bertolem@cubic-login.uphs.upenn.edu
```

## Writing Code Interactively in a Jupyter Notebook

We are going to ssh into a cluster, start a Jupyter kernel, and then find that kernel via a browser. This is actually pretty easy.

```bash
PORT=$6666 #DO NOT USE THIS ONE. We have to have different ones. This one is satan. If you can't connect, you and someone else probably, somehow, picked the same port
#local terminal, get into pmacs
ssh -Y mb3152@sciget.pmacs.upenn.edu
#cluster, start a jupyter instance
PORT=$6666
jupyter notebook --no-browser --NotebookApp.token='' --NotebookApp.disable_check_xsrf=True --port=$PORT
#local terminal, this connects you to the jupyter instance
PORT=$6666
ssh -N -L localhost:$PORT:localhost:$PORT  mb3152@sciget.pmacs.upenn.edu
```

Now, in your local web-browser, go to: localhost:6666. You can now start a python or R kernel or a terminal and get to coding!

## Viewing brains

We going to break principle 2 here, but for good reason. Whatever you use to look at brains, download it locally. I prefer wb_view, but this applies to any viewer. Once you have it set up, mount your local disk on the cluster:

PMACS

```bash
#my username is mb3152
#mount to your home directory on pmacs, using a local directory called "/Users/maxwell/upenn/"
sshfs mb3152@sciget.pmacs.upenn.edu:/home/mb3152 /Users/maxwell/upenn/ -o follow_symlinks
```
CUBIC
```bash
#my username is bertolem
#mount to your home directory on pmacs, using a local directory called "/Users/maxwell/CUBIC/"
sshfs bertolem@cubic-login.uphs.upenn.edu://cbica/home/bertolem/ /Users/maxwell/CUBIC/ -o follow_symlinks
```
Now, you can use Finder, and you can just navigate to the brain you want to look at.

## Bonus for Pros: Jupyter Lab

JupyterLab is an extension of Jupyter Notebooks (which itself is an extension of IPython). With JupyterLab, you can actually turn the Jupyter Notebooks environment currently sitting in your browser into a pretty powerful IDE, including panels and tabsets, Git and Github integration, linting, and more. Just like any IDE, it's up to you what you'd like set up in there. Visit [JupyterLab](https://jupyterlab.readthedocs.io/en/stable/) to get started, and read about a bunch of cool extensions [here](https://github.com/mauhai/awesome-jupyterlab).
