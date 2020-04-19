---
layout: default
title: Installation
nav_order: 2
permalink: /install
---
# Installation
{: .no_toc}

The pipelines only requires a working installation of **[Conda](https://docs.conda.io/en/latest/)** for Python 3, git  and [Snakemake](https://snakemake.readthedocs.io/en/stable/). This guide has been tested on a clean, **minimal** installation of Ubuntu 19.10 and Debian Buster and should work on *most* Linux distributions.

MacOS and the Windows 10 Linux subsystem haven't been tested.

Check your current setup with the following commands.

``` sh
conda --version # => 5.10.0
snakemake --version # => 4.8.2
```

---

#### Table of contents
{: .no_toc }

1. TOC
{:toc}

---

## System Requirements

They strongly depend on the executed steps of the pipeline. You should be fine with around *200 GB* of memory, *32 vCPUs* and *1.5 TB* of disk space for a few **medium sized** metagenomes.

Certain steps also can be run on rented **[AWS EC2](https://www.ec2instances.info/)** instances for example.

## Miniconda

To install the **Conda** distribution Miniconda follow the instructions on [this site](https://docs.conda.io/en/latest/miniconda.html).

Commands:

``` sh
wget -q https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
sh Miniconda3-latest-Linux-x86_64.sh
```

You will need to **accept** the licencse and select an installation path.

If you know **you accepted the licencse**, you can run a *non-interactive* installation like this:

``` sh
wget -q https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
sh Miniconda3-latest-Linux-x86_64.sh -b -p $HOME/miniconda3
# Activate base env
eval "$($HOME/miniconda3/bin/conda shell.bash hook)"
# Init for Shell functions & automatic base activation
conda init
```

Conda environment activation can take a while, slowing the start of a new shell/login. You can disable automatic activation with:

``` sh
# From the installer, just as a reminder
conda config --set auto_activate_base false
# Afterwards you can still just run
conda activate base
```

## Snakemake

**Snakemake** can be installed with the required conda distribution. Install it from the [Bioconda](https://bioconda.github.io/) and [conda-forge](https://conda-forge.org/#about) channel.

``` sh
# From the installer, just as a reminder
conda install -c bioconda -c conda-forge snakemake
```

You could also create an environment like this

``` sh
conda create -n snake -c bioconda -c conda-forge snakemake
# will be placed in `~/miniconda3/envs/snake`
conda activate snake
```

or like this

``` sh
conda create -p ./snake -c bioconda -c conda-forge snakemake
# will be placed in `snake`
conda activate ./snake
```

## Knutt

Clone the git repository for the pipeline you want to use:

``` sh
git clone https://github.com/KnuttPipeline/KnuttReads2Bins.git
git clone https://github.com/KnuttPipeline/KnuttBinAnno.git
git clone https://github.com/KnuttPipeline/KnuttBinPhylo.git
```

Now you can edit the **configuration** files (`config.yml`).