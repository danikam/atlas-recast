---
title: "Gitlab CI and Docker for Environment Preservation"
teaching: 30
exercises: 30
questions:
- "How does gitlab CI/CD help me continuously keep my containerized analysis environment(s) up-to-date?"
- "What do I need to add to my gitlab repo(s) to enable this functionality?"
objectives:
- "Learn how to write a Dockerfile to containerize your analysis code and environment."
- "Understand what needs to be added to your `.gitlab-ci.yml` file to keep the containerized environment continuously up to date for your repo."
keypoints:
- "gitlab CI/CD can be used to keep your analysis environment up-to-date by re-building a container that encapsulates the environment each time new commits are pushed to the repo."
- "This functionality is enabled in gitlab CI/CD by adding a Dockerfile to your repo that specifies how to build the environment, and a container-building stage to the .gitlab-ci.yml file."
---

## Introduction
Gitlab CI/CD automates the task of keeping your analysis environment up-to-date so you don't have to think about it (much). This is accomplished by re-building the container in which your analysis environment is preserved for each analysis repo each time that new commits are pushed to the repos. 


## RECAST and Gitlab CI/CD

So how is this automated container re-building actually accomplished? As you've already learned, each time that an analyst pushes new commits to the docker repo with gitlab CI/CD set up, gitlab starts a pipeline that runs the tests you wrote in your `.gitlab-ci.yml` file to validate the new commits. In addition to these code tests, it's also possible to add code to the `.gitlab-ci.yml` file to build a container in which the analysis code and environment reside, and store it in the "gitlab registry". But to build this container correctly, you first need some specific instructions on:
 * how to set up the exact environment in the container that your code depends on,
 * how to add your analysis code to the container, and 
 * how to pre-build the code so that it can be just run trivially inside the container (ideally with only a single command).

These all sound like tasks that a Dockerfile would be great for! And indeed, the first key component of automated environment preservation with gitlab CI/CD is to add a Dockerfile to the repo with these specifications.  

> ## `atlas` User in ATLAS Containers
> By default, files added to a docker container will be owned by root user. However, it's considered good practice to avoid actually working as root user unnecessarily since this minimizes the risk of accidentally misusing root privileges [FIXME: Lukas, is this the main reason?], so the analysisbase images use a non-root `atlas` user by default. 
> 
> Because of this, if we don't explicitly make the directory containing your analysis code owned by `atlas` user, you may run into permission issues when your code tries to run (as atlas user) and create new files and plots, etc. inside the directory.
{: .callout}

### Writing your Gitlab Dockerfile

So far, you've been starting your containers from the atlas/analysisbase:21.2.75 base image, volume-mounting your analysis code, and building the code manually, maybe with the help of some aliases and shell scripts [FIXME: will this statement be accurate??]. Now we're going to write a Dockerfile that automatically adds your code to the container and builds it, then bundles all this into a new container that's ready to run your code!


> ## Exercise
> Working from your shell, cd into the top level of your gitlab repo for the VHbb analysis. Create an empty file named Dockerfile
>
> ~~~
> touch Dockerfile
> ~~~
> {: .source}
> 
> Now open the Dockerfile with a text editor and, starting with the following skeleton, fill in the FIXMEs to make a Dockerfile that builds your analysis environment. 
> 
> ~~~
> # Specify the image from which you are working
> [FIXME]
> 
> # Put the current repo (the one in which this Dockerfile resides) in the /Bootcamp directory
> # Note that this directory is created on the fly and does not need to reside in the repo already
> [FIXME] 
> 
> # Go into the /Bootcamp/build directory (again, it will create the directory if it doesn't already exist)
> [FIXME]
> 
> # Source the ATLAS analysis environment
> # Make sure the directory containing your analysis code (and the code inside it) is owned by atlas user
> # Build your source code using cmake
> RUN [FIXME: source the ATLAS analysis environment] && \
>     sudo chown -R [FIXME: complete this command so that the directory containing the analysis code, and all the code inside, is owned by atlas user] && \
>     [FIXME: cmake setup] && \
>     [FIXME: build the code]
> ~~~
> {: .source}
>
> > ## Solution
> > ~~~
> > # Specify the image from which you are working
> > FROM atlas/analysisbase:21.2.75
> > 
> > # Put the current repo (the one in which this Dockerfile resides) in the directory specified here
> > # Note that this directory is created on the fly and does not need to reside in the repo already
> > ADD . /Bootcamp/
> > 
> > # Go into the directory specified here (again, it will create the directory if it doesn't already exist)
> > WORKDIR /Bootcamp/build
> > 
> > # Source the ATLAS analysis environment
> > # Make sure the directory containing your analysis code (and the code inside it) is owned by atlas user
> > # Build your source code using cmake
> > RUN source ~/release_setup.sh &&  \
> >     sudo chown -R atlas /Bootcamp && \
> >     cmake ../source && \
> >     make
> > ~~~
> > {: .source}
> {: .solution}
> 
> Once you're happy with your Dockerfile, you can commit it to your repo and push it to github.
{: .challenge}


> ## Hints
> As you're working, you can test whether the Dockerfile builds successfully using the `docker build` command. Eg.
> ~~~
> docker build -t vhbb_test .
> ~~~
> {: .source}
> 
> When your container builds successfully, you can `exec` into it and poke around to make sure it's set up exactly as you want, and that you can successfully run the executable you built:
> ~~~
> docker run -it --rm vhbb_test bash
> ~~~
> {: .source}
{: .callout}

> ## Is any upkeep needed after I've added the Dockerfile to the repo?
> While gitlab CI/CD can do most of the heavy lifting in terms of automatically keeping your analysis environment up-to-date, you may want to review your Dockerfile(s) every now and then as your analysis progresses to ensure that:
> * the base image release is up-to-date with the release currently being used by analysts
> * If new functionality has been added to the code, any corresponding new executable(s) are being properly built.
{: .callout}

Now that you've added a Dockerfile to your gitlab repo, you can proceed with updating your `.gitlab-ci.yml` to actually build the container during the CI/CD pipeline and store it in the gitlab registry. You can later pull it from the gitlab registry just as you would any other container, but in this case using your CERN credentials. 


So, if there isn't yet code in your `.gitlab-ci.yml` file to build the container, you can add a build stage under the `stages` section

~~~
stages:
  - build
~~~
{: .source}

Next, add the following lines somewhere underneath to build the image and save it to the docker registry. 

~~~
build_image:
  stage: build
  variables:
    TO: $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_NAME
  tags:
    - docker-image-build
  script:
    - ignore
~~~
{: .source}

Once this is done, you can commit and push the updated .gitlab-ci.yml file to your gitlab repo and check to make sure the pipeline passed. If it passed, the repo image built by the pipeline should now be stored on the docker registry, and accessible as follows:

~~~
docker login gitlab-registry.cern.ch
docker pull gitlab-registry.cern.ch/[repo owner]/[repo name]:[branch name]
~~~
{: .source}

Notice that the script to run is just a dummy 'ignore' command. This is because using the docker-image-build tag, the jobs always land on special runners that are managed by CERN IT which run a custom script in the background. You can safely ignore the details.

> ## Recommended Tag Structure
> You'll notice the environment variable `TO` in the `.gitlab-ci.yml` script above. This controls the name of the Docker image that is produced in the CI step. Here, the image name will be `<reponame>:<branch or tagname>`. This way images built from different branches do not overwrite each other and tagged commits will correspond to tagged images.
{: .callout} 

{% include links.md %}

