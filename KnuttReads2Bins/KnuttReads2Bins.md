---
layout: default
title: KnuttReads2Bins
nav_order: 3
permalink: /KnuttReads2Bins
has_children: true
has_toc: true
---
# KnuttReads2Bins
{: .no_toc}

[**KnuttReads2Bins**](https://github.com/KnuttPipeline/KnuttReads2Bins) performs read analysis by performing [Kaiju](http://kaiju.binf.ku.dk/), [SSU (SINA)](https://sina.readthedocs.io/en/latest/) and [Sourmash](https://sourmash.readthedocs.io/en/latest/) classification as well as [DIAMOND BLASTX](http://www.diamondsearch.org/) searches.

Futhermore it automatically performs **assembly** and **binning** using [MEGAHIT](https://github.com/voutcn/megahit) and [MetaBAT2](https://bitbucket.org/berkeleylab/metabat/). The resulting bins and contigs are also classified and analyzed using [Sourmash](https://sourmash.readthedocs.io/en/latest/), [CheckM](https://ecogenomics.github.io/CheckM/), [metaQUAST](http://quast.sourceforge.net/metaquast) and [CAT/BAT](https://github.com/dutilh/CAT). 

Data from every tool is parsed and combined into `.tsv` files. Those files are used for one-file dashboards on every step comparing provided samples.

## Quickstart

1. Place your paired read files in the `input/` folder.
2. Check the `config.yml`
3. Use the following schema for your read files: `SAMPLE_R1.fastq.gz`
4. Run the whole workflow with:

``` sh
snakemake -kpj 32 --use-conda all allReport 2>&1 | tee run.log & disown
```

**32** is the number of vCPUs to use during the run.
