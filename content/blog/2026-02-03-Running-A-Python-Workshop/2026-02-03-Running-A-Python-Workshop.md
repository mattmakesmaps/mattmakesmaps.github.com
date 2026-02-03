---
title: Run a Geospatial Python Workshop Like It's 2026
draft: false
description: Tips and Tricks on Hosting a Geospatial Python Workshop
date: 2026-02-03
tags:
    - GIS
    - python
    - TNC
    - Nature Conservancy
---

The start to 2026 has given me the opportunity to help draft a Python workshop for our 
global science staff at The Nature Conservancy. I'll be running the 
[Intermediate Module](https://github.com/TNC-Geospatial-Conservation-Tech/ocs-training-2026-intermediate-mybinder),
demoing workflows for accessing GIS data stored in the cloud, either through raw object store,
or through services such as OGC API Feature/OGC API Tiles. I'll also be demoing
data search workflows using STAC.

I'm incredibly proud of my team, my personal contributions, and the participants who are
interested in learning about Cloud Native Geospatial workflows.

These demos will be run as Python Notebooks. Taking a step back, below are my practical notes
on creating a workshop environment that allows for both interactive execution of
these notebooks, backup plans, and some prerequisite logistical questions to consider.

## JupyterLab Universe

Running Python notebooks in the cloud means interacting with the [Project Jupyter](https://jupyter.org/)
suite of tools. Primarily, this refers to:

- [JupyterLab](https://jupyterlab.readthedocs.io/en/latest/#) environment for authoring and
executing python notebooks.
- [binder](https://mybinder.org/) which consumes a public github repo of notebooks and provides
a small ephemeral compute environments running JupyterLab for interactive execution.
- [nbviewer](https://nbviewer.org/) is a separate service for beautifully rendering non-executable
versions of existing notebooks, useful for sharing.
- [nbconvert](https://nbconvert.readthedocs.io/en/latest/#) a command-line utility for converting notebooks
to other formats. Useful for conversion to a static site served by [github pages](https://docs.github.com/en/pages).

These tools, to be honest, are incredible. They provide a tightly coupled system for authoring, hosting,
and sharing python notebooks. Geospatial Python in 2026 is undeniably _open_ and undeniably _cool_.

## My Ideal Workflow

Although I didn't follow this workflow this time around, this is what I'll be doing next time.

0) **Use [binder](https://mybinder.org/)** to allow participants to interact with and execute notebook cells.
As of 02/2026 it seems to be very stable. The launcher badge that is generated can be placed directly
into your repo's README.
1) **Draft workbooks outside of binder, but continously check your python environment's compatibility with binder:** Author notebooks in a local jupyterlab or alternative cloud-compute environment, but
make sure to periodically check that your `binder`'s `environment.yml` file can still be used to successfully
spin up an instance. In my experience, I had to drop down dependencies such as `python==3.11` or `rasterio==1.3` in order to have the environment successfully spin up before a timeout error is thrown. Additional notes
on optimizing your environment for binder can be found [here](https://discourse.jupyter.org/t/how-to-reduce-mybinder-org-repository-startup-time/4956)) **Anytime you add a new dependency, check that your environment still launches in binder.**
2) **Use [nbviewer](https://nbviewer.org/) or [nbconvert](https://nbconvert.readthedocs.io/en/latest/#) to render static webpages from your notebooks:** Github's preview functionality does an "OK" job of
rendering `.ipynb` files, but for things like interactive slippy maps, it falls flat. In order to correctly display these and other interactive components, use one of these tools. These also serve as great backups
and alternatives, per note at bottom of this post.

## Logistical Notes

Although not always possible, here are some aspirational thoughts on logistics/planning.

### Make a decision about participant count early

When planning, decide on whether or not to implement a participant cap early on.

The number of participants has downstream impacts. Determining whether or not to cap participation
can affect things like:

- What the workshop compute environment is (local workstations, cloud platform `A` vs `B`)
- What the workshop storage environment is (local storage, JupyterLab local storage, cloud storage)
- Your ability to provide real-time support during a workshop
- Your need to generate backup plans in the event of compute capacity issues. (Notebook Webpages)

### Pick a compute and storage environment early

Although the promise of Cloud-based compute is to provide a consistent, reproducible environment,
no longer subject to the `Sorry... It works on my computer` trap, the reality is not necessarily
the case.

In my experience, I ported my notebooks between three different JupyterLab hosting implementations:
[binder](https://mybinder.org/), [SageMaker Studio Lab](https://studiolab.sagemaker.aws/), and
an internal SageMaker deployment. Each platform had their own integration issues, requiring subtle
changes to be made to the structure of my notebooks and python dependencies. Choosing the compute
environment once and sticking to it reduces churn and wasted effort.

Some integration challenges were:
- Binder: Optimizing Python dependencies and repo structure to [reduce startup time and eliminate blocking "spawn errors"](https://discourse.jupyter.org/t/how-to-reduce-mybinder-org-repository-startup-time/4956)
- SageMaker Studio Lab: Finding alternative map visualization extensions to mitigate [platform-specific loading issues](https://github.com/jupyter-widgets/ipyleaflet/issues/1034)
- Object Storage Policies: Adjusting bucket policies to integrate with each platform.

### Have a backup plan (or two)

With live demos, stuff happens... guaranteed. Third party services may go down. Globally distributed
participants may run into unforeseen network issues. Participants who are unfamiliar with a notebook
interface may just prefer to follow along rather then modify and execute notebook cells.

Binder seems to be working great, but there is always a risk that it might go down day of presentation.

As a backup/alternative, static workbooks can be viewed using `nbviewer`. However, `nbviewer` itself is
[subject to rate limiting by Github](https://github.com/jupyter/nbviewer/issues?q=503%20sort%3Acreated-desc),
and in my experience has been unable to render notebooks.

Backup to the backup? Use `nbconvert` to create a static site [hosted on github pages](https://tnc-geospatial-conservation-tech.github.io/ocs-training-2026-intermediate-mybinder/).