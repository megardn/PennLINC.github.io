---
layout: default
title: Informatics Onboarding
parent: Onboarding
grand_parent: Lab Basics
has_toc: true
---

# Informatics Onboarding
{: .no_toc}

The Informatics team plays a significant role in PennLINC's scientific engine. This page outlines the onboarding process for informatics work, including data curation, analysis, and software development, and is particularly geared towards new data analysts. The goal of this section is to provide the resources necessary to develop the **core competencies** for informatics at PennLINC. Note that there is no expectation that you master *all* of the competencies below in one sitting - just that you are familiar with how to go about accomplishing these tasks (or at least google your way through it).

[Jump to the Epilogue](#epilogue)

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

# Core Competencies
{: .no_toc}

The core competencies of informatics are split into 3 major units:

1. [Programming & Software Tools](#unit-1-programming--software-tools)

2. [Data Management, Curation, & Pipelines](#unit-2-data-management-curation--pipelines)

3. [Hypothesis Testing & Analysis]() (Coming Soon!)

The units are designed around the day-to-day tasks of Informatics team members, with different tasks utilizing a number of competencies from each of these units. In a perfect world, Informatics team members are experts in all 3 units, but in practice, your eventual expertise will depend on your assigned projects within the lab, along with past experience, interests, and desire to learn.

In the sections below, we outline each major competency by asking simple, task-based questions, and provide our best recommended resource for learning each of the competencies, as well as a glossary of (optional) secondary resources. There is an estimated time to complete these, but don't feel locked into these time-frames. If you have previously developed one of these skills before, and can confidently answer a question already, feel free to briefly skim the resource.

---

## Unit 1: Programming & Software Tools

Being a competent programmer is fundamental to good neuroscience. It's important to remember that languages come and go — understanding the principles of programming is more important than spending hours memorizing one language's idiosyncrasies.

### The Command Line

Working from the command line is a **must**, as we spend a large amount of time on computing clusters like [PMACS](/docs/pmacs) and [CUBIC](/docs/cubic). Using  the command line is a skill with increasing returns, and benefits you more the better you get at it - don't get discouraged if it feels slow at first.

> Q: How would you create a directory from the command line, move files into it, and loop over its contents?

> Q: How would you record the visual text output of a program in a file?

> Q: How would you make sure your shell always loads with the correct paths to programs set automatically?

We recommend RyansTutorials.net (the [Linux](https://ryanstutorials.net/linuxtutorial/) and [bash scripting](https://ryanstutorials.net/bash-scripting-tutorial/) sections) for this competency (3-5 hours).

Other resources:

- [w3 schools](https://www.w3resource.com/linux-system-administration/linux-commands-introduction.php)


### Python

Python is a powerful all-purpose language for data science. It is particularly popular in neuroimaging due to its low skill floor and high skill ceiling/extensibility — you can do anything from simply adding two numbers together, to building deep learning neuroimaging pipelines.

> Q: What are the basic data structures in Python programming? How do they differ? What scenarios are appropriate for using one particular data structure in comparison to others?

> Q: How would you read in a table in Python? How would you loop over the rows in a table?

> Q: What is a `class`? How would you list all of the available methods of a `class`? What's the difference between a `class`' method and a `class`' attribute?

> Q: How would you run a Python program? How would you check for errors? What is a `traceback`?

We recommend [Coursera](https://www.coursera.org/learn/python-crash-course#syllabus) for this part (> 8 hours), although Python is a *very* common language with many resources available:

- [RealPython](https://realpython.com/start-here/)
- [Codecademy](https://www.codecademy.com/learn/learn-python-3)
- [A handy lookup repo](https://github.com/rasbt/python_reference)


### R

R is a similarly popular data science language, but tends to favor a slightly narrower use case: statistical and numerical analysis. While it is well-equipped to handle myriad programming tasks, R really shines once you are ready to work on tabular data, run statistical anaylsis on experiments, plot beautiful graphs and figures, and produce high-quality reports. R is the go-to language for the statistics portion of any scientific endeavour.

> Q: How would you simulate a random sampling of numbers? How would you plot that sample in a histogram?

> Q: What is a Dataframe? How would you create one from a CSV file?

> Q: What is "tidy" data? The Tidyverse? What are "verbs" in tidy R parlance?

> Bonus: [Why is R so weird?](https://twitter.com/WhyDoesR)

We recommend *either* Roger Peng's [R Programming for Data Science](https://bookdown.org/rdpeng/rprogdatascience/), *or* [R4DS](https://r4ds.had.co.nz/) by Hadley Wickham. Both are long, so for the sake of time, focus on sections 1 through 15 of Peng, and sections 1 through 21 of Wickham (> 8 hours). Getting through the entirety of either book, however, is well worth the time if you want to be very familiar with R.

Other resources:

- [Swirl](https://swirlstats.com/students.html), an interactive prompt-based learning package
- [learnR4free](https://www.learnr4free.com/index.html) and [this blog](https://livefreeordichotomize.com/2020/07/02/so-you-want-to-learn-r/) collate a number of external resources

### Git & Github

Version control with Git and Github is essential for managing reproducible science. The Informatics team handles all of their projects using git, and collaboration on large projects is accomplished primarily through Github Pull Requests, so fluency with basic git commands and familiarity with a Github-based workflow are daily requirements.

> Q: What is version control? What kinds of files are appropriate for version controlling software?

> Q: What is the difference between a `commit` and a `push`?

> Q: What is a `branch`? How could one use a `branch` to manage different ideas/experiments in code/analysis?

> Q: What is a Pull Request? Why is it useful for collaboration?

While git is essential, it doesn't take very long to learn, and the basics quickly become second nature. We recommend [this](https://www.youtube.com/watch?v=SWYqp7iY_Tc) Github crash course (1 hour).

Other resources:

- [HappyGitWithR](https://happygitwithr.com/)

### Miscellaneous

This section mentions a few other technologies that are not essential for getting started, but will become necessary as you work on more complex projects. Skim the links (or Google these) to learn more.

- [Containerization with Docker](https://ropenscilabs.github.io/r-docker-tutorial/01-what-and-why.html) is used frequently to share reproducible software
- [Jupyter](https://realpython.com/jupyter-notebook-introduction/) and [Rmarkdown](https://rmarkdown.rstudio.com/lesson-1.html) for creating reproducible notebooks
- [Python environment management with Conda](https://towardsdatascience.com/devops-for-data-science-making-your-python-project-reproducible-f55646e110fa)

[Jump to top](#informatics-onboarding)

---

## Unit 2: Data Management, Curation, & Pipelines

This section introduces the basic data types in neuroimaging, how they are stored, and what software pipelines are frequently used for preprocessing. These competencies are particularly important for Informatics team members.

---
NOTE: Many resources in this unit walk through hands-on tasks that won't be possible until you've been assigned a project. Once you've been assigned a project, you can revisit these interactive portions. Until then, just watch/read along to develop some familiarity with the software and workflows being demonstrated.

---

### Introduction to MRI Modalities

A good foundation in the basic physics of MRI, and how the physics extends to different *modalities* of MRI, is essential:

> Q: What is magnetic resonance imaging? What is the BOLD signal?

> Q: What is the difference between a structural and a functional image?

> Q: What is diffusion in MRI? What is ASL?

We recommend [MRI physics with Dylan Tisdall](https://www.youtube.com/watch?v=SwsH64PBBZE) (< 1 hour).

Other resources:

- MRI Physics series with [Geoff Aguirre](https://www.youtube.com/channel/UCQ6zwRPkmDrZj9N6Inl8UZw)
- Diffusion imaging from [Albert Einstein College of Medicine](https://www.youtube.com/watch?v=dW8Yh-c2xVY&ab_channel=AlbertEinsteinCollegeofMedicine)
- Various sections of [AndysBrainBook](https://andysbrainbook.readthedocs.io/en/latest/index.html)

### BIDS

Brain Imaging Data Structure is a data storage standard for neuroimaging. It describes how best to format and store neuroimaging data (and why it's important to do so). PennLINC expects all of its data, both internal and publicly shared, to be in BIDS format — much of the work of the Informatics team is making sure this goal is being met across different projects.

> Q: What are the benefits of using BIDS?

> Q: What modalities are currently covered in BIDS? What happens if the data you have isn't covered in BIDS?

We recommend learning about BIDS straight from the source: the [official BIDS specification](https://bids-specification.readthedocs.io/en/stable/) (< 1 hour). It's not necessary to learn the BIDS specification for each imaging modality by rote, though.

Other resources:

- [AndysBrainBook](https://andysbrainbook.readthedocs.io/en/latest/OpenScience/OS/BIDS_Overview.html)

### Data Warehousing with Flywheel

Neuroimaging produces vast amounts of data. It's not practical to expect large projects to store and access data on one hard disk drive, or even one cluster. At a certain point, it becomes necessary to leverage cloud computing to store and access data, and much of the engineering involved in this method has be outsourced to professional engineering companies. PennLINC houses much of its data on the Flywheel platform, so familiarity with Flywheel is essential for informatics work.

> Q: How is data in Flywheel structured? What are the relationships between different `containers`?

> Q: How would you view a particular scan on the website, given the session and subject labels? How would you find the same data programmatically?

> Q: How would you edit metadata for an entire collection of subjects?

> Q: What is a `gear`? How would you run `gears` (both on the website and programmatically)?

There are admittedly a large number of Flywheel resources that are not (yet) organized optimally. It's recommended that you follow the order below:

- [General overview](https://docs.flywheel.io/hc/en-us/sections/360001703554-Flywheel-Overview) and the [Overview Webinar](https://docs.flywheel.io/hc/en-us/articles/360044328514-Webinar-series-Intro-to-Flywheel) (1 hour)
- [SDK Webinar](https://docs.flywheel.io/hc/en-us/articles/360044853993-Webinar-series-Intro-to-the-Flywheel-SDK) (1 hour)
- [BIDS on Flywheel](https://docs.flywheel.io/hc/en-us/articles/360008162154-BIDS-Overview) (< 1 hour)
- [Running Gears](https://pennlinc.github.io/docs/flywheel/sdk_running/) (< 1 hour)


### BIDS Curation on Flywheel

As mentioned, a major task for the Informatics team is the curation of data into BIDS. This competency lets you to accomplish this on Flywheel using an internal tool called `fw-heudiconv`, developed and maintained by Tinashe Tapera.

> Q: What is a BIDS heuristic? How would you create a heuristic for `fw-heudiconv`?

> Q: How do you run `fw-heudiconv`?

> Q: How would you change something you've curated?

`fw-heudiconv` has been documented internally:

- [On readthedocs](https://fw-heudiconv.readthedocs.io/en/latest/overview.html)
- [On this site!](/docs/flywheel/bids/#step-by-step-bids-curation-with-fw-heudiconv)

(< 1 hour)

### Preprocessing Pipelines

Before hypotheses can be tested, neuroimaging data needs to be preprocessed. There are a number of tools for preprocessing — each is appropriate for a particular modality or scan type, and each has its own set of features and limitations. You'll get to work very closely with one (or many) of these pipelines once assigned a project, so basic familiarity with the tools suffices for now.

#### fMRI Data

For fMRI, we recommend `fMRIPrep`. Start with [this primer on fMRI Preprocessing](https://andysbrainbook.readthedocs.io/en/latest/fMRI_Short_Course/fMRI_04_Preprocessing.html), explaining what the different steps for preprocessing are and why they're necessary; then, take a look at the official [fMRIPrep readthedocs](https://fmriprep.org/en/stable/) (or jump to [this section of AndysBrainBook](https://andysbrainbook.readthedocs.io/en/latest/OpenScience/OS/fMRIPrep.html)) to see how `fMRIPrep` is run.

Additionally, check out the [Nature Methods paper](https://www.nature.com/articles/s41592-018-0235-4) written for the software.

#### Diffusion Data

For diffusion imaging, we recommend `QSIPrep`, developed primarily by Dr. Matt Cieslak (manuscript in preparation). Learn about `QSIPrep` at its official [readthedocs site](https://qsiprep.readthedocs.io/en/latest/).

#### ASL Data

For diffusion imaging, we recommend `ASLPrep`<sup>[1](#myfootnote1)</sup>, developed primarily by Dr. Azeez Adebimpe (manuscript in preparation). Learn about `ASLPrep` at its official [readthedocs site](https://aslprep.readthedocs.io/en/latest/).

#### Functional Connectivity & More

Even after having run `fMRIPrep`, there may be more artifact (non-useful noise or poor signal) that can be removed, and more interesting derivates (new variables calculated from the signal) that can be computed — our in house tool for accomplishing this is `XCPEngine`, developed primarily by Rastko Ciric (an alumni data analyst). Learn more about `XCPEngine` at its official [readthedocs site](https://xcpengine.readthedocs.io/), or by reading the [benchmarking paper](https://pubmed.ncbi.nlm.nih.gov/28302591/).

[Jump to top](#informatics-onboarding)

---
# Epilogue

These *core competencies* detailed above may seem overwhelming. There is a lot of information and readers may be coming from different scientific backgrounds, with different levels of experience. Some of the terminologies used in neuroimaging can feel like jargon, and it doesn't help that PennLINC, and the University itself, may have their own unending lists of foreign words, phrases, tools, softwares...

*Don't panic*.

These resources are here for *everyone's* reference, and always will be. It's not expected that you will breeze through all of the above in one sitting, especially in addition to other lab-wide onboarding tasks. Instead, remember:

- **Work at a comfortable pace**: fast enough to get through the material, but not so fast that you don't get to appreciate the material.
- **Reach out for help**: don't spend so long on a problem that it consumes your entire day. The sites listed may not always be perfect resources for learning, and sometimes more assistance is necessary.
- **Google is your friend**: don't hesitate to go outside of our recommended list/glossary of resources
- **You'll get your hands dirty eventually**: it's normal to feel like you haven't developed a competency when you haven't yet actually *run* some of these tools and programs.
- **Contribute**: science gets better when we do it together. If you find a superior/additional resource for learning these competencies, don't hesitate to [contribute](/docs/Contributing/documentation_guidelines/) to this document.

# *Good luck!*
{: .no_toc}

---
<a name="myfootnote1">1</a>: Are you starting to notice a pattern? The reason for this is that *Prep* workflows are all built using a neuroimaging framework called [NiPype](https://nipype.readthedocs.io/en/latest/index.html); advanced users are encouraged to check it out.
