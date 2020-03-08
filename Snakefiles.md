---
layout: default
title: Snakefiles for the paper
nav_order: 1
description: "The Snakefiles for the Dyksma et. al. paper"
permalink: /Snakefiles
nav_exclude: true
---

# Snakefiles for the paper

This pages describes how to accquire the pipelines used in the workflow. All pipelines require conda and Snakemake.

``` sh
conda create -y -n snake -c bioconda -c conda-forge snakemake>=5.10 sra-tools
conda activate snake
```

## KnuttReads2Bins

Read analysis and binning.

``` sh
git clone -b paper https://github.com/KnuttPipeline/KnuttReads2Bins.git
cd KnuttReads2Bins
download_sra(){
  fastq-dump --split-files -O input $1 --gzip
  mv input/${1}_1.fastq.gz input/${2}_R1.fastq.gz
  mv input/${1}_2.fastq.gz input/${2}_R2.fastq.gz
}
download_sra SRR11128853 ENR-Ac244days_meta
download_sra SRR11128854 ENR-Ac211days_meta
download_sra SRR11128855 PFL9_meta
snakemake -prj 16 --use-conda paper 2>&1 | tee run.log
# Run with 16 cores available
```

## KnuttBinAnno

Bin annotation.

``` sh
git clone -b paper https://github.com/KnuttPipeline/KnuttBinAnno.git
cd KnuttBinAnno

snakemake -prj 16 --use-conda paper 2>&1 | tee run.log
```

## KnuttBinPhylo

Automatic phylognetic marker analysis

Coming soon
{: .label .label-yellow }
