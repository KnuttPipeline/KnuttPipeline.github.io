---
layout: default
title: Shared File Formats
nav_order: 4
permalink: /shared_files
---
# Shared files
{: .no_toc}

Certain file formats occur multiple times in one pipeline or are shared across pipelines. 

---

#### Table of contents
{: .no_toc }

1. TOC
{:toc}

---

## Benchmark files

These files are produced with Snakemakes [`benchmark` directive](https://snakemake.readthedocs.io/en/stable/snakefiles/rules.html#benchmark-rules). The memory values are recorded with the [`psutil` library](https://psutil.readthedocs.io/en/latest/).

Source: [StackOverflow - heathobrien](https://stackoverflow.com/a/47201241), [StackOverflow - Victor S](https://stackoverflow.com/questions/22372960/is-this-explanation-about-vss-rss-pss-uss-accurate)

<dl>
  <dt><em>s</em></dt>
  <dd>Summed duration (seconds) a CPU core was used</dd>
  <dt><em>h:m:s</em></dt>
  <dd>Same as <em>s</em></dd>
  <dt><em>max_rss</em></dt>
  <dd>Resident set size. Actual memory stored in RAM the process used. Includes shared memory.</dd>
  <dt><em>max_vms</em></dt>
  <dd>Virtual memory size. The process may have allocated memory, but never actually stored something in it or mapped it to something else. Includes shared memory.</dd>
  <dt><em>max_uss</em></dt>
  <dd>Unique set size. RAM actually used by the process. Doesn't include shared memory.</dd>
  <dt><em>max_pss</em></dt>
  <dd>Proportional set size. USS together with shared memory. The shared memory is proportionally distributed among its processes.</dd>
  <dt><em>io_in</em></dt>
  <dd>Megabytes read from disk</dd>
  <dt><em>io_out</em></dt>
  <dd>Megabytes written to disk</dd>
  <dt><em>mean_load</em></dt>
  <dd><em>s</em> divided by the run time multiplied with 100. </dd>
</dl>

The memory properties are reported as in megabytes and are summed across all subprocesses. Only the highest value at any point is reported. Use PSS to predict memory the rule may use again in the future, as it includes the memory shared across subprocesses.

## Sequence data files

For KnuttReadsBins read preparation steps multiple FASTQ files are produced. Certain statistics of the sequences and their PHRED score values are reused in the reports. Two files can be generated for every sample and FASTQ file.

### Overview files

One line represents one FASTQ file.

<dl>
  <dt><em>read</em></dt>
  <dd>Read type of the sequences (R1, R2, merged)</dd>
  <dt><em>minReadLen</em></dt>
  <dd>Shortest sequence length in the file</dd>
  <dt><em>quantile25ReadLen</em></dt>
  <dd>25th percentile of all read lengths</dd>
  <dt><em>medianReadLen</em></dt>
  <dd>50th percentile of all read length</dd>
  <dt><em>quantile75ReadLen</em></dt>
  <dd>75th percentile of all read length</dd>
  <dt><em>maxReadLen</em></dt>
  <dd>Longest sequence length</dd>
  <dt><em>meanReadLen</em></dt>
  <dd>Average read length</dd>
  <dt><em>reads</em></dt>
  <dd>Number of reads in the file</dd>
  <dt><em>basepairs</em></dt>
  <dd>Number of basepairs in the file</dd>
</dl>

### Plot data files

These files are a little bit more complex, as they contain multiple datasets meant for direct x,y,(z) plots.

<dl>
  <dt><em>read</em></dt>
  <dd>Read type of the sequences (R1, R2, merged)</dd>
  <dt><em>type</em></dt>
  <dd>Dataset this points belong to</dd>
  <dt><em>x</em></dt>
  <dd>x value</dd>
  <dt><em>y</em></dt>
  <dd>y value</dd>
  <dt><em>z</em></dt>
  <dd>z value, not present for all datasets</dd>
</dl>

| Dataset | Description | x | y | z |
|--------------------|-------------------------------------|------------------|------------------|------------------|
| gccontent_density | Read GC content distribution | Read GC content | Density estimate | NA |
| avg_seq_qual | Mean read PHRED score distribution | Mean PHRED score | Density estimate | NA |
| cycle_qual_counts | Occurence table of the PHRED scores | Cycle/Position | PHRED score | Occurrence count |
| readlength_density | Read length distribution | Read length | Density estimate | NA |

The density estimates are calculated with [Rs `density`](https://stat.ethz.ch/R-manual/R-devel/library/stats/html/density.html) with 512 points between the smallest and biggest value(`from,to`). The default parameters would estimate values outside this range.

## Sequence data comparison files

The impact of steps like adapter and quality trimming can be important to know. KnuttReads2Bins produces such files given information on quality and length changes. One line represents one file. Average quality refers to the mean of all PHRED scores of a single sequence.

<dl>
  <dt><em>read</em></dt>
  <dd>Read type of the sequences (R1, R2, merged)</dd>
  <dt><em>minReadLenChange</em></dt>
  <dd>Smallest sequence length change</dd>
  <dt><em>quantile25ReadLenChange</em></dt>
  <dd>25th percentile of all read length changes</dd>
  <dt><em>medianReadLenChange</em></dt>
  <dd>50th percentile of all read length changes</dd>
  <dt><em>quantile75ReadLenChange</em></dt>
  <dd>75th percentile of all read length changes</dd>
  <dt><em>maxReadLenChange</em></dt>
  <dd>Biggest sequence length change</dd>
  <dt><em>meanReadLenChange</em></dt>
  <dd>Average sequence length change</dd>
  <dt><em>minAvgQualChange</em></dt>
  <dd>Smallest change of the average sequence quality</dd>
  <dt><em>quantile25AvgQualChange</em></dt>
  <dd>25th percentile of all average quality changes</dd>
  <dt><em>medianAvgQualChange</em></dt>
  <dd>50th percentile of all average quality changes</dd>
  <dt><em>quantile75AvgQualChange</em></dt>
  <dd>75th percentile of all average quality changes</dd>
  <dt><em>maxAvgQualChange</em></dt>
  <dd>Biggest change of average quality</dd>
  <dt><em>meanAvgQualChange</em></dt>
  <dd>Mean change of average read qualities</dd>
  <dt><em>newentries</em></dt>
  <dd>ID count not in the first file (should be 0)</dd>
  <dt><em>deletedentries</em></dt>
  <dd>ID count no longer in the second file</dd>
  <dt><em>matchingentries</em></dt>
  <dd>Number of IDs found in both files</dd>
</dl>
