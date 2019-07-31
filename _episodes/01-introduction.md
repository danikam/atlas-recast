---
title: "Introduction"
teaching: 20
exercises: 0
questions:
- "How can RECAST help me make the most of my analysis?"
- "How do docker and gitlab work together to preserve your analysis code and operating environment?"
- "What is needed to fully automate and preserve the analysis workflow?"

objectives:
- "Understand the motivation for incorporating RECAST into your analysis"
- "Familiarize yourself with the three key components of RECAST"
keypoints:
- "RECAST preserves your analysis code and operating environment so it can be re-interpreted with new physical models."
- "Gitlab enables full preservation of your analysis code."
- "Docker makes it possible to preserve the exact environment in which your analysis code is run."
- "Your analysis workflow definition automates the process of passing an arbitrary signal model through your containerized analysis chain."
---

## Introduction
To understand why analysis preservation, and RECAST in particular, is useful, consider that it can take months or even years for a multi-person analysis team to develop cuts that optimally carve out a phase space sensitive to the model they want to test, and estimate the standard model backgrounds and systematics in this phase space.


Years later, other physicists may dream up new theories leading to alternative models that would show up in the same or similar phase space. Since the cuts and standard model background estimates won't be affected by considering different signal models in the same phase space, it would probably be way faster for them just to make a few tweaks to the original analysis and re-run it with the new models. But the original analysts have moved on, and even if they can dig up the analysis code, they may not remember exactly how to use it or what sort of environment they were running it in.


This is where RECAST comes in! RECAST automates the process of passing a new signal model through an analysis at the time that the analysis is being developed so that it can be trivially reused at any time in the future to re-interpret new signal models in the phase space that it so painstakingly revealed.

## Three Key Components of RECAST

Analysts write a custom code framework that they pass data and signal/background MC through to search for a particular physics model in the data. The first part of analysis preservation is therefore to preserve this code framework. Gitlab is great for this. It fully preserves not only the final version of the code, but also every single version and offshoot of the code that was ever used by the analysts. 


But as we've seen, the code framework is far from standalone. It relies on having specific libraries, compilers, and even operating systems in place, and it may be really fussy about the exact versions of all these dependencies. So the second part of analysis preservation is to capture the exact environment in which the code was run by the original analysts. This is where docker comes in.


ATLAS has developed [version-controlled docker base images](https://hub.docker.com/u/atlas) that encapsulate the OS, compilers, standard libraries, and ATLAS-specific dependencies commonly used in ATLAS analyses. You've already had a chance to work with one of them, `atlas/analysisbase:21.2.75`. Individual searches design custom Dockerfiles in the Github repo(s) to add the analysis code to the base image, along with any analysis-specific dependencies, and then build the code to produce the exact environment needed to run it.


The third and final piece of analysis preservation is to fully automate the steps that an analyst would go through in order to pass a new signal model through the analysis chain to arrive at the new result. This is accomplished using a tool called yadage, which allows the user to codify each step of the analysis, which analysis environment (i.e. container) a given step needs to run in, and how each step fits into the overall analysis workflow to arrive at the final result.

> ## Other RECAST resources
> In addition to this tutorial, there are many other resources available for RECAST, including:
>
> ### RECAST documentation
> * Excellent [RECAST documentation](https://recast-docs.web.cern.ch/recast-docs/) written by Lukas Heinrich, which is borrowed from extensively for this tutorial.
> 
> ### Slides from past tutorials
> * [Exotics Tutorial 2018-11-19](https://indico.cern.ch/event/771214/)
> * [Exotics Tutorial 2018-11-12](https://indico.cern.ch/event/768607/)
> * [SUSY Tutorial 2018-11-08](https://indico.cern.ch/event/763748/)
> * [Docker Tutorial Induction Week 2019-01-31](https://indico.cern.ch/event/772589/contributions/3210539/attachments/1788754/2913246/DockerSWTutorial.pdf)
> * [GitLab Tutorial Induction Week 2019-01-31](https://indico.cern.ch/event/772589/contributions/3210424/attachments/1788725/2913191/20190131_ContinuousIntegration_ATLASInductionWeek.pdf)
> 
> ### Communication channels
> * [Mattermost Channel](https://mattermost.web.cern.ch/atlas-ap/channels/town-square)
> * **Mailing list:** atlas-phys-exotics-recast@cern.ch
> * [Discourse](https://atlas-talk.web.cern.ch/c/recast)
>
> ### Talks
> [ATLAS Weekly Talk on Analysis Preservation](https://indico.cern.ch/event/793316/contributions/3295488/attachments/1786861/2909640/AP_RECAST_Weekly.pdf)
{: .callout}

{% include links.md %}

