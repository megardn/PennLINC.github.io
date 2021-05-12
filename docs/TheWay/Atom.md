---
layout: default
title: Atom on the clusters (with GitHub)
parent: The Way
nav_order: 3
has_toc: true
---

# Atom on the cluster (with GitHub)

## Writing Code Interactively in Atom

[Atom](https://atom.io) allows you to code interactively using the kernel you started on the cluster. What does this mean? It means you have a nice shiny IDE, but you are using the software and data on the cluster. Best of all worlds.

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
Type this in the box but replace 6666 with what PORT number you want to use (anything but 6666):
[{
  "name": "Remote server",
  "options": {
    "baseUrl": "http://localhost:6666"
  }
}]

If you are using a Mac, first install [OSXFuse](https://github.com/osxfuse/osxfuse/releases/download/macfuse-4.0.5/macfuse-4.0.5.dmg) and [SSHFS](https://github.com/osxfuse/sshfs/releases/download/osxfuse-sshfs-2.5.0/sshfs-2.5.0.pkg).

### Make sure you have access to your code on the cluster:

PMACS
```bash
#mount (my username is mb3152)
sshfs mb3152@sciget.pmacs.upenn.edu:/home/mb3152 /Users/maxwell/upenn/ -o follow_symlinks
```
CUBIC
```bash
#mount (my username is bertolem)
sshfs -o follow_symlinks bertolem@cubic-login.uphs.upenn.edu://cbica/home/bertolem/ /Users/maxwell/CUBIC/
```

### Start a kernal on the cluster, connect to it from your local machine
```bash
PORT=$6666 #DO NOT USE THIS ONE. We have to have different ones. This one is satan. If you can't connect, you and someone else probably, somehow, picked the same port
#local terminal, this connects you to the jupyter instance
ssh -L localhost:$PORT:localhost:$PORT bertolem@cubic-login.uphs.upenn.edu
#once logged in:
jupyter notebook --no-browser --NotebookApp.token='' --NotebookApp.disable_check_xsrf=True --port=$PORT
```

### Connect to kernal in Atom
In Atom, once you have a python or R script open, go to Packages > Hydrogen > Connect to Remote Kernal. Since you updated your settings, it will automatically connect to the one you have running.

Click Remote Server, then [New Session], then Python 3 or R.

Now you can code interactively, using the python or R on pmacs.
![interactive](./interactive.png)

## Common issues

Sometimes your port connection got disconnection. But the port is still "running", so you need to kill it. This can be done both on the cluster and locally:

```bash
lsof -ti:$PORT | xargs kill -9
```
You should be able to reconnect after this.

## GitHub Integration

You can track your work using github.com. Both Git and GitHub can be found in Atom under Pacakges -> GitHub.

If you have a github repo directory open, Atom will detect this. If it's not a repo yet, you can initialize it in Atom in the Git panel. 

Next, in Atom, you "stage" your changed and commit them in Git, and the push using GitHub (tiny "push" button in the bottom right corner).
