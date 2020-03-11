---
layout: default
title: Using a cluster
nav_order: 3
permalink: /run/cluster
parent: Executing
has_toc: false
---
## Using a cluster
{: .no_toc}

This section will focus on using a **HPC cluster** with a `qsub` interface like [SGE](http://gridscheduler.sourceforge.net/) and/or a [DRMAAv1](https://en.wikipedia.org/wiki/DRMAA) implemention like [Slurm](https://slurm.schedmd.com/).

The workflow has to be in a folder which is **available on all nodes**, like a NFS.

### Comand line

You can use all flags described in the [local section](/run/local). The `-j` flag now instead gives the number of jobs to run on the cluster at the same time. Rules marked as `localrule` still run on the machine executing snakemake.

``` sh
snakemake --use-conda --cluster "qsub -t {threads} -l mem={resources.mem_mb}mb" -j 256 -kpr --ri data
```

### Profiles

To reduce the length of your Snakemake calls, you can save common configurations as **profiles**.

Every profile is a folder with a `config.yaml` in `~/.config/snakemake` or `/etc/xdg/snakemake/`

Content of `~/.config/snakemake/cluster/config.yaml`:

``` yaml
cluster: "qsub -t {threads} -l mem={resources.mem_mb}mb"
jobs: 256
keep-going: true
printshellcmds: true
use-conda: true
reason: true
rerun-incomplete: true
```

This is the same call as the one shown above:

``` sh
snakemake --profile cluster data
```

Per Unneberg created a [profile cookiecutter for **Slurm**](https://github.com/Snakemake-Profiles/slurm). 
