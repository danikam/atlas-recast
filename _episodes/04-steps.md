---
title: "Writing Your Analysis Steps"
teaching: 20
exercises: 40
questions:
- "FIXME"
- 
objectives:
- "FIXME"
keypoints:
- "FIXME"
---

## Introduction

Now that you have an idea of how the analysis workflow is reproduced by connecting containerized steps, it's time to dive into the gory details of actually encoding them in a steps.yml file, and adding tests in your recast.yml file to develop and validate each step. You can then try writing and validating your own steps.yml file for our VHbb analysis. 

## Overview of writing steps and tests
[FIXME: discuss the technical details of what goes into defining an analysis step, writing a test for each step, and running the tests with the recast run command=-line tool]

> ## Exercise
> Working from your shell, cd out of your analysis repo directory, and clone a git repo containing a skeleton of our RECAST setup that we'll fill in as we go along:
> ~~~
> git clone https://gitlab.cern.ch/damacdon/usatlas-bootcamp-recast.git   # FIXME: actually, this repo currently contains the full version. Need to turn it into a skeleton. 
> ~~~
> {: .source}
> [FIXME: list `recast run` commands to initialize and set needed environment variables]
> cd into the `usatlas-bootcamp-recast` directory, and open the recast.yml and specs/steps.yml file with a text editor. Fill in the FIXMEs in `specs/steps.yml` with the steps needed to perform the analysis. 
>
> At any point, you can test the steps you've written by running
> ~~~
> recast tests shell bootcamp/vhbb [name of test, eg. test_intro] --backend docker
> ~~~
> {: .source}
> > ## Solution
> > zzz put finalized version of solution here
> {: .solution}
{: .challenge}




{% include links.md %}

