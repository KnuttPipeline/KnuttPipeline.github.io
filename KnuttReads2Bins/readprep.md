---
layout: default
title: Read preparation
nav_order: 1
permalink: /KnuttReads2Bins/ReadPrep
parent: KnuttReads2Bins
has_toc: false
---
## Read Preparation
{: .no_toc}

Before analysing the reads, certain **technical artifacts** have to be removed. Depending on the input data source, sequencing adapters may need to be **trimmed**, overlapping reads **merged** and for some steps **quality trimming** necessary.

The steps for this stage are defined in the [`Snakefile_1PrepareReads`](https://github.com/KnuttPipeline/KnuttReads2Bins/blob/master/Snakefile_1PrepareReads) file. **No reference data** is required.

<dl>
  <dt><em>Small metagenome</em></dt>
  <dd>Tbd. GB</dd>
  <dt><em>Medium metagenome</em></dt>
  <dd>Tbd. GB</dd>
  <dt><em>Large metagenome</em></dt>
  <dd>Tbd. GB</dd>
</dl>

All preparation steps can be run with the `prepareReads` and `prepareReadsReport` rules.

Outputs are stored in `<outputdir>/ReadPrep/`, `<outputdir>/Data/ReadPrep/` and ``<outputdir>/Reports/``.

Used tools are [cutadapt](https://cutadapt.readthedocs.io/en/latest/guide.html), [BBmerge](https://jgi.doe.gov/data-and-tools/bbtools/bb-tools-user-guide/bbmerge-guide/) & [BBMask](https://jgi.doe.gov/data-and-tools/bbtools/bb-tools-user-guide/bbmask-guide/), [khmer trim-low-abund.py](https://khmer.readthedocs.io/en/v2.1.2/user/scripts.html#trim-low-abund-py) and [seqtk](https://github.com/lh3/seqtk). R libraries for data processing include data.table, seqinr, plotly, ggplot, pbapply, flexdashboard and shortreads. **Check the environment (`envs/`) files for more details**.

---

#### Table of contents
{: .no_toc }

1. TOC
{:toc}

---

### Raw sequencing data

**Sequence data files** for all samples can be produced with the rule `rawSeqData` and FASTQC reports with `rawFASTQC`. A report on the user provided files can be created with `rawReport`.

#### Output files
{: .no_toc }
``` tree
output
├── output/Benchmarking
│   └── output/Benchmarking/ReadPrep
│       ├── output/Benchmarking/ReadPrep/raw_fastqc_SAMPLE_R1.tsv
│       ├── output/Benchmarking/ReadPrep/raw_fastqc_SAMPLE_R2.tsv
│       ├── output/Benchmarking/ReadPrep/raw_report.tsv
│       ├── output/Benchmarking/ReadPrep/raw_seqdata_SAMPLE_R1.tsv
│       └── output/Benchmarking/ReadPrep/raw_seqdata_SAMPLE_R2.tsv
├── output/Data
│   └── output/Data/ReadPrep
│       └── output/Data/ReadPrep/RawSequenceData
│           ├── output/Data/ReadPrep/RawSequenceData/SAMPLE_raw_seqdat_overview.tsv
│           └── output/Data/ReadPrep/RawSequenceData/SAMPLE_raw_seqdat_plotdata.tsv
└── output/Reports
    ├── output/Reports/1raw-reads.html
    └── output/Reports/FastQC
        ├── output/Reports/FastQC/raw_SAMPLE_R1_fastqc.html
        ├── output/Reports/FastQC/raw_SAMPLE_R1_fastqc.zip
        ├── output/Reports/FastQC/raw_SAMPLE_R2_fastqc.html
        └── output/Reports/FastQC/raw_SAMPLE_R2_fastqc.zip
```

See the general file information for the [`Benchmarking`](/shared_files#benchmark-files) and [`seqdat`](/shared_files#sequence-data-files) files.

---

### Adapter trimming

Even if you disable adapter trimming in the configuration file, **it is still run** to generate data, that should show trimming wasn't necessary. Trimming is performed with the `trim` rule. Sequence data files for the trimmed files are produced with `trimSeqData` and the FASTQC reports with `trimFASTQC`.

The trimming report (`trimReport`) uses **data from the merge operation**, therefore it is included in the next section on merging.

#### Output files
{: .no_toc }

``` tree
output
├── output/Benchmarking
│   └── output/Benchmarking/ReadPrep
│       ├── output/Benchmarking/ReadPrep/trim_impact_SAMPLE_R1.tsv
│       ├── output/Benchmarking/ReadPrep/trim_impact_SAMPLE_R2.tsv
│       ├── output/Benchmarking/ReadPrep/trim_SAMPLE.tsv
│       ├── output/Benchmarking/ReadPrep/trim_seqdata_SAMPLE_R1.tsv
│       └── output/Benchmarking/ReadPrep/trim_seqdata_SAMPLE_R2.tsv
├── output/Data
│   └── output/Data/ReadPrep
│       └── output/Data/ReadPrep/AdapterTrimmed
│           ├── output/Data/ReadPrep/AdapterTrimmed/SAMPLE_adptr_tr_impact.tsv
│           ├── output/Data/ReadPrep/AdapterTrimmed/SAMPLE_adptr_tr_seqdat_overview.tsv
│           ├── output/Data/ReadPrep/AdapterTrimmed/SAMPLE_adptr_tr_seqdat_plotdata.tsv
│           └── output/Data/ReadPrep/AdapterTrimmed/SAMPLE_adptr_tr.tsv
├── output/ReadPrep
│   └── output/ReadPrep/AdapterTrimmed
│       └── output/ReadPrep/AdapterTrimmed/SAMPLE
│           ├── output/ReadPrep/AdapterTrimmed/SAMPLE/SAMPLE_R1_adptr_tr.fastq.gz
│           └── output/ReadPrep/AdapterTrimmed/SAMPLE/SAMPLE_R2_adptr_tr.fastq.gz
└── output/Reports
    └── output/Reports/FastQC
        ├── output/Reports/FastQC/trim_SAMPLE_R1_fastqc.html
        └── output/Reports/FastQC/trim_SAMPLE_R2_fastqc.html
```

See the general file information for the [`Benchmarking`](/shared_files#benchmark-files), [`impact`](/shared_files#sequence-data-comparison-files) and [`seqdat`](/shared_files#sequence-data-files) files.

The `SAMPLE_adptr_tr.tsv` file is the [minimal report](https://cutadapt.readthedocs.io/en/stable/guide.html#reporting) produced by cutadapt.

<dl>
  <dt><em>status</em></dt>
  <dd>Incomplete Adapter warning, almost always <em>OK</em></dd>
  <dt><em>in_reads</em></dt>
  <dd>Number of reads in input</dd>
  <dt><em>in_bp</em></dt>
  <dd>Base pairs (R1+R2) in input</dd>
  <dt><em>too_short</em></dt>
  <dd>Reads discarded for being too short</dd>
  <dt><em>too_long</em></dt>
  <dd>Reads discarded for being too long</dd>
  <dt><em>too_many_n</em></dt>
  <dd>Reads discarded for containing too many unspecified base calls</dd>
  <dt><em>out_reads</em></dt>
  <dd>Reads written</dd>
  <dt><em>w/adapters</em></dt>
  <dd>R1 reads adapter trimmed</dd>
  <dt><em>qualtrim_bp</em></dt>
  <dd>R1 base pairs quality trimmed</dd>
  <dt><em>out_bp</em></dt>
  <dd>R1 base pairs written</dd>
  <dt><em>w/adapters2</em></dt>
  <dd>R2 reads adapter trimmed</dd>
  <dt><em>qualtrim2_bp</em></dt>
  <dd>R2 base pairs quality trimmed</dd>
  <dt><em>out2_bp</em></dt>
  <dd>R2 base pairs written</dd>
</dl>

### Read merging

As the R1 and R2 reads from paired end sequencing **may** overlap, read merging is performed with BBmerge. Merging for all samples can be performed with `merge`. Sequence data is generated with `mergeSeqData` and FASTQC is run with `mergeFASTQC`. The trim report (`trimReport`) and merge report (`mergeReport`) both always use untrimmed and trimmed merging results for analysis.

#### Output files
{: .no_toc }

``` tree
output
├── output/Benchmarking
│   └── output/Benchmarking/ReadPrep
│       ├── output/Benchmarking/ReadPrep/merge_report.tsv
│       ├── output/Benchmarking/ReadPrep/merge_seqdata_SAMPLE_tr_merged.tsv
│       ├── output/Benchmarking/ReadPrep/merge_seqdata_SAMPLE_tr_unmgd_R1.tsv
│       ├── output/Benchmarking/ReadPrep/merge_seqdata_SAMPLE_tr_unmgd_R2.tsv
│       ├── output/Benchmarking/ReadPrep/merge_seqdata_SAMPLE_untr_merged.tsv
│       ├── output/Benchmarking/ReadPrep/merge_seqdata_SAMPLE_untr_unmgd_R1.tsv
│       ├── output/Benchmarking/ReadPrep/merge_seqdata_SAMPLE_untr_unmgd_R2.tsv
│       ├── output/Benchmarking/ReadPrep/merging_tr_SAMPLE.tsv
│       └── output/Benchmarking/ReadPrep/merging_untr_SAMPLE.tsv
├── output/Data
│   └── output/Data/ReadPrep
│       ├── output/Data/ReadPrep/Merging_tr
│       │   ├── output/Data/ReadPrep/Merging_tr/SAMPLE_merge_tr_insert_sizes.tsv
│       │   ├── output/Data/ReadPrep/Merging_tr/SAMPLE_merge_tr_overview.tsv
│       │   ├── output/Data/ReadPrep/Merging_tr/SAMPLE_merge_tr_seqdat_overview.tsv
│       │   └── output/Data/ReadPrep/Merging_tr/SAMPLE_merge_tr_seqdat_plotdata.tsv
│       └── output/Data/ReadPrep/Merging_untr
│           ├── output/Data/ReadPrep/Merging_untr/SAMPLE_merge_untr_insert_sizes.tsv
│           ├── output/Data/ReadPrep/Merging_untr/SAMPLE_merge_untr_overview.tsv
│           ├── output/Data/ReadPrep/Merging_untr/SAMPLE_merge_untr_seqdat_overview.tsv
│           └── output/Data/ReadPrep/Merging_untr/SAMPLE_merge_untr_seqdat_plotdata.tsv
├── output/ReadPrep
│   ├── output/ReadPrep/Merging_tr
│   │   └── output/ReadPrep/Merging_tr/SAMPLE
│   │       ├── output/ReadPrep/Merging_tr/SAMPLE/SAMPLE_merge_tr_adapters.fa
│   │       ├── output/ReadPrep/Merging_tr/SAMPLE/SAMPLE_merge_tr_merged.fastq.gz
│   │       ├── output/ReadPrep/Merging_tr/SAMPLE/SAMPLE_merge_tr_merge.log
│   │       ├── output/ReadPrep/Merging_tr/SAMPLE/SAMPLE_merge_tr_unmgd_R1.fastq.gz
│   │       └── output/ReadPrep/Merging_tr/SAMPLE/SAMPLE_merge_tr_unmgd_R2.fastq.gz
│   └── output/ReadPrep/Merging_untr
│       └── output/ReadPrep/Merging_untr/SAMPLE
│           ├── output/ReadPrep/Merging_untr/SAMPLE/SAMPLE_merge_untr_adapters.fa
│           ├── output/ReadPrep/Merging_untr/SAMPLE/SAMPLE_merge_untr_merged.fastq.gz
│           ├── output/ReadPrep/Merging_untr/SAMPLE/SAMPLE_merge_untr_merge.log
│           ├── output/ReadPrep/Merging_untr/SAMPLE/SAMPLE_merge_untr_unmgd_R1.fastq.gz
│           └── output/ReadPrep/Merging_untr/SAMPLE/SAMPLE_merge_untr_unmgd_R2.fastq.gz
└── output/Reports
    ├── output/Reports/2trimming.html
    ├── output/Reports/3read-merging.html
    └── output/Reports/FastQC
        ├── output/Reports/FastQC/merge_tr_SAMPLE_merged_fastqc.html
        ├── output/Reports/FastQC/merge_tr_SAMPLE_unmgd_R1_fastqc.html
        ├── output/Reports/FastQC/merge_tr_SAMPLE_unmgd_R2_fastqc.html
        ├── output/Reports/FastQC/merge_untr_SAMPLE_merged_fastqc.html
        ├── output/Reports/FastQC/merge_untr_SAMPLE_unmgd_R1_fastqc.html
        └── output/Reports/FastQC/merge_untr_SAMPLE_unmgd_R2_fastqc.html
```

See the general file information for the [`Benchmarking`](/shared_files#benchmark-files) and [`seqdat`](/shared_files#sequence-data-files) files.

The `SAMPLE_merge_{untr/tr}_insert_sizes.tsv` files are directly produced by BBmerge. One line represents one read pair.


<dl>
  <dt><em>#id</em></dt>
  <dd>Sequence identifier as seen in the FASTQ file</dd>
  <dt><em>numericID</em></dt>
  <dd>Positional index of the sequence</dd>
  <dt><em>insert</em></dt>
  <dd>Length of the new sequence resulting from the merge, -1 for failed merges</dd>
  <dt><em>status</em></dt>
  <dd>Character showing the merging success, can be F(ailed), I(mperfect) or P(erfect)</dd>
  <dt><em>mismatches</em></dt>
  <dd>The number of mismatches during merging. 0 for perfect merges and missing for failed merges</dd>
</dl>

For the `SAMPLE_merge_{untr/tr}_overview.tsv` file the BBmerge log output is parsed. The adapter output can be used to make sure that adapter trimming was successful. This is done by making sure the `adaptercount` field has a **low value** (compared to the joined/merged count) and the adapter sequence is long, consisting mostly of N's.

<dl>
  <dt><em>pairstomerge</em></dt>
  <dd>Read pairs read from the FASTQ files</dd>
  <dt><em>joined</em></dt>
  <dd>Number of pairs merged</dd>
  <dt><em>ambigous</em></dt>
  <dd>Not merged pairs, because multiple different solutions were likely</dd>
  <dt><em>nosolution</em></dt>
  <dd>Not mergeable pairs</dd>
  <dt><em>tooshort</em></dt>
  <dd>Rejected pairs, because the resulting sequence would be too short</dd>
  <dt><em>shortestinsert</em></dt>
  <dd>Shortest sequence length produced by merging</dd>
  <dt><em>medianinsert</em></dt>
  <dd>Median sequence length produced by merging</dd>
  <dt><em>longestinsert</em></dt>
  <dd>Longest sequence length produced by merging</dd>
  <dt><em>adaptercount</em></dt>
  <dd>Number of merged reads where a adapter read-through is expected</dd>
  <dt><em>Read1_adapter</em></dt>
  <dd>Conensus sequence of the suspected adapter sequence for R1</dd>
  <dt><em>Read2_adapter</em></dt>
  <dd>Conensus sequence of the suspected adapter sequence for R2</dd>
</dl>

### Read quality trimming

For some steps like **read classification** and **direct database searches** it makes sense to mask low complexity and low quality regions of the reads, as not all tools are quality aware. The **merged and unmerged R1 reads are combined** for these steps, because read pairings is another information some tools can't process and R1+R2 hits on the same subject may not be correctlly accounted for. K-mer based error trimming using `trim-low-abund.py` from khmer is performed **after** masking and quality scored based trimming.

If samples with different depths are compared using read analysis methods, **sampling (rarefaction)** of the reads can compensate for this difference. This sampling is configurable in the config file.

The quality trimming (and optional sampling) can be performed with the `analysisReads`, the sequence data files are produced with `analysisReadsSeqData` and the FASTQC reports with `analysisReadsFASTQC`. The report can be generated with `analysisReadsReport`.

``` tree
output
├── output/Benchmarking
│   └── output/Benchmarking/ReadPrep
│       ├── output/Benchmarking/ReadPrep/analysis_tr_unsmpld_seqdata_SAMPLE.tsv
│       ├── output/Benchmarking/ReadPrep/kmertr_tr_SAMPLE_merged.tsv
│       ├── output/Benchmarking/ReadPrep/kmertr_tr_SAMPLE_unmgd_R1.tsv
│       ├── output/Benchmarking/ReadPrep/qtr_tr_SAMPLE_merged.tsv
│       ├── output/Benchmarking/ReadPrep/qtr_tr_SAMPLE_unmgd_R1.tsv
│       ├── output/Benchmarking/ReadPrep/qtr_tr_impact_SAMPLE_merged.tsv
│       ├── output/Benchmarking/ReadPrep/qtr_tr_impact_SAMPLE_unmgd_R1.tsv
│       ├── output/Benchmarking/ReadPrep/qtr_tr_seqdata_SAMPLE_tr_merged.tsv
│       ├── output/Benchmarking/ReadPrep/qtr_tr_seqdata_SAMPLE_tr_unmgd_R1.tsv
│       └── output/Benchmarking/ReadPrep/read_ana_report.tsv
├── output/Data
│   └── output/Data/ReadPrep
│       ├── output/Data/ReadPrep/AdapterTrimmed
│       │   └── output/Data/ReadPrep/AdapterTrimmed/SAMPLE_adptr_tr.tsv
│       ├── output/Data/ReadPrep/AnalysisReads_tr
│       │   ├── output/Data/ReadPrep/AnalysisReads_tr/SAMPLE_analysis_tr_unsmpld_seqdat_overview.tsv
│       │   └── output/Data/ReadPrep/AnalysisReads_tr/SAMPLE_analysis_tr_unsmpld_seqdat_plotdata.tsv
│       └── output/Data/ReadPrep/Merging_tr
│           ├── output/Data/ReadPrep/Merging_tr/SAMPLE_merge_tr_insert_sizes.tsv
│           ├── output/Data/ReadPrep/Merging_tr/SAMPLE_qtr_tr_impact.tsv
│           ├── output/Data/ReadPrep/Merging_tr/SAMPLE_qtr_tr_seqdat_overview.tsv
│           ├── output/Data/ReadPrep/Merging_tr/SAMPLE_qtr_tr_seqdat_plotdata.tsv
│           └── output/Data/ReadPrep/Merging_tr/SAMPLE_qtr_tr.tsv
├── output/ReadPrep
│   ├── output/ReadPrep/AnalysisReads_tr
│   │   └── output/ReadPrep/AnalysisReads_tr/SAMPLE
│   │       ├── output/ReadPrep/AnalysisReads_tr/SAMPLE/SAMPLE_analysis_tr_unsmpld_concat.log
│   │       └── output/ReadPrep/AnalysisReads_tr/SAMPLE/SAMPLE_analysis_tr_unsmpld.fastq.gz
│   └── output/ReadPrep/Merging_tr
│       └── output/ReadPrep/Merging_tr/SAMPLE
│           ├── output/ReadPrep/Merging_tr/SAMPLE/SAMPLE_merge_tr_merged_mask.log
│           ├── output/ReadPrep/Merging_tr/SAMPLE/SAMPLE_merge_tr_merged_qtr_kmertr.fastq.gz
│           ├── output/ReadPrep/Merging_tr/SAMPLE/SAMPLE_merge_tr_merged_qtr_kmertr.log
│           ├── output/ReadPrep/Merging_tr/SAMPLE/SAMPLE_merge_tr_unmgd_R1_mask.log
│           ├── output/ReadPrep/Merging_tr/SAMPLE/SAMPLE_merge_tr_unmgd_R1_qtr_kmertr.fastq.gz
│           └── output/ReadPrep/Merging_tr/SAMPLE/SAMPLE_merge_tr_unmgd_R1_qtr_kmertr.log
└── output/Reports
    ├── output/Reports/4read-ana-prep.html
    └── output/Reports/FastQC
        ├── output/Reports/FastQC/class_tr_unsmpld_SAMPLE_fastqc.html
        ├── output/Reports/FastQC/qtr_tr_SAMPLE_merged_fastqc.html
        └── output/Reports/FastQC/qtr_tr_SAMPLE_unmgd_R1_fastqc.html
```

See the general file information for the [`Benchmarking`](/shared_files#benchmark-files), [`impact`](/shared_files#sequence-data-comparison-files) and [`seqdat`](/shared_files#sequence-data-files) files.

The `SAMPLE_qtr_tr.tsv` file is also the [minimal report](https://cutadapt.readthedocs.io/en/stable/guide.html#reporting), but the reads are processed as unpaired reads, therefore there are no R2 values. BBmask converts the low complexity regions to unspecified base calls before cutadapt is called. Also included is data from the masking and k-mer trimmming process.

<dl>
  <dt><em>status</em></dt>
  <dd>Incomplete Adapter warning, almost always <em>OK</em></dd>
  <dt><em>in_reads</em></dt>
  <dd>Number of reads in input</dd>
  <dt><em>in_bp</em></dt>
  <dd>Base pairs in input</dd>
  <dt><em>too_short</em></dt>
  <dd>Reads discarded for being too short</dd>
  <dt><em>too_long</em></dt>
  <dd>Reads discarded for being too long</dd>
  <dt><em>too_many_n</em></dt>
  <dd>Reads discarded for containing too many unspecified base calls</dd>
  <dt><em>out_reads</em></dt>
  <dd>Reads written</dd>
  <dt><em>w/adapters</em></dt>
  <dd>Reads adapter trimmed, should be 0</dd>
  <dt><em>qualtrim_bp</em></dt>
  <dd>Base pairs quality trimmed</dd>
  <dt><em>out_bp</em></dt>
  <dd>Base pairs written</dd>
  <dt><em>total_masked</em></dt>
  <dd>Base pairs masked by BBmask</dd>
  <dt><em>fpr</em></dt>
  <dd>False positive rate estimation for k-mer trimming</dd>
  <dt><em>reads_skipped</em></dt>
  <dd>Low abundance reads skipped for k-mer trimming</dd>
  <dt><em>basepairs_skipped</em></dt>
  <dd>Low abundance base pairs skipped for k-mer trimming</dd>
  <dt><em>reads_removed</em></dt>
  <dd>Entire reads removed due to k-mer trimming</dd>
  <dt><em>reads_trimmed</em></dt>
  <dd>Number of reads k-mer trimmed</dd>
  <dt><em>basepairs_removed_or_trimmed</em></dt>
  <dd>Base pairs remvoved by k-mer trimming</dd>
</dl>