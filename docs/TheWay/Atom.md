---
layout: default
title: Interfacing with the clusters via Jupyter
parent: The Way
nav_order: 5
has_toc: true
---

# Interfacing with the clusters
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

## General Principles & Motivation

## Bonus for Pros: Jupyter Lab

JupyterLab is an extension of Jupyter Notebooks (which itself is an extension of IPython). With JupyterLab, you can actually turn the Jupyter Notebooks environment currently sitting in your browser into a pretty powerful IDE, including panels and tabsets, Git and Github integration, linting, and more. Just like any IDE, it's up to you what you'd like set up in there. Visit [JupyterLab](https://jupyterlab.readthedocs.io/en/stable/) to get started, and read about a bunch of cool extensions [here](https://github.com/mauhai/awesome-jupyterlab).

## Bonus for Pros: Writing Code Interactively in Atom

If you want a bit more power and, in our opinion, a prettier editor, We support [Atom](https://atom.io), as it allows you to code interactively using the kernel you started on the cluster. What does this mean? It means you have a nice shiny IDE, but you are using the software and data on the cluster. Best of all worlds.

Once you have downloaded and installed Atom, install some packages. It is easiest to do this on the command line. To activate command line tools, open Atom, go to the menu bar up in the top-left: Atom > Install Shell Commands.

Then copy and paste this into your Terminal (outside of Atom for now!):
```bash
apm install hydrogen
apm install script
apm install autocomplete-python
apm install ide-r
apm install language-r
apm install platformio-ide-terminal
```

Okay, one more thing. We need to tell Atom what port to expect the Jupyter session on.
Go into the Preferences for Atom: File > Preferences.
Select Packages on the left, and then click on the Settings button for Hydrogen.

![atom2](./atom-hydrogen2.png)

Scroll down to the Kernal Gateways Section
![atom3](./hydrogen-port.png)
Type this in the box:
[{
  "name": "Remote server",
  "options": {
    "baseUrl": "http://localhost:6666"
  }
}]

We follow the same process as above: ssh into a cluster, start a Jupyter kernel, connect to that port. However, instead of opening up in a browser, we find that kernel via Atom:

in Atom, go to Packages > Hydrogen > Connect to Remote Kernal. Since you updated your settings, it will automatically connect to the one you have running.

Click Remote Server, then [New Session], then Python 3 or R.

Now you can code interactively, using the python or R on pmacs.
![interactive](./interactive.png)

## Common issues

Sometimes your port connection got disconnection. But the port is still "running", so you need to kill it

```bash
lsof -ti:PORT | xargs kill -9
```
You should be able to reconnect after this.
