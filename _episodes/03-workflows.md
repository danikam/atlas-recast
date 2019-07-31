---
title: "Workflows"
teaching: 20
exercises: 0
questions:
- "FIXME"
- 
objectives:
- "FIXME"

---

## Introduction

If it weren't for the docker containers used in RECAST, this could conceivably be accomplished with some environment variables and bash scripts that just list out each command that an analyst would type into the terminal while going through the analysis. But when we perform the analysis steps in one or more docker containers, we need a way to codify what needs to happen in which container for each step, and how the output from one step feeds as the input for later steps, which may in general need to be done in different containers. Lukas Heinrich has written an awesome tool called yadage to handle this situation. 

~~~
## Background preservation
Recall that a real ATLAS analysis will have background processes that fall into the same phase space region as the signal you're interested in. 
~~~
{: .callout}

## 

{% include links.md %}

