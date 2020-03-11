---
layout: default
title: Running locally
nav_order: 1
permalink: /run/local
parent: Executing
has_toc: false
---
## Running locally
{: .no_toc}

This section will focus on **local and general** options.

---

#### Table of contents
{: .no_toc }

1. TOC
{:toc}

---

### Run Snakemake, Run

A typical call to run the `data` rule may look like this:

``` sh
snakemake --use-conda  --verbose -kprj --res mem_mb=32000 --ri data -n
```

| Short Option        | Long Option          | Explanation                                                                                |
|:-------------------:|:--------------------:|:-------------------------------------------------------------------------------------------|
|                     | `--use-conda`        | Create Conda envs automatically in `.snakemake/conda`                                      |
|                     | `--verbose`          | Print detailed stack traces and detailed operations                                        |
| `-k`                | `--keep-going`       | If a job fails, continue with independent jobs                                             |
| `-p`                | `--printshellcmds`   |                                                                                            |
| `-r`                | `--reason`           | Print the reason for rule execution (Missing output, updated input etc.)                   |
| `-j`                | `--cores`            | Number of CPU cores (threads) to use for this run. With no int, it uses all. Default is 1. |
| `--res`             | `--resources`        | Key value pair for resources <br> `mem_mb`: Memory in Megabyte.                            |
| `--ri`              | `--rerun-incomplete` | If Snakemake marked a file as incomplete after a crash, delete and produce it again        |
| `-n`                | `--dryrun`           | Just pretend to run the workflow. A similar option is `-S`  (`--summary`)                  |

### Crashed and burned (Unlocking)

After the workflow was **killed** (Snakemake didn't shutdown), the workflow directory will be still **locked**. If you are sure, that snakemake is no longer running (`ps aux | grep snake`).

Unlock the working directory:

``` sh
snakemake data --unlock
```

### Influence rule execution

If you don't want to delete files marked as **temporary** in the workflow use the `--notemp` (`--nt`) flag. After copying files, modifications date may be altered and Snakemake tries to **recreate** files. By running Snakemake with the `--touch` (`-t`)  option, exisitng files will be marked with the current date as current.

If you want to force the recreation of a certain file, use one of the force flags.

| Short Option        | Long Option            | Explanation                                                     |
|:-------------------:|:----------------------:|:----------------------------------------------------------------|
| `-f`                | `--force`              | Force execution of the given files or rules                     |
| `-F`                | `--forceall`           | Force execution of the given files or rules and preceding rules |
| `-R`                | `--forcerules`         | Force execution of the given files or rules and depending rules |
