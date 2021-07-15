---
layout: default
title: Current Projects
has_children: True
has_toc: false
nav_order: 10
---

# Current Projects

These pages provide an overview of the current projects completed/close to completion in the lab.

To add a project to this list, please first read the [documentation guide](/docs/Contributing/contributing) to familiarize yourself with the process of adding docs to this website. When it comes time to add your file, create a new markdown document in `/docs/CurrentProjects/<YOURPROJECTNAME>.md`. At the top of the file, insert a YAML header like the one below:

```
---
layout: default
title: MY PROJECT NAME # keep this short!!!
has_children: false
parent: Current Projects
---
```

A template of the content of the project page is below. Simply copy it into your markdown file and fill in the relevant fields. Each project should additionally have a GitHub Pages site — if you've never made a Github pages site, see the tutorial [here](/docs/Contributing/project-documentation/).

For additional information see the [Project Setup Page](/docs/LabHome/ProjectSetup/).

----------------------------------------

### Project Title

Brief and informative title; not always the same as the manuscript title

### Brief Project Description

Try to make this no more than three highly accessible sentences.

### Project Lead(s)

Anticipated First Author(s)

### Faculty Lead(s)

Anticipated Last Author(s)

### Analytic Replicator

Team member who replicates analyses, as stipulated in the [Project Reproducibility  Guide](https://pennlinc.github.io/docs/LabHome/ReproSystem/)

### Collaborators

Include people here as they make contributions (useful as our memory is imperfect and it is bad to forget contributions in long-running projects)

### Project Start Date

Date that project began

### Current Project Status

See the [Stages of a Project](https://pennlinc.github.io/docs/LabHome/ProjectStages/) page

### Dataset

E.g., PNC, HBN, GRMPY, etc

### Github repo

Link to github repository for the project

### Path to data on filesystem

Full path to project path on relevant computing cluster (i.e., CUBIC, PMACS, etc)

### Slack Channel

For project communication

### Trello board

For task tracking and keeping project meetings on track; provide link.

### Google Drive Folder

A good place to aggregate and share articles, manuscript drafts, etc; provide link.

### Zotero library

Shared library name for references

### Current work products

I.e., citations to poster presentations, links to preprints, final publication citation

### Code documentation

This section is the bulk of the project page, and can be broken up as best fits the project.  Remember that this should be acessible prose that allows your replicator, reviewer, or interested reader to step through your code and understand how the code corresponds to the findings described in the paper. At a minimum, there should be clear documentation regarding sample selection (e.g., inclusion/exclusion), preprocessing (e.g., container version, data freeze), and hypothesis testing (usually in the form of an analytic notebook).   See the [Project Reproducibility  Guide](https://pennlinc.github.io/docs/LabHome/ReproSystem/) for more information.
