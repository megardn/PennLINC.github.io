---
layout: default
title: Interfacing with the clusters
parent: TheWay
nav_order: 1
has_toc: true
---

# Interfacing with the clusters
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

# Connecting to the clusters

PMACS

```bash
#my username is mb3152
#login
ssh -Y mb3152@scisub.pmacs.upenn.edu
#mount to your home directory on pmacs, using a local directory called "/Users/maxwell/upenn/"
sshfs mb3152@sciget.pmacs.upenn.edu:/home/mb3152 /Users/maxwell/upenn/ -o follow_symlinks
```
CUBIC 
```bash
#my username is mb3152
#login
ssh -Y bertolem@cubic-login.uphs.upenn.edu
#mount to your home directory on pmacs, using a local directory called "/Users/maxwell/CUBIC/"
sshfs bertolem@cubic-login.uphs.upenn.edu://cbica/home/bertolem/ /Users/maxwell/CUBIC/ -o follow_symlinks
```

## Text Editor

We support [Atom](https://atom.io), as it allows you to code interactively using a kernal you start on the cluster. What does this mean? It means you have a nice shiny IDE, but you are using the software and data on the cluster. Best of all worlds.
