---
layout: default
title: Data Analyst Training
parent: Onboarding
grand_parent: Lab Basics
has_toc: true
---

# Informatics Onboarding
{: .no_toc}

The Informatics team plays a significant role in the scientific engine. This page outlines the onboarding process for informatics work, and is particularly geared towards new data analysts. The goal of this section is to provide you with resources necessary to develop the **core competencies** for informatics at PennLINC. Note that there is no expectation that one develops *mastery* of *all* of the competencies below — just that one is familiar with how to go about accomplishing these tasks (or at least one can google one's way through it).

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

# Core Competencies
{: .no_toc}

The core competencies of informatics are split into 3 major units:

1. [Programming & Software Tools](#unit-1-programming--software-tools)

2. [Data Management, Curation, & Pipelines](#unit-2-data-management-curation--pipelines)

3. Hypothesis Testing & Analysis (Coming Soon!)

The units are designed around the day-to-day tasks of Informatics team members, with different tasks employing a number of competencies from each of these units. In a perfect world, Informatics team members are experts in all 3 units, but in practice, your eventual expertise will depend on your assigned duties, past experience, interests, and desire to learn.

In the sections below, we outline each major competency by asking simple, task-based questions, and provide our best recommended resource for learning each of the competencies, as well as a glossary of secondary resources. There is an estimated time to complete these, but don't feel locked into these time frames. If you can confidently answer the questions yourself, you likely have developed that competancy before — feel free to briefly skim the resource.

## Unit 1: Programming & Software Tools

Being a competent programmer is fundamental to good neuroscience. It's important to remember that languages come and go — understanding the principles of programming is more important than spending hours memorising one language's idiosyncrasies.

### The Command Line

Working from the command line is a **must**, due in no small part to the fact that we spend a large amount of time on computing clusters like [PMACS](/docs/pmacs) and [CUBIC](/docs/cubic). Working on the command line is one of those skills that benefits you more the better you get at it -- don't feel discouraged if it feels slow at first.

> Q: How would you create a directory from the command line, move files into it, and loop over its contents?

> Q: How would you record the visual text output of a program in a file?

> Q: How would you make sure your shell always loads with the correct paths to programs set automatically?

We recommend RyansTutorials.net (the [Linux](https://ryanstutorials.net/linuxtutorial/) and [bash scripting](https://ryanstutorials.net/bash-scripting-tutorial/) sections) for this competency (3-5 hours).

Other resources:

- [w3 schools](https://www.w3resource.com/linux-system-administration/linux-commands-introduction.php)


### Python

Python is a powerful all-purpose language for data science. It is particularly popular in neuroimaging due to its low skill floor and high skill ceiling/extensibility — you can do anything from adding two numbers together, to building deep learning neuroimaging pipelines.

> Q: What are the basic data structures in Python programming? How do they differ? What scenarios are appropriate for using one particular data structure in comparison to others?

> Q: How would you read in a table in Python? How would you loop over the rows in a table?

> Q: What is a `class`? How would you list all of the available methods of a `class`? What's the difference between a `class`' method and a `class`' attribute?

> Q: How would you run a Python program? How would you check for errors? What is a `traceback`?

We recommend [Coursera](https://www.coursera.org/learn/python-crash-course#syllabus) for this part (> 8 hours), although Python is a *very* common language with loads of open resources available:

- [RealPython](https://realpython.com/start-here/)
- [Codecademy](https://www.codecademy.com/learn/learn-python-3)
- [A handy lookup repo](https://github.com/rasbt/python_reference)


### R

R is a similarly popular data science language, but tends to favour a slightly narrower use case: statistical and numerical analysis. While it is well-equipped to handle myriad programming tasks, R really shines once you want to work on tabular data, run statistics on experiments, plot beautiful graphs and figures, and produce high-quality reports. R is the go-to language for the statistics portion of any scientific endeavour.

> Q: How would you simulate a random sampling of numbers? How would you plot that sample in a histogram?

> Q: What is a Dataframe? How would you create one from a CSV file?

> Q: What "tidy" data? The Tidyverse? What are "verbs" in tidy R parlance?

> Bonus: [Why is R so weird?](https://twitter.com/WhyDoesR)

We recommend *either* Roger Peng's [R Programming for Data Science](https://bookdown.org/rdpeng/rprogdatascience/), *or* [R4DS](https://r4ds.had.co.nz/) by Hadley Wickham. Both are long, so for the sake of time, focus on sections 1 through 15 of Peng, and sections 1 through 21 of Wickham (> 8 hours). Getting through the entirety of either book, however, will be well worth your while if you want to be very familiar with R.

Other resources:

- [Swirl](https://swirlstats.com/students.html), an interactive prompt-based learning package
- [learnR4free](https://www.learnr4free.com/index.html) and [this blog](https://livefreeordichotomize.com/2020/07/02/so-you-want-to-learn-r/) collate a number of external resources

### Git & Github

Version control with Git and Github is essential for managing reproducible science. The Informatics team handles all projects using git, and so fluency with basic git commands and familiarity with a Github-based workflow are daily requirements.

> Q: What is version control? What kinds of files are appropriate for version controlling software?

> Q: What is the difference between a `commit` and a `push`?

> Q: What is a `branch`? How could one use a `branch` to manage different ideas/experiments in code/analysis?

While git is essential, it doesn't take very long to learn, and the basics quickly become second nature. We recommend [this](https://www.youtube.com/watch?v=SWYqp7iY_Tc) Github crash course (1 hour).

Other resources:

- [HappyGitWithR](https://happygitwithr.com/)

### Miscellaneous

This section mentions just a few other technologies that are not essential for getting started, but will become necessary as you work on more complex projects. Google these or skim the links to learn more.

- [Containerization with Docker](https://ropenscilabs.github.io/r-docker-tutorial/01-what-and-why.html) is used frequently to share reproducible software
- [Jupyter](https://realpython.com/jupyter-notebook-introduction/) and [Rmarkdown]() for creating reproducible notebooks


## Unit 2: Data Management, Curation, & Pipelines

This section introduces the basic data types in neuroimaging, how they are stored, and what software pipelines are frequently used for preprocessing. These competencies are particularly important for for the data analyst role.

### Introduction to MRI Modalities

A good foundation in the basic physics of MRI, and how the physics extends to different *modalities* of MRI, is essential:

> Q: What is magnetic resonance imaging? What is the BOLD signal?

> Q: What is the difference between a structural & a functional image?

> Q: What is diffusion in MRI? What is ASL?

We recommend [MRI physics with Dylan Tisdal](https://www.youtube.com/watch?v=SwsH64PBBZE) (< 1 hour).

Other resources:

- MRI Physics series with [Geoff Aguirre](https://www.youtube.com/channel/UCQ6zwRPkmDrZj9N6Inl8UZw)
- Diffusion imaging from [Albert Einstein College of Medicine](https://www.youtube.com/watch?v=dW8Yh-c2xVY&ab_channel=AlbertEinsteinCollegeofMedicine)
- [AndysBrainBook](https://andysbrainbook.readthedocs.io/en/latest/index.html)

### BIDS

Brain Imaging Data Structure is a data storage standard for neuroimaging. It describes how best to format and store neuroimaging data (and why it's important to do so). PennLINC expects all of its data, both internal and publicly shared, to be in BIDS format — much of the work of the Informatics team is making sure this goal is being met.

> Q: What are the benefits of using BIDS?

> Q: What modalities are currently covered in BIDS? What happens if the data you have isn't covered in BIDS?

We recommend learning about BIDS straight from the source: the [official BIDS specification](https://bids-specification.readthedocs.io/en/stable/).

Other resources:

- [AndysBrainBook](https://andysbrainbook.readthedocs.io/en/latest/OpenScience/OS/BIDS_Overview.html)

### Data Warehousing with Flywheel

Neuroimaging produces vast amounts of data. It's not practical to expect large projects to store and access data off of one hard disk drive, or even one cluster. At a certain point, it becomes necessary to leverage cloud computing to store and access data, and much of the engineering of this method can be outsourced to professional engineering companies. PennLINC houses much of its data on the Flywheel platform. Familiarity with Flywheel is essential for data analysts' work.

> Q: How is data in Flywheel structured? What are the relationships between different `containers`?

> Q: How would you view a particular scan, given the session and subject labels?

> Q:
