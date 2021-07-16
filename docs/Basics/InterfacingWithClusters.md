---
layout: default
title: Working on CUBIC/PMACS via Visual Code Studio
parent: Computation Basics
nav_order: 3
has_toc: true
---



# Interfacing with the clusters

## General Principles & Motivation
You will need ssh keys set up, a PMACS or CUBIC account (with VPN).

https://pennlinc.github.io/docs/Basics/sshKeys/

## General Principles & Motivation

1. We want to code interactively with zero lag in a format that encourages documenting your code
2. We want the software and data we are using to be on the cluster, preventing version issues or having to download a test set to your local machine
3. We want it to be easy!

This means we are going to not use X11 at all. Why? Because running graphics on the cluster, and then having them sent to your local screen, is very laggy and not dependable.

## Download/Install Visual Code Studio

You can find it here: https://code.visualstudio.com



We want to login to the cluster such that we have a "port" to it.Now start a Jupyter kernel, and then find that kernel via a browser. This is actually pretty easy.

```bash
PORT=$6666 #DO NOT USE THIS ONE. We have to have different ones. This one is satan. If you can't connect, you and someone else probably, somehow, picked the same port
#cluster, start a jupyter instance
PORT=$6666
ssh -L localhost:$PORT:localhost:$PORT  bertolem@cubic-login.uphs.upenn.edu
jupyter notebook --no-browser --NotebookApp.token='' --NotebookApp.disable_check_xsrf=True --port=$PORT
```

Now, in your local web-browser, go to: localhost:PORT (e.g., 6666). You can now start a python or R kernel or a terminal and get to coding! You can work in a notebook for a while, but you can also save it out as a script.

## Common issues

Sometimes your port connection got disconnection. But the port is still "running", so you need to kill it. You can do this for your notebook on the cluster and your connection from the cluster to your local machine:

```bash
lsof -ti:$PORT | xargs kill -9
```
You should be able to reconnect after this.

