---
layout: default
title: Fetching Your Data
parent: The Way
nav_order: 1
has_toc: true
---

# Fetching Your Data
{: .no_toc }

There are a number of places you may have to fetch data from to get them onto a cluster filesystem. We will briefly cover best practices for the methods we've used before. Additions will be made as we gain more experience.

## Table of contents
{: .no_toc .text-delta }


## Between Clusters Or Local Disks

The best option for moving a large amount of data between clusters is to use the `scp` command. Remember that this process must remain open and running in your terminal, so it might be useful to do this in a fresh terminal window or use `&` at the end of your command. You could also use [`screen`](https://www.geeksforgeeks.org/screen-command-in-linux-with-examples/) to set up a non-terminating terminal.

As mentioned in our general [PMACS documentation](/docs/pmacs), you should scp *into* a node called `transfer`. That would look like this:

```
## my username is ttapera
scp -r path/to/your/data ttapera@transfer.pmacs.upenn.edu:/path/on/pmacs
```

An alternative to `scp` is `rsync`, but that tends to have [more happening under the hood](https://stackoverflow.com/questions/20244585/how-does-scp-differ-from-rsync).

## Flywheel

On Flywheel, your data may already be in BIDS. In this case we recommend using Flywheel's export function `fw export bids`, or the export function provided by [`fw-heudiconv`](https://fw-heudiconv.readthedocs.io/en/latest/). We built the export function into `fw-heudiconv` because we wanted to have more flexibility in what BIDS data we could grab, including data that's not yet supported in the official BIDS spec. Admittedly though, downloading all of `fw-heudiconv` a lot of overhead for just the export function.

```
# with fw export bids
fw export bids <DESTINATION_DIRECTORY> --project <PROJECT_NAME> --subject <SUBJECT_FILTER>

# with fw-heudiconv
fw-heudiconv-export --project <PROJECT_NAME> --subject <SUBJECTS_FILTER> --session <SESSION_FILTER> --folders <LIST_OF_BIDS_FOLDERS>
```

Try `fw-heudiconv-export -h` for more info.

## Globus

[Globus](https://www.globus.org/) is a research data management platform whose best feature is data transfer and sharing. It's surprisingly easy to use and gets the job done with minimal setup. The data sharing concept revolves around setting virtual *endpoints* that data can be shared to and from. Endpoints can be thought of conceptually as mounts, where you can give outbound network access to a certain directory on your machine or cluster, and by sharing the URL of your endpoint, someone can access your directory through the internet or network cluster.

Currently, the best way to use Globus is either through your local disk or on PMACs (recommended). We're still awaiting CUBIC authorization. The general docs for globus are located [here](https://docs.globus.org/how-to/), but for posterity, here are the best instructions:

On a local disk:

1. Log in to Globus with your UPenn organization account -- [https://docs.globus.org/how-to/get-started/](https://docs.globus.org/how-to/get-started/) -- and try out the tutorial for sharing between two test endpoints on Globus' system
2. Download and install [Globus Connect Personal](https://www.globus.org/globus-connect-personal); this service will manage the endpoint on your local machine
3. Download and install the [CLI](https://docs.globus.org/cli/) with pip -- remember to use conda environments! This service will allow you to manage the Globus session when it's running
4. [Login with the CLI](https://docs.globus.org/cli/quickstart/) and transfer your data either through the CLI commands or by visiting the file manager (which you saw in step 1). If someone has shared a Globus endpoint with your account, you'll have access to it in "Endpoints".

On PMACs:

0. Make sure you have access to the PULSE Secure VPN -- [remote.pmacs.upenn.edu](remote.pmacs.upenn.edu)

1. Log in to PMACs' dedicated node for Globus functionality:
```
# first ssh into sciget for network access
ssh -y ttapera@sciget.pmacs.upenn.edu

# then from sciget, log onto the globus node
ssh -y ttapera@sciglobus.pmacs.upenn.edu
```

2. Globus Connect Personal should be available. As above, use it to initialize an endpoint on a directory of your choice on PMACs. Specifically, you should run it as below so that it opens a GUI for logging in with an auto-generated token:
```
# this command will return a URL you can open in any browser and a token you can use to sign in
globusconnect -start &
```

3. Using a new or existing conda environment (see [here](https://pennlinc.github.io/docs/pmacs#logging-in-to-pmacs-lpc) for how to activate conda on PMACs), install the [CLI](https://docs.globus.org/cli/) using `pip` and login with `globus login`.

4. Visit [https://docs.globus.org/how-to/get-started/](https://docs.globus.org/how-to/get-started/) to access the File Manager, as in the Local Disk instructions, to start transferring data.
