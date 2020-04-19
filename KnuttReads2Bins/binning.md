---
layout: default
title: Binning
nav_order: 5
permalink: /KnuttReads2Bins/Binning
parent: KnuttReads2Bins
has_toc: false
---
## Binning
{: .no_toc}

The final step of the **KnuttReads2Bins** pipeline, the binning of the contigs into MAGs/bins is executed using **METABAT2** in the [`Snakefile_5Binning`](https://github.com/KnuttPipeline/KnuttReads2Bins/blob/master/Snakefile_5Binning) file.

<dl>
  <dt><em>Small metagenome</em></dt>
  <dd>Tbd. GB</dd>
  <dt><em>Medium metagenome</em></dt>
  <dd>Tbd. GB</dd>
  <dt><em>Large metagenome</em></dt>
  <dd>Tbd. GB</dd>
</dl>

The `binning` rule executes **METABAT** and **CheckM**. The bins can be also classified with **Sourmash** (`binningSourmash`) and **BAT** (`bat`). BAT uses the classification of the contigs from CAT. The **taxonomic composition for the bins** based on the CAT classification can be generated with the `batKrona` rule.
A report is available with `binningReport`.

If you are interested in the relationship of your bins to published genomes/bins checkout **KnuttBinPhylo**.

---

#### Output files
{: .no_toc }

``` tree
output
├── output/Benchmarking
│   └── output/Benchmarking/Binning
│       ├── output/Benchmarking/Binning/bat_SAMPLE.tsv
│       ├── output/Benchmarking/Binning/binning_report.tsv
│       ├── output/Benchmarking/Binning/checkm_extra_SAMPLE.tsv
│       ├── output/Benchmarking/Binning/checkm_wf_SAMPLE.tsv
│       ├── output/Benchmarking/Binning/metabat_SAMPLE.tsv
│       ├── output/Benchmarking/Binning/sourmash_bins_description_SAMPLE.tsv
│       ├── output/Benchmarking/Binning/sourmash_bins_k51_SAMPLE.tsv
│       └── output/Benchmarking/Binning/sourmash_classification_SAMPLE.tsv
├── output/Binning
│   ├── output/Binning/BAT
│   │   └── output/Binning/BAT/SAMPLE
│   │       ├── output/Binning/BAT/SAMPLE/SAMPLE_BAT.log
│   │       ├── output/Binning/BAT/SAMPLE/SAMPLE.bin2classification.named.txt
│   │       ├── output/Binning/BAT/SAMPLE/SAMPLE.bin2classification.txt
│   │       ├── output/Binning/BAT/SAMPLE/SAMPLE.bins.alignment.diamond
│   │       ├── output/Binning/BAT/SAMPLE/SAMPLE.bins.predicted_proteins.faa
│   │       ├── output/Binning/BAT/SAMPLE/SAMPLE.log
│   │       └── output/Binning/BAT/SAMPLE/SAMPLE.ORF2LCA.txt
│   ├── output/Binning/CheckM
│   │   └── output/Binning/CheckM/SAMPLE
│   │       ├── output/Binning/CheckM/SAMPLE/SAMPLE_checkm_data
│   │       │   ├── output/Binning/CheckM/SAMPLE/SAMPLE_checkm_data/bins
│   │       │   │   └── output/Binning/CheckM/SAMPLE/SAMPLE_checkm_data/bins/SAMPLE.X
│   │       │   │       └── ...
│   │       │   ├── output/Binning/CheckM/SAMPLE/SAMPLE_checkm_data/checkm.log
│   │       │   ├── output/Binning/CheckM/SAMPLE/SAMPLE_checkm_data/lineage.ms
│   │       │   └── output/Binning/CheckM/SAMPLE/SAMPLE_checkm_data/storage
│   │       │       └── ...
│   │       ├── output/Binning/CheckM/SAMPLE/SAMPLE_checkm_helperfiles.log
│   │       └── output/Binning/CheckM/SAMPLE/SAMPLE_checkm_wf.log
│   ├── output/Binning/METABAT
│   │   └── output/Binning/METABAT/SAMPLE
│   │       ├── output/Binning/METABAT/SAMPLE/bins
│   │       │   ├── output/Binning/METABAT/SAMPLE/bins/SAMPLE.X.fa
│   │       │   └── ...
│   │       ├── output/Binning/METABAT/SAMPLE/SAMPLE.lowDepth.fa
│   │       ├── output/Binning/METABAT/SAMPLE/SAMPLE_metabat2.log
│   │       ├── output/Binning/METABAT/SAMPLE/SAMPLE.tooShort.fa
│   │       └── output/Binning/METABAT/SAMPLE/SAMPLE.unbinned.fa
│   └── output/Binning/sourmash
│       └── output/Binning/sourmash/SAMPLE
│           ├── output/Binning/sourmash/SAMPLE/SAMPLE_bins_sourmash_classification.log
│           ├── output/Binning/sourmash/SAMPLE/SAMPLE_bins_sourmash_descr.log
│           ├── output/Binning/sourmash/SAMPLE/SAMPLE_bins_sourmash_k51.log
│           └── output/Binning/sourmash/SAMPLE/SAMPLE_bins_sourmash_k51.sig
├── output/Data
│   └── output/Data/Binning
│       ├── output/Data/Binning/SAMPLE_bat.tsv
│       ├── output/Data/Binning/SAMPLE_binmap.tsv
│       ├── output/Data/Binning/SAMPLE_bins_sourmash_classification.tsv
│       ├── output/Data/Binning/SAMPLE_bins_sourmash_signature.tsv
│       ├── output/Data/Binning/SAMPLE_checkm_cov.tsv
│       ├── output/Data/Binning/SAMPLE_checkm_profile.tsv
│       ├── output/Data/Binning/SAMPLE_checkm_tetras.tsv
│       └── output/Data/Binning/^.tsv
└── output/Reports
    ├── output/Reports/8binning.html
    └── output/Reports/BAT_krona.html
```

See the general file information for the [`Benchmarking`](/shared_files#benchmark-files) files.

The file `SAMPLE_bat.tsv` uses the same format (`bin` instead of `contig`) as the CAT files. One important difference is that **one bin can have multiple classifications**.

If you need it, `SAMPLE_binmap.tsv` has one column with the bins and **reject files** and a second column with the contig names in it.

The `SAMPLE_bins_sourmash_signature.tsv` has the same format as the other signature description files. `SAMPLE_bins_sourmash_classification.tsv` gives the sourmash classification for every bin:

<dl>
  <dt><em>bin</em></dt>
  <dd>The bin name</dd>
  <dt><em>status</em></dt>
  <dd><em>nomatch</em> or <em>found</em></dd>
  <dt>Tax columns</dt>
  <dd>The assigned taxonomy</dd>
</dl>

`SAMPLE_checkm_cov.tsv` is the third coverage file for the contigs this pipeline provides based on the same BAM file. It is used by CheckM for its profile calculations. Just six columns are in it:

<dl>
  <dt><em>contigid</em></dt>
  <dd>The short name of the contig</dd>
  <dt><em>bin</em></dt>
  <dd>The bin name</dd>
  <dt><em>len</em></dt>
  <dd>Contig lengths</dd>
  <dt><em>bamfile</em></dt>
  <dd>BAM file name</dd>
  <dt><em>avgcov</em></dt>
  <dd>Average coverage of this contig</dd>
  <dt><em>reads</em></dt>
  <dd>Read count mapped to this contig</dd>
</dl>

This profile is written to the `SAMPLE_checkm_profile.tsv` file. The bin `unbinned` also contains the contigs which were too short or had not enough coverage.

<dl>
  <dt><em>bin</em></dt>
  <dd>The bin name</dd>
  <dt><em>binsize_Mbp</em></dt>
  <dd>Size of the bin in Mega-base-pairs</dd>
  <dt><em>reads</em></dt>
  <dd>Reads mapped to this bin</dd>
  <dt><em>reads_perc</em></dt>
  <dd>Percentage of reads mapped to this bin</dd>
  <dt><em>ofbinnedpop_perc</em></dt>
  <dd>How much of the successfully binned population does this bin represent</dd>
  <dt><em>ofcommunity_perc</em></dt>
  <dd>Read count mapped to this contig</dd>
</dl>

Mainly for educational purposes a file giving the tetra nulceotide frequencies for every contig (`SAMPLE_checkm_tetras.tsv`) is available.

The well known CheckM lineage workflow data is placed in `SAMPLE_checkm.tsv`:

<dl>
  <dt><em>bin</em></dt>
  <dd>The bin name</dd>
  <dt><em>marker_lineage</em></dt>
  <dd>The marker set set selected by phylogenetic placement</dd>
  <dt><em>lineage_genomes</em></dt>
  <dd>Reference genomes which were placed on that phylogenetic ode</dd>
  <dt><em>lineage_markers</em></dt>
  <dd>Markers in this set of marker sets</dd>
  <dt><em>lineage_marker_sets</em></dt>
  <dd>Colocated marker sets used for this node</dd>
  <dt><em>X_sets</em></dt>
  <dd>Number of times (X) each marker was identified</dd>
  <dt><em>completeness</em></dt>
  <dd>Completion based on the markers</dd>
  <dt><em>contaimination</em></dt>
  <dd>Contamination based on the markers</dd>
  <dt><em>strain_heterogeneity</em></dt>
  <dd>Percentage of the contamination caused by very similar marker genes</dd>
</dl>